# Vue组件Props和Events规范

## 概述

本文档定义了Vue组件中Props属性和Events事件的标准定义方式、命名规范和最佳实践，确保组件间通信的一致性和可维护性。

## Props属性规范

### 基本Props定义

使用TypeScript接口定义Props，提供完整的类型安全：

```vue
<script setup lang="ts">
// 基础Props接口定义
interface Props {
  // 必需的字符串属性
  title: string
  
  // 可选的数字属性
  count?: number
  
  // 布尔属性（默认false）
  disabled?: boolean
  
  // 数组属性
  items: Array<string>
  
  // 对象属性
  config: Record<string, any>
  
  // 联合类型
  size: 'small' | 'medium' | 'large'
  
  // 函数属性
  formatter?: (value: string) => string
}

// 使用withDefaults提供默认值
const props = withDefaults(defineProps<Props>(), {
  count: 0,
  disabled: false,
  size: 'medium',
  formatter: (value: string) => value
})
</script>
```

### Props命名规范

1. **camelCase命名**：在JavaScript中使用camelCase
2. **kebab-case在模板**：在HTML模板中使用kebab-case
3. **语义化命名**：使用描述性的名称

```vue
<!-- 父组件模板 -->
<template>
  <UserCard
    :user-name="userName"
    :is-active="isActive"
    :max-count="maxCount"
    @user-select="handleUserSelect"
  />
</template>

<script setup lang="ts">
// 子组件Props定义
interface Props {
  userName: string        // 对应 user-name
  isActive: boolean      // 对应 is-active  
  maxCount: number       // 对应 max-count
}
</script>
```

### Props类型定义标准

#### 基础类型

```typescript
interface BasicProps {
  // 字符串类型
  title: string
  description?: string
  
  // 数字类型
  count: number
  price?: number
  
  // 布尔类型
  visible: boolean
  disabled?: boolean
  
  // 日期类型
  createdAt: Date
  updatedAt?: Date
}
```

#### 复杂类型

```typescript
// 自定义类型定义
interface User {
  id: number
  name: string
  email: string
  avatar?: string
}

interface TableColumn {
  key: string
  title: string
  width?: number
  sortable?: boolean
}

interface ComponentProps {
  // 对象类型
  user: User
  
  // 数组类型
  columns: Array<TableColumn>
  users: User[]
  
  // 联合类型
  status: 'loading' | 'success' | 'error' | 'idle'
  
  // 泛型类型
  data: Record<string, any>
  
  // 函数类型
  onFilter?: (query: string) => void
  validator?: (value: any) => boolean
  
  // 可选的复杂类型
  config?: {
    theme: 'light' | 'dark'
    locale: string
    features: string[]
  }
}
```

#### 高级Props模式

```typescript
// 条件Props - 根据某个prop的值决定其他props的类型
interface BaseProps {
  type: 'button' | 'link'
}

interface ButtonProps extends BaseProps {
  type: 'button'
  onClick: () => void
  disabled?: boolean
}

interface LinkProps extends BaseProps {
  type: 'link'
  href: string
  target?: '_blank' | '_self'
}

type ComponentProps = ButtonProps | LinkProps

// 使用示例
const props = defineProps<ComponentProps>()

// 在模板中根据type渲染不同内容
```

### Props验证和默认值

```vue
<script setup lang="ts">
interface Props {
  // 必需属性
  title: string
  
  // 带默认值的可选属性
  size?: 'small' | 'medium' | 'large'
  count?: number
  visible?: boolean
  
  // 复杂默认值
  config?: {
    theme: string
    locale: string
  }
  
  // 数组默认值
  items?: string[]
  
  // 函数默认值
  formatter?: (value: any) => string
}

const props = withDefaults(defineProps<Props>(), {
  size: 'medium',
  count: 0,
  visible: true,
  
  // 对象默认值使用工厂函数
  config: () => ({
    theme: 'light',
    locale: 'zh-CN'
  }),
  
  // 数组默认值使用工厂函数
  items: () => [],
  
  // 函数默认值
  formatter: (value: any) => String(value)
})

// 运行时验证（开发环境）
if (process.env.NODE_ENV === 'development') {
  if (props.count < 0) {
    console.warn('count should be non-negative')
  }
}
</script>
```

## Events事件规范

### 基本Events定义

```vue
<script setup lang="ts">
// Events接口定义
interface Emits {
  // 简单事件（无参数）
  close: []
  
  // 单参数事件
  update: [value: string]
  change: [event: Event]
  
  // 多参数事件
  select: [item: any, index: number]
  
  // 复杂参数事件
  submit: [data: FormData, options: { validate: boolean }]
  
  // 可选参数事件
  error: [message: string, code?: number]
}

const emit = defineEmits<Emits>()

// 触发事件的方法
const handleClose = () => {
  emit('close')
}

const handleUpdate = (newValue: string) => {
  emit('update', newValue)
}

const handleSelect = (item: any, index: number) => {
  emit('select', item, index)
}
</script>
```

### 事件命名规范

1. **kebab-case命名**：在模板中使用kebab-case
2. **动词开头**：使用动词描述动作
3. **语义化命名**：清晰表达事件含义

```vue
<!-- 推荐的事件命名 -->
<template>
  <CustomComponent
    @item-select="handleItemSelect"
    @data-update="handleDataUpdate"
    @form-submit="handleFormSubmit"
    @modal-close="handleModalClose"
    @user-login="handleUserLogin"
    @file-upload="handleFileUpload"
  />
</template>

<!-- 避免的事件命名 -->
<template>
  <CustomComponent
    @click="handleClick"           <!-- 太通用 -->
    @change="handleChange"         <!-- 不够具体 -->
    @itemSelected="handleSelect"   <!-- 应使用kebab-case -->
    @onSubmit="handleSubmit"       <!-- 不要使用on前缀 -->
  />
</template>
```

### 标准事件模式

#### 1. 数据更新事件

```vue
<script setup lang="ts">
interface Props {
  modelValue: string
}

interface Emits {
  'update:modelValue': [value: string]
}

const props = defineProps<Props>()
const emit = defineEmits<Emits>()

// v-model支持
const updateValue = (newValue: string) => {
  emit('update:modelValue', newValue)
}
</script>

<template>
  <input 
    :value="modelValue"
    @input="updateValue($event.target.value)"
  />
</template>
```

#### 2. 用户交互事件

```vue
<script setup lang="ts">
interface Item {
  id: number
  name: string
}

interface Emits {
  'item-click': [item: Item, event: MouseEvent]
  'item-hover': [item: Item]
  'item-select': [item: Item, selected: boolean]
  'item-delete': [item: Item]
}

const emit = defineEmits<Emits>()

const handleItemClick = (item: Item, event: MouseEvent) => {
  emit('item-click', item, event)
}

const handleItemSelect = (item: Item, selected: boolean) => {
  emit('item-select', item, selected)
}
</script>
```

#### 3. 表单事件

```vue
<script setup lang="ts">
interface FormData {
  name: string
  email: string
}

interface ValidationError {
  field: string
  message: string
}

interface Emits {
  'form-submit': [data: FormData]
  'form-reset': []
  'form-change': [field: string, value: any]
  'form-validate': [isValid: boolean, errors: ValidationError[]]
}

const emit = defineEmits<Emits>()

const handleSubmit = (data: FormData) => {
  emit('form-submit', data)
}

const handleFieldChange = (field: string, value: any) => {
  emit('form-change', field, value)
}
</script>
```

#### 4. 生命周期事件

```vue
<script setup lang="ts">
interface Emits {
  'component-ready': []
  'data-loaded': [data: any[]]
  'loading-start': []
  'loading-end': []
  'error-occurred': [error: Error]
}

const emit = defineEmits<Emits>()

onMounted(() => {
  emit('component-ready')
})

const loadData = async () => {
  emit('loading-start')
  try {
    const data = await fetchData()
    emit('data-loaded', data)
  } catch (error) {
    emit('error-occurred', error as Error)
  } finally {
    emit('loading-end')
  }
}
</script>
```

### 事件参数规范

#### 1. 事件对象传递

```vue
<script setup lang="ts">
interface Emits {
  // 原生事件对象
  'button-click': [event: MouseEvent]
  'input-change': [event: Event]
  'form-submit': [event: SubmitEvent]
  
  // 自定义事件对象
  'custom-action': [payload: {
    type: string
    data: any
    timestamp: number
  }]
}

const emit = defineEmits<Emits>()

const handleClick = (event: MouseEvent) => {
  // 传递完整的事件对象
  emit('button-click', event)
}

const handleCustomAction = (type: string, data: any) => {
  // 传递结构化的事件数据
  emit('custom-action', {
    type,
    data,
    timestamp: Date.now()
  })
}
</script>
```

#### 2. 数据变更事件

```vue
<script setup lang="ts">
interface DataChangeEvent<T = any> {
  oldValue: T
  newValue: T
  field?: string
}

interface Emits {
  'value-change': [event: DataChangeEvent<string>]
  'item-update': [event: DataChangeEvent<Item>]
}

const emit = defineEmits<Emits>()

const updateValue = (newValue: string, oldValue: string) => {
  emit('value-change', {
    oldValue,
    newValue,
    field: 'value'
  })
}
</script>
```

## 组件通信最佳实践

### 1. Props Down, Events Up模式

```vue
<!-- 父组件 -->
<template>
  <UserList
    :users="users"
    :loading="loading"
    @user-select="handleUserSelect"
    @user-delete="handleUserDelete"
  />
</template>

<script setup lang="ts">
const users = ref<User[]>([])
const loading = ref(false)

const handleUserSelect = (user: User) => {
  // 处理用户选择
  selectedUser.value = user
}

const handleUserDelete = async (user: User) => {
  // 处理用户删除
  await deleteUser(user.id)
  await loadUsers()
}
</script>
```

### 2. v-model双向绑定

```vue
<!-- 自定义输入组件 -->
<template>
  <div class="custom-input">
    <input
      :value="modelValue"
      @input="handleInput"
      @blur="handleBlur"
    />
  </div>
</template>

<script setup lang="ts">
interface Props {
  modelValue: string
  placeholder?: string
}

interface Emits {
  'update:modelValue': [value: string]
  'blur': [event: FocusEvent]
}

const props = defineProps<Props>()
const emit = defineEmits<Emits>()

const handleInput = (event: Event) => {
  const target = event.target as HTMLInputElement
  emit('update:modelValue', target.value)
}

const handleBlur = (event: FocusEvent) => {
  emit('blur', event)
}
</script>

<!-- 使用组件 -->
<template>
  <CustomInput v-model="inputValue" />
</template>
```

### 3. 多个v-model绑定

```vue
<!-- 支持多个v-model的组件 -->
<template>
  <div class="user-form">
    <input
      :value="firstName"
      @input="updateFirstName"
      placeholder="名"
    />
    <input
      :value="lastName"
      @input="updateLastName"
      placeholder="姓"
    />
  </div>
</template>

<script setup lang="ts">
interface Props {
  firstName: string
  lastName: string
}

interface Emits {
  'update:firstName': [value: string]
  'update:lastName': [value: string]
}

const props = defineProps<Props>()
const emit = defineEmits<Emits>()

const updateFirstName = (event: Event) => {
  const target = event.target as HTMLInputElement
  emit('update:firstName', target.value)
}

const updateLastName = (event: Event) => {
  const target = event.target as HTMLInputElement
  emit('update:lastName', target.value)
}
</script>

<!-- 使用组件 -->
<template>
  <UserForm
    v-model:first-name="user.firstName"
    v-model:last-name="user.lastName"
  />
</template>
```

### 4. 事件修饰符使用

```vue
<template>
  <!-- 阻止默认行为 -->
  <form @submit.prevent="handleSubmit">
    <!-- 阻止事件冒泡 -->
    <button @click.stop="handleButtonClick">
      点击
    </button>
    
    <!-- 只触发一次 -->
    <button @click.once="handleOnceClick">
      只能点击一次
    </button>
    
    <!-- 键盘事件修饰符 -->
    <input
      @keyup.enter="handleEnter"
      @keyup.esc="handleEscape"
    />
  </form>
</template>
```

### 5. 异步事件处理

```vue
<script setup lang="ts">
interface Emits {
  'async-action': [callback: (result: any) => void]
  'promise-action': [promise: Promise<any>]
}

const emit = defineEmits<Emits>()

// 回调模式
const handleAsyncAction = () => {
  emit('async-action', (result) => {
    console.log('异步操作完成:', result)
  })
}

// Promise模式
const handlePromiseAction = () => {
  const promise = new Promise((resolve) => {
    setTimeout(() => resolve('完成'), 1000)
  })
  emit('promise-action', promise)
}
</script>
```

## 类型安全最佳实践

### 1. 严格的类型定义

```typescript
// 定义严格的Props类型
interface StrictProps {
  // 使用字面量类型而不是string
  status: 'idle' | 'loading' | 'success' | 'error'
  
  // 使用具体的数字范围
  priority: 1 | 2 | 3 | 4 | 5
  
  // 使用品牌类型确保类型安全
  userId: string & { readonly brand: unique symbol }
  
  // 使用条件类型
  config: T extends 'advanced' ? AdvancedConfig : BasicConfig
}
```

### 2. 运行时类型检查

```vue
<script setup lang="ts">
interface Props {
  items: Array<{ id: number; name: string }>
}

const props = defineProps<Props>()

// 开发环境下的运行时检查
if (process.env.NODE_ENV === 'development') {
  watchEffect(() => {
    props.items.forEach((item, index) => {
      if (typeof item.id !== 'number') {
        console.error(`Item at index ${index} has invalid id type`)
      }
      if (typeof item.name !== 'string') {
        console.error(`Item at index ${index} has invalid name type`)
      }
    })
  })
}
</script>
```

### 3. 泛型组件

```vue
<script setup lang="ts" generic="T extends Record<string, any>">
interface Props<T> {
  items: T[]
  keyField: keyof T
  displayField: keyof T
}

interface Emits<T> {
  'item-select': [item: T]
  'item-delete': [item: T]
}

const props = defineProps<Props<T>>()
const emit = defineEmits<Emits<T>>()

const handleItemSelect = (item: T) => {
  emit('item-select', item)
}
</script>

<template>
  <div class="generic-list">
    <div
      v-for="item in items"
      :key="item[keyField]"
      @click="handleItemSelect(item)"
    >
      {{ item[displayField] }}
    </div>
  </div>
</template>
```

## 常见错误和解决方案

### 1. Props变更检测

```vue
<script setup lang="ts">
// ❌ 错误：直接修改props
const props = defineProps<{ count: number }>()
// props.count++ // 这会导致警告

// ✅ 正确：使用本地状态
const localCount = ref(props.count)

// ✅ 正确：通过事件通知父组件
const emit = defineEmits<{ 'update-count': [count: number] }>()
const incrementCount = () => {
  emit('update-count', props.count + 1)
}
</script>
```

### 2. 事件命名冲突

```vue
<script setup lang="ts">
// ❌ 避免与原生事件同名
interface Emits {
  click: [event: MouseEvent]  // 与原生click冲突
  change: [value: string]     // 与原生change冲突
}

// ✅ 使用更具体的事件名
interface Emits {
  'item-click': [event: MouseEvent]
  'value-change': [value: string]
}
</script>
```

### 3. 内存泄漏预防

```vue
<script setup lang="ts">
const emit = defineEmits<{
  'data-update': [data: any]
}>()

// ❌ 可能导致内存泄漏
const timer = setInterval(() => {
  emit('data-update', getData())
}, 1000)

// ✅ 正确的清理方式
onBeforeUnmount(() => {
  if (timer) {
    clearInterval(timer)
  }
})
</script>
```

## 总结

遵循这些Props和Events规范可以确保：

1. **类型安全**：完整的TypeScript类型定义
2. **一致性**：统一的命名和结构规范
3. **可维护性**：清晰的组件通信模式
4. **可读性**：语义化的属性和事件命名
5. **性能**：避免不必要的重渲染和内存泄漏

在实际开发中，应该根据具体需求灵活应用这些规范，同时保持代码的简洁性和可读性。