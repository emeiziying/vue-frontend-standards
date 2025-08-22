# Vue Composables 自定义组合函数规范

## 概述

Composables（组合函数）是Vue 3 Composition API的核心特性，用于封装和复用有状态逻辑。通过自定义hooks，我们可以将复杂的业务逻辑抽象成可复用的函数。

## 命名规范

### 函数命名
```typescript
// ✅ 推荐 - 以 use 开头的驼峰命名
export const useCounter = () => { }
export const useAsyncData = () => { }
export const useLocalStorage = () => { }
export const useDebounce = () => { }

// ❌ 不推荐
export const counter = () => { }
export const asyncData = () => { }
export const getLocalStorage = () => { }
```

### 文件命名
```
composables/
├── useAuth.ts          # 认证相关
├── useApi.ts           # API请求
├── useLocalStorage.ts  # 本地存储
├── useDebounce.ts      # 防抖处理
└── useThrottle.ts      # 节流处理
```

## 基础Composables

### 1. 状态管理类

#### useToggle - 布尔状态切换
```typescript
// composables/useToggle.ts
import { ref, Ref } from 'vue'

export const useToggle = (initialValue = false): [
  Ref<boolean>,
  (value?: boolean) => void
] => {
  const state = ref(initialValue)
  
  const toggle = (value?: boolean) => {
    state.value = typeof value === 'boolean' ? value : !state.value
  }
  
  return [state, toggle]
}

// 使用示例
const [isVisible, toggleVisible] = useToggle(false)
toggleVisible() // 切换状态
toggleVisible(true) // 设置为true
```

#### useCounter - 计数器
```typescript
// composables/useCounter.ts
import { ref, computed } from 'vue'

interface UseCounterOptions {
  min?: number
  max?: number
  step?: number
}

export const useCounter = (
  initialValue = 0,
  options: UseCounterOptions = {}
) => {
  const { min = -Infinity, max = Infinity, step = 1 } = options
  
  const count = ref(initialValue)
  
  const increment = () => {
    if (count.value < max) {
      count.value += step
    }
  }
  
  const decrement = () => {
    if (count.value > min) {
      count.value -= step
    }
  }
  
  const reset = () => {
    count.value = initialValue
  }
  
  const set = (value: number) => {
    count.value = Math.max(min, Math.min(max, value))
  }
  
  const isMin = computed(() => count.value <= min)
  const isMax = computed(() => count.value >= max)
  
  return {
    count: readonly(count),
    increment,
    decrement,
    reset,
    set,
    isMin,
    isMax
  }
}
```

### 2. 异步数据处理

#### useAsyncData - 异步数据获取
```typescript
// composables/useAsyncData.ts
import { ref, Ref } from 'vue'

interface UseAsyncDataOptions<T> {
  immediate?: boolean
  initialData?: T
  onSuccess?: (data: T) => void
  onError?: (error: Error) => void
}

export const useAsyncData = <T>(
  fetcher: () => Promise<T>,
  options: UseAsyncDataOptions<T> = {}
) => {
  const {
    immediate = true,
    initialData,
    onSuccess,
    onError
  } = options
  
  const data = ref<T | undefined>(initialData)
  const loading = ref(false)
  const error = ref<Error | null>(null)
  
  const execute = async () => {
    try {
      loading.value = true
      error.value = null
      
      const result = await fetcher()
      data.value = result
      
      onSuccess?.(result)
      return result
    } catch (err) {
      const errorObj = err instanceof Error ? err : new Error(String(err))
      error.value = errorObj
      onError?.(errorObj)
      throw errorObj
    } finally {
      loading.value = false
    }
  }
  
  const refresh = () => execute()
  
  if (immediate) {
    execute()
  }
  
  return {
    data: readonly(data) as Readonly<Ref<T | undefined>>,
    loading: readonly(loading),
    error: readonly(error),
    execute,
    refresh
  }
}

// 使用示例
const { data: users, loading, error, refresh } = useAsyncData(
  () => userApi.getUsers(),
  {
    onSuccess: (users) => console.log('获取用户成功:', users.length),
    onError: (error) => console.error('获取用户失败:', error)
  }
)
```

### 3. 本地存储

#### useLocalStorage - 本地存储同步
```typescript
// composables/useLocalStorage.ts
import { ref, watch, Ref } from 'vue'

export const useLocalStorage = <T>(
  key: string,
  defaultValue: T,
  options: {
    serializer?: {
      read: (value: string) => T
      write: (value: T) => string
    }
  } = {}
): [Ref<T>, (value: T) => void, () => void] => {
  const {
    serializer = {
      read: JSON.parse,
      write: JSON.stringify
    }
  } = options
  
  const storedValue = localStorage.getItem(key)
  const initialValue = storedValue !== null 
    ? serializer.read(storedValue)
    : defaultValue
  
  const state = ref<T>(initialValue)
  
  const setValue = (value: T) => {
    state.value = value
  }
  
  const removeValue = () => {
    localStorage.removeItem(key)
    state.value = defaultValue
  }
  
  // 监听状态变化，同步到localStorage
  watch(
    state,
    (newValue) => {
      if (newValue === undefined || newValue === null) {
        localStorage.removeItem(key)
      } else {
        localStorage.setItem(key, serializer.write(newValue))
      }
    },
    { deep: true }
  )
  
  // 监听storage事件，同步其他标签页的变化
  window.addEventListener('storage', (e) => {
    if (e.key === key && e.newValue !== null) {
      state.value = serializer.read(e.newValue)
    }
  })
  
  return [state, setValue, removeValue]
}

// 使用示例
const [theme, setTheme] = useLocalStorage('theme', 'light')
const [userPrefs, setUserPrefs] = useLocalStorage('userPrefs', {
  language: 'zh-CN',
  notifications: true
})
```

### 4. 防抖和节流

#### useDebounce - 防抖处理
```typescript
// composables/useDebounce.ts
import { ref, watch, Ref } from 'vue'

export const useDebounce = <T>(
  value: Ref<T>,
  delay: number
): Ref<T> => {
  const debouncedValue = ref<T>(value.value)
  
  watch(value, (newValue) => {
    const timer = setTimeout(() => {
      debouncedValue.value = newValue
    }, delay)
    
    return () => clearTimeout(timer)
  })
  
  return debouncedValue as Ref<T>
}

// 函数防抖版本
export const useDebounceFn = (
  fn: (...args: any[]) => any,
  delay: number
) => {
  let timer: number | null = null
  
  const debouncedFn = (...args: any[]) => {
    if (timer) clearTimeout(timer)
    timer = setTimeout(() => fn(...args), delay)
  }
  
  const cancel = () => {
    if (timer) {
      clearTimeout(timer)
      timer = null
    }
  }
  
  return { debouncedFn, cancel }
}

// 使用示例
const searchQuery = ref('')
const debouncedQuery = useDebounce(searchQuery, 300)

watch(debouncedQuery, (query) => {
  // 执行搜索
  performSearch(query)
})
```

#### useThrottle - 节流处理
```typescript
// composables/useThrottle.ts
import { ref, Ref } from 'vue'

export const useThrottle = <T>(
  value: Ref<T>,
  delay: number
): Ref<T> => {
  const throttledValue = ref<T>(value.value)
  let lastUpdate = 0
  
  watch(value, (newValue) => {
    const now = Date.now()
    if (now - lastUpdate >= delay) {
      throttledValue.value = newValue
      lastUpdate = now
    }
  })
  
  return throttledValue as Ref<T>
}

// 函数节流版本
export const useThrottleFn = (
  fn: (...args: any[]) => any,
  delay: number
) => {
  let lastCall = 0
  
  const throttledFn = (...args: any[]) => {
    const now = Date.now()
    if (now - lastCall >= delay) {
      lastCall = now
      return fn(...args)
    }
  }
  
  return throttledFn
}
```

## 业务逻辑Composables

### 1. 表单处理

#### useForm - 表单状态管理
```typescript
// composables/useForm.ts
import { ref, reactive, computed } from 'vue'

interface ValidationRule<T = any> {
  required?: boolean
  min?: number
  max?: number
  pattern?: RegExp
  validator?: (value: T) => boolean | string
}

interface FormField<T = any> {
  value: T
  rules?: ValidationRule<T>[]
  error?: string
}

export const useForm = <T extends Record<string, any>>(
  initialValues: T,
  validationRules: Partial<Record<keyof T, ValidationRule[]>> = {}
) => {
  const formData = reactive<T>({ ...initialValues })
  const errors = reactive<Partial<Record<keyof T, string>>>({})
  const touched = reactive<Partial<Record<keyof T, boolean>>>({})
  
  const validateField = (field: keyof T): boolean => {
    const rules = validationRules[field] || []
    const value = formData[field]
    
    for (const rule of rules) {
      if (rule.required && (!value || value === '')) {
        errors[field] = '此字段为必填项'
        return false
      }
      
      if (rule.min && value.length < rule.min) {
        errors[field] = `最少需要${rule.min}个字符`
        return false
      }
      
      if (rule.max && value.length > rule.max) {
        errors[field] = `最多允许${rule.max}个字符`
        return false
      }
      
      if (rule.pattern && !rule.pattern.test(value)) {
        errors[field] = '格式不正确'
        return false
      }
      
      if (rule.validator) {
        const result = rule.validator(value)
        if (result !== true) {
          errors[field] = typeof result === 'string' ? result : '验证失败'
          return false
        }
      }
    }
    
    delete errors[field]
    return true
  }
  
  const validateForm = (): boolean => {
    let isValid = true
    for (const field in validationRules) {
      if (!validateField(field)) {
        isValid = false
      }
    }
    return isValid
  }
  
  const resetForm = () => {
    Object.assign(formData, initialValues)
    Object.keys(errors).forEach(key => delete errors[key])
    Object.keys(touched).forEach(key => delete touched[key])
  }
  
  const setFieldValue = (field: keyof T, value: any) => {
    formData[field] = value
    touched[field] = true
    validateField(field)
  }
  
  const isValid = computed(() => Object.keys(errors).length === 0)
  const isDirty = computed(() => Object.keys(touched).length > 0)
  
  return {
    formData,
    errors: readonly(errors),
    touched: readonly(touched),
    isValid,
    isDirty,
    validateField,
    validateForm,
    resetForm,
    setFieldValue
  }
}
```

### 2. API请求管理

#### useApi - API请求封装
```typescript
// composables/useApi.ts
import { ref } from 'vue'

interface ApiOptions {
  immediate?: boolean
  onSuccess?: (data: any) => void
  onError?: (error: Error) => void
}

export const useApi = <T = any>(
  apiCall: (...args: any[]) => Promise<T>,
  options: ApiOptions = {}
) => {
  const { immediate = false, onSuccess, onError } = options
  
  const data = ref<T | null>(null)
  const loading = ref(false)
  const error = ref<Error | null>(null)
  
  const execute = async (...args: any[]): Promise<T | null> => {
    try {
      loading.value = true
      error.value = null
      
      const result = await apiCall(...args)
      data.value = result
      
      onSuccess?.(result)
      return result
    } catch (err) {
      const errorObj = err instanceof Error ? err : new Error(String(err))
      error.value = errorObj
      onError?.(errorObj)
      return null
    } finally {
      loading.value = false
    }
  }
  
  if (immediate) {
    execute()
  }
  
  return {
    data: readonly(data),
    loading: readonly(loading),
    error: readonly(error),
    execute
  }
}

// 使用示例
const { data: user, loading, error, execute: fetchUser } = useApi(
  (id: string) => userApi.getUser(id),
  {
    onSuccess: (user) => console.log('用户加载成功:', user.name),
    onError: (error) => console.error('用户加载失败:', error.message)
  }
)

// 手动触发请求
await fetchUser('123')
```

## 最佳实践

### 1. 返回值规范
```typescript
// ✅ 推荐 - 返回对象，便于解构和扩展
export const useCounter = () => {
  return {
    count,
    increment,
    decrement,
    reset
  }
}

// ✅ 推荐 - 返回数组，适用于简单场景
export const useToggle = () => {
  return [state, toggle] as const
}

// ❌ 不推荐 - 直接返回值
export const useCounter = () => count
```

### 2. 类型定义
```typescript
// ✅ 推荐 - 完整的类型定义
interface UseCounterReturn {
  count: Readonly<Ref<number>>
  increment: () => void
  decrement: () => void
  reset: () => void
}

export const useCounter = (): UseCounterReturn => {
  // 实现
}
```

### 3. 副作用清理
```typescript
// ✅ 推荐 - 自动清理副作用
export const useEventListener = (
  target: EventTarget,
  event: string,
  handler: EventListener
) => {
  onMounted(() => {
    target.addEventListener(event, handler)
  })
  
  onUnmounted(() => {
    target.removeEventListener(event, handler)
  })
}
```

### 4. 响应式数据保护
```typescript
// ✅ 推荐 - 使用 readonly 保护内部状态
export const useCounter = () => {
  const count = ref(0)
  
  return {
    count: readonly(count), // 防止外部直接修改
    increment: () => count.value++
  }
}
```

## 测试规范

### Composable测试示例
```typescript
// useCounter.test.ts
import { describe, it, expect } from 'vitest'
import { useCounter } from '@/composables/useCounter'

describe('useCounter', () => {
  it('should initialize with default value', () => {
    const { count } = useCounter()
    expect(count.value).toBe(0)
  })
  
  it('should increment count', () => {
    const { count, increment } = useCounter()
    increment()
    expect(count.value).toBe(1)
  })
  
  it('should respect min/max limits', () => {
    const { count, increment, decrement } = useCounter(0, { min: 0, max: 5 })
    
    // 测试最大值限制
    for (let i = 0; i < 10; i++) {
      increment()
    }
    expect(count.value).toBe(5)
    
    // 测试最小值限制
    for (let i = 0; i < 10; i++) {
      decrement()
    }
    expect(count.value).toBe(0)
  })
})
```

## 组织结构

### 目录结构
```
src/composables/
├── core/              # 核心通用composables
│   ├── useToggle.ts
│   ├── useCounter.ts
│   └── useLocalStorage.ts
├── api/               # API相关
│   ├── useApi.ts
│   └── useAsyncData.ts
├── ui/                # UI交互相关
│   ├── useModal.ts
│   ├── useToast.ts
│   └── useForm.ts
├── utils/             # 工具类
│   ├── useDebounce.ts
│   ├── useThrottle.ts
│   └── useEventListener.ts
└── business/          # 业务逻辑
    ├── useAuth.ts
    ├── useCart.ts
    └── useUserProfile.ts
```

### 导出规范
```typescript
// composables/index.ts
// 核心功能
export { useToggle } from './core/useToggle'
export { useCounter } from './core/useCounter'
export { useLocalStorage } from './core/useLocalStorage'

// API相关
export { useApi } from './api/useApi'
export { useAsyncData } from './api/useAsyncData'

// 业务逻辑
export { useAuth } from './business/useAuth'
export { useCart } from './business/useCart'
```

通过合理使用Composables，可以大大提高代码的复用性和可维护性，让Vue 3项目的开发更加高效和优雅。