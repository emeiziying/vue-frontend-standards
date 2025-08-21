# Pinia Store 结构规范

## 概述

本文档定义了 Pinia 状态管理的标准结构和最佳实践，确保团队在使用 Pinia 时保持一致的代码风格和架构模式。

## Store 定义标准模板

### 基础 Store 结构

```typescript
// stores/user.ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'
import type { User, LoginCredentials } from '@/types/user'

export const useUserStore = defineStore('user', () => {
  // ===== State =====
  const user = ref<User | null>(null)
  const loading = ref(false)
  const error = ref<string | null>(null)

  // ===== Getters =====
  const isLoggedIn = computed(() => !!user.value)
  const userName = computed(() => user.value?.name || '')
  const userRole = computed(() => user.value?.role || 'guest')

  // ===== Actions =====
  const login = async (credentials: LoginCredentials) => {
    loading.value = true
    error.value = null
    
    try {
      const response = await authApi.login(credentials)
      user.value = response.data
    } catch (err) {
      error.value = err instanceof Error ? err.message : '登录失败'
      throw err
    } finally {
      loading.value = false
    }
  }

  const logout = () => {
    user.value = null
    error.value = null
  }

  const clearError = () => {
    error.value = null
  }

  // ===== Return =====
  return {
    // State
    user: readonly(user),
    loading: readonly(loading),
    error: readonly(error),
    
    // Getters
    isLoggedIn,
    userName,
    userRole,
    
    // Actions
    login,
    logout,
    clearError
  }
})
```

### Store 文件命名规范

```
stores/
├── user.ts          # 用户相关状态
├── product.ts       # 产品相关状态
├── cart.ts          # 购物车状态
├── notification.ts  # 通知状态
└── app.ts          # 应用全局状态
```

## 状态、Getter、Action 组织规范

### 1. State 组织规范

#### 状态分类
```typescript
// ✅ 推荐：按功能分类组织状态
export const useProductStore = defineStore('product', () => {
  // 数据状态
  const products = ref<Product[]>([])
  const currentProduct = ref<Product | null>(null)
  
  // UI 状态
  const loading = ref(false)
  const error = ref<string | null>(null)
  const selectedFilters = ref<ProductFilter>({})
  
  // 分页状态
  const pagination = ref({
    page: 1,
    pageSize: 20,
    total: 0
  })
  
  // ...
})
```

#### 状态初始化
```typescript
// ✅ 推荐：提供明确的初始状态
const initialState = {
  user: null,
  loading: false,
  error: null,
  preferences: {
    theme: 'light',
    language: 'zh-CN'
  }
}

export const useUserStore = defineStore('user', () => {
  const user = ref<User | null>(initialState.user)
  const loading = ref(initialState.loading)
  const error = ref<string | null>(initialState.error)
  const preferences = ref({ ...initialState.preferences })
  
  // 重置状态的方法
  const resetState = () => {
    user.value = initialState.user
    loading.value = initialState.loading
    error.value = initialState.error
    preferences.value = { ...initialState.preferences }
  }
  
  return { user, loading, error, preferences, resetState }
})
```

### 2. Getters 组织规范

#### 计算属性命名
```typescript
export const useCartStore = defineStore('cart', () => {
  const items = ref<CartItem[]>([])
  
  // ✅ 推荐：使用描述性的计算属性名
  const totalItems = computed(() => 
    items.value.reduce((sum, item) => sum + item.quantity, 0)
  )
  
  const totalPrice = computed(() =>
    items.value.reduce((sum, item) => sum + item.price * item.quantity, 0)
  )
  
  const isEmpty = computed(() => items.value.length === 0)
  
  const hasDiscountItems = computed(() =>
    items.value.some(item => item.discount > 0)
  )
  
  // ✅ 推荐：复杂计算逻辑提取为独立函数
  const getItemsByCategory = computed(() => (category: string) =>
    items.value.filter(item => item.category === category)
  )
  
  return {
    items: readonly(items),
    totalItems,
    totalPrice,
    isEmpty,
    hasDiscountItems,
    getItemsByCategory
  }
})
```

### 3. Actions 组织规范

#### 异步 Actions 标准格式
```typescript
export const useApiStore = defineStore('api', () => {
  const data = ref<any[]>([])
  const loading = ref(false)
  const error = ref<string | null>(null)
  
  // ✅ 推荐：标准异步 Action 格式
  const fetchData = async (params?: FetchParams) => {
    loading.value = true
    error.value = null
    
    try {
      const response = await api.getData(params)
      data.value = response.data
      return response.data
    } catch (err) {
      const errorMessage = err instanceof Error ? err.message : '获取数据失败'
      error.value = errorMessage
      throw new Error(errorMessage)
    } finally {
      loading.value = false
    }
  }
  
  // ✅ 推荐：提供重试机制
  const retryFetch = async (params?: FetchParams, maxRetries = 3) => {
    let attempts = 0
    
    while (attempts < maxRetries) {
      try {
        return await fetchData(params)
      } catch (err) {
        attempts++
        if (attempts >= maxRetries) throw err
        await new Promise(resolve => setTimeout(resolve, 1000 * attempts))
      }
    }
  }
  
  return { data, loading, error, fetchData, retryFetch }
})
```

#### Actions 错误处理
```typescript
// ✅ 推荐：统一的错误处理模式
const handleApiError = (error: unknown, defaultMessage: string) => {
  if (error instanceof ApiError) {
    return error.message
  }
  if (error instanceof Error) {
    return error.message
  }
  return defaultMessage
}

export const useUserStore = defineStore('user', () => {
  const updateProfile = async (profileData: ProfileData) => {
    loading.value = true
    error.value = null
    
    try {
      const response = await userApi.updateProfile(profileData)
      user.value = { ...user.value, ...response.data }
      return response.data
    } catch (err) {
      const errorMessage = handleApiError(err, '更新用户信息失败')
      error.value = errorMessage
      throw new Error(errorMessage)
    } finally {
      loading.value = false
    }
  }
  
  return { updateProfile }
})
```

## Store 模块化最佳实践

### 1. 按功能域划分 Store

```typescript
// stores/index.ts - Store 注册中心
export { useUserStore } from './user'
export { useProductStore } from './product'
export { useCartStore } from './cart'
export { useOrderStore } from './order'
export { useNotificationStore } from './notification'
export { useAppStore } from './app'

// 类型导出
export type * from './types'
```

### 2. Store 组合模式

```typescript
// stores/composables/useAuth.ts
export const useAuth = () => {
  const userStore = useUserStore()
  const appStore = useAppStore()
  
  const login = async (credentials: LoginCredentials) => {
    try {
      await userStore.login(credentials)
      appStore.setAuthStatus(true)
      // 可以添加其他登录后的逻辑
    } catch (error) {
      appStore.setAuthStatus(false)
      throw error
    }
  }
  
  const logout = () => {
    userStore.logout()
    appStore.setAuthStatus(false)
    appStore.clearUserData()
  }
  
  return {
    login,
    logout,
    isLoggedIn: userStore.isLoggedIn,
    user: userStore.user
  }
}
```

### 3. Store 持久化

```typescript
// stores/user.ts
import { defineStore } from 'pinia'
import { useLocalStorage } from '@vueuse/core'

export const useUserStore = defineStore('user', () => {
  // ✅ 推荐：使用 VueUse 进行持久化
  const user = useLocalStorage<User | null>('user', null)
  const preferences = useLocalStorage('user-preferences', {
    theme: 'light',
    language: 'zh-CN'
  })
  
  // 敏感数据不持久化
  const token = ref<string | null>(null)
  
  return { user, preferences, token }
})
```

### 4. Store 类型定义

```typescript
// stores/types.ts
export interface BaseState {
  loading: boolean
  error: string | null
}

export interface PaginationState {
  page: number
  pageSize: number
  total: number
}

export interface ApiState<T> extends BaseState {
  data: T[]
  pagination: PaginationState
}

// stores/product.ts
export const useProductStore = defineStore('product', (): ApiState<Product> & {
  // 额外的状态和方法
  selectedProduct: Ref<Product | null>
  selectProduct: (product: Product) => void
} => {
  // 实现...
})
```

### 5. Store 测试支持

```typescript
// stores/user.ts
export const useUserStore = defineStore('user', () => {
  // ... store 实现
  
  // ✅ 推荐：提供测试辅助方法
  const __testing__ = {
    setUser: (userData: User) => {
      user.value = userData
    },
    setLoading: (loadingState: boolean) => {
      loading.value = loadingState
    },
    setError: (errorMessage: string | null) => {
      error.value = errorMessage
    },
    reset: () => {
      user.value = null
      loading.value = false
      error.value = null
    }
  }
  
  return {
    // 正常的 store 接口
    user, loading, error, login, logout,
    
    // 测试接口（仅在测试环境暴露）
    ...(process.env.NODE_ENV === 'test' ? { __testing__ } : {})
  }
})
```

## 命名约定

### Store 命名
- Store 文件：使用 camelCase，如 `userStore.ts`
- Store ID：使用 kebab-case，如 `'user-profile'`
- Composable：使用 `use` 前缀，如 `useUserStore()`

### 状态命名
- 数据状态：使用名词，如 `users`, `currentUser`
- 布尔状态：使用 `is/has/can` 前缀，如 `isLoading`, `hasError`
- 临时状态：使用描述性名称，如 `selectedItems`, `filterOptions`

### Action 命名
- 获取数据：`fetch*`, `load*`, `get*`
- 创建数据：`create*`, `add*`
- 更新数据：`update*`, `edit*`, `modify*`
- 删除数据：`delete*`, `remove*`
- 状态操作：`set*`, `toggle*`, `clear*`

## 性能优化建议

### 1. 避免不必要的响应式
```typescript
// ✅ 推荐：对只读数据使用 readonly
export const useConfigStore = defineStore('config', () => {
  const config = ref<AppConfig>({})
  
  return {
    config: readonly(config), // 防止外部修改
    updateConfig: (newConfig: Partial<AppConfig>) => {
      config.value = { ...config.value, ...newConfig }
    }
  }
})
```

### 2. 合理使用 computed
```typescript
// ✅ 推荐：缓存复杂计算
export const useDataStore = defineStore('data', () => {
  const items = ref<Item[]>([])
  
  // 复杂计算使用 computed 缓存
  const expensiveComputation = computed(() => {
    return items.value
      .filter(item => item.active)
      .sort((a, b) => b.priority - a.priority)
      .slice(0, 10)
  })
  
  return { items, expensiveComputation }
})
```

### 3. 按需加载 Store
```typescript
// utils/store-loader.ts
export const loadUserStore = () => import('@/stores/user')
export const loadProductStore = () => import('@/stores/product')

// 在组件中按需加载
const { useUserStore } = await loadUserStore()
const userStore = useUserStore()
```