# Vue前端开发规范 - 完整示例

<div align="center">

![代码示例](https://img.shields.io/badge/代码示例-完整集合-blue)
![覆盖范围](https://img.shields.io/badge/覆盖-组件|Store|工具-green)
![代码质量](https://img.shields.io/badge/质量-生产就绪-brightgreen)

**💡 完整的Vue项目代码示例集合**

[🏠 返回首页](./README.md) • [📋 完整目录](./TABLE_OF_CONTENTS.md) • [🚀 快速开始](./QUICK_START.md) • [📄 配置模板](./TEMPLATES.md)

</div>

---

## 📁 完整项目示例

> 💡 **说明**: 以下是一个标准Vue项目的完整目录结构示例，展示了如何组织文件和目录

### 项目结构示例

```
vue-project-example/
├── public/
│   ├── favicon.ico
│   └── index.html
├── src/
│   ├── assets/
│   │   ├── images/
│   │   │   ├── logo.png
│   │   │   └── avatars/
│   │   ├── icons/
│   │   │   ├── user.svg
│   │   │   └── settings.svg
│   │   └── styles/
│   │       ├── main.css
│   │       ├── variables.css
│   │       └── components.css
│   ├── components/
│   │   ├── base/
│   │   │   ├── BaseButton.vue
│   │   │   ├── BaseInput.vue
│   │   │   ├── BaseModal.vue
│   │   │   └── index.ts
│   │   ├── common/
│   │   │   ├── UserCard.vue
│   │   │   ├── DataTable.vue
│   │   │   ├── SearchBox.vue
│   │   │   └── index.ts
│   │   └── layout/
│   │       ├── AppHeader.vue
│   │       ├── AppSidebar.vue
│   │       ├── AppFooter.vue
│   │       └── DefaultLayout.vue
│   ├── views/
│   │   ├── home/
│   │   │   ├── HomePage.vue
│   │   │   └── components/
│   │   │       └── WelcomeSection.vue
│   │   ├── user/
│   │   │   ├── UserListPage.vue
│   │   │   ├── UserDetailPage.vue
│   │   │   ├── UserEditPage.vue
│   │   │   └── components/
│   │   │       ├── UserForm.vue
│   │   │       └── UserStats.vue
│   │   └── auth/
│   │       ├── LoginPage.vue
│   │       └── RegisterPage.vue
│   ├── stores/
│   │   ├── modules/
│   │   │   ├── user.ts
│   │   │   ├── auth.ts
│   │   │   └── app.ts
│   │   └── index.ts
│   ├── router/
│   │   ├── modules/
│   │   │   ├── user.ts
│   │   │   ├── auth.ts
│   │   │   └── admin.ts
│   │   ├── guards.ts
│   │   └── index.ts
│   ├── composables/
│   │   ├── useAuth.ts
│   │   ├── useApi.ts
│   │   ├── useLocalStorage.ts
│   │   └── index.ts
│   ├── utils/
│   │   ├── helpers/
│   │   │   ├── format.ts
│   │   │   ├── validation.ts
│   │   │   └── date.ts
│   │   ├── constants/
│   │   │   ├── api.ts
│   │   │   ├── routes.ts
│   │   │   └── app.ts
│   │   └── index.ts
│   ├── services/
│   │   ├── api/
│   │   │   ├── base.ts
│   │   │   ├── user.ts
│   │   │   ├── auth.ts
│   │   │   └── index.ts
│   │   └── http/
│   │       ├── interceptors.ts
│   │       └── config.ts
│   ├── types/
│   │   ├── api/
│   │   │   ├── user.ts
│   │   │   ├── auth.ts
│   │   │   └── common.ts
│   │   ├── components/
│   │   │   ├── base.ts
│   │   │   └── common.ts
│   │   └── global.d.ts
│   ├── plugins/
│   │   ├── pinia.ts
│   │   └── router.ts
│   ├── App.vue
│   └── main.ts
├── tests/
│   ├── unit/
│   │   ├── components/
│   │   ├── utils/
│   │   └── stores/
│   ├── e2e/
│   │   ├── specs/
│   │   └── fixtures/
│   └── setup.ts
├── docs/
│   ├── api.md
│   ├── deployment.md
│   └── development.md
├── .vscode/
│   ├── settings.json
│   ├── extensions.json
│   └── launch.json
├── .eslintrc.js
├── .prettierrc
├── .gitignore
├── tailwind.config.js
├── vite.config.ts
├── tsconfig.json
├── package.json
└── README.md
```

---

## 📋 示例目录

| 分类 | 示例内容 | 复杂度 |
|------|----------|--------|
| 📁 [项目结构](#📁-完整项目示例) | 标准目录结构 | ⭐ |
| 🎯 [Vue组件](#🎯-vue组件示例) | 基础组件、业务组件、页面组件 | ⭐⭐ |
| 🗄️ [Pinia Store](#🗄️-pinia-store示例) | 状态管理完整示例 | ⭐⭐⭐ |
| 🛠️ [工具函数](#🛠️-工具函数示例) | 格式化、验证、工具类 | ⭐⭐ |
| 🔧 [配置文件](#🔧-配置文件模板集合) | Vite、TypeScript、Package.json | ⭐⭐ |
| 🧪 [测试示例](#🧪-测试示例) | 单元测试、组件测试 | ⭐⭐⭐ |
| 🎨 [样式示例](#🎨-样式示例) | CSS、TailwindCSS | ⭐⭐ |

---

## 🎯 Vue组件示例

> 📚 **组件分类**: 基础组件 → 业务组件 → 页面组件，按复杂度递增

### 1. 🔘 基础组件示例

<details>
<summary><strong>📄 BaseButton.vue - 通用按钮组件</strong></summary>

> **功能特性**: 多种样式变体、尺寸选择、加载状态、禁用状态  
> **使用场景**: 表单提交、操作触发、导航跳转  
> **复杂度**: ⭐⭐
```vue
<template>
  <button 
    :class="buttonClasses"
    :disabled="disabled || loading"
    :type="type"
    @click="handleClick"
  >
    <!-- 🔄 加载状态指示器 -->
    <span v-if="loading" class="base-button__spinner">
      <svg class="animate-spin h-4 w-4" viewBox="0 0 24 24">
        <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4" fill="none"/>
        <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"/>
      </svg>
    </span>
    
    <!-- 📝 按钮文本内容 -->
    <span :class="{ 'ml-2': loading }">
      <slot />
    </span>
  </button>
</template>

<script setup lang="ts">
import { computed } from 'vue'

interface Props {
  variant?: 'primary' | 'secondary' | 'success' | 'warning' | 'danger' | 'ghost'
  size?: 'xs' | 'sm' | 'md' | 'lg' | 'xl'
  disabled?: boolean
  loading?: boolean
  type?: 'button' | 'submit' | 'reset'
  block?: boolean
}

interface Emits {
  click: [event: MouseEvent]
}

const props = withDefaults(defineProps<Props>(), {
  variant: 'primary',
  size: 'md',
  disabled: false,
  loading: false,
  type: 'button',
  block: false
})

const emit = defineEmits<Emits>()

const buttonClasses = computed(() => {
  const baseClasses = [
    'base-button',
    'inline-flex',
    'items-center',
    'justify-center',
    'font-medium',
    'rounded-md',
    'transition-all',
    'duration-200',
    'focus:outline-none',
    'focus:ring-2',
    'focus:ring-offset-2'
  ]

  const variantClasses = {
    primary: [
      'bg-blue-600',
      'text-white',
      'hover:bg-blue-700',
      'focus:ring-blue-500',
      'disabled:bg-blue-300'
    ],
    secondary: [
      'bg-gray-200',
      'text-gray-900',
      'hover:bg-gray-300',
      'focus:ring-gray-500',
      'disabled:bg-gray-100'
    ],
    success: [
      'bg-green-600',
      'text-white',
      'hover:bg-green-700',
      'focus:ring-green-500',
      'disabled:bg-green-300'
    ],
    warning: [
      'bg-yellow-600',
      'text-white',
      'hover:bg-yellow-700',
      'focus:ring-yellow-500',
      'disabled:bg-yellow-300'
    ],
    danger: [
      'bg-red-600',
      'text-white',
      'hover:bg-red-700',
      'focus:ring-red-500',
      'disabled:bg-red-300'
    ],
    ghost: [
      'bg-transparent',
      'text-gray-700',
      'border',
      'border-gray-300',
      'hover:bg-gray-50',
      'focus:ring-gray-500',
      'disabled:text-gray-400'
    ]
  }

  const sizeClasses = {
    xs: ['px-2', 'py-1', 'text-xs'],
    sm: ['px-3', 'py-1.5', 'text-sm'],
    md: ['px-4', 'py-2', 'text-sm'],
    lg: ['px-4', 'py-2', 'text-base'],
    xl: ['px-6', 'py-3', 'text-base']
  }

  const blockClasses = props.block ? ['w-full'] : []
  const disabledClasses = (props.disabled || props.loading) ? ['cursor-not-allowed', 'opacity-60'] : []

  return [
    ...baseClasses,
    ...variantClasses[props.variant],
    ...sizeClasses[props.size],
    ...blockClasses,
    ...disabledClasses
  ]
})

const handleClick = (event: MouseEvent) => {
  if (!props.disabled && !props.loading) {
    emit('click', event)
  }
}
</script>

<style scoped>
/* 🔄 加载指示器样式 */
.base-button__spinner {
  display: inline-flex;
  align-items: center;
}
</style>
```

**使用示例**:
```vue
<template>
  <!-- 基础用法 -->
  <BaseButton @click="handleClick">点击我</BaseButton>
  
  <!-- 不同变体 -->
  <BaseButton variant="primary">主要按钮</BaseButton>
  <BaseButton variant="secondary">次要按钮</BaseButton>
  <BaseButton variant="danger">危险按钮</BaseButton>
  
  <!-- 不同尺寸 -->
  <BaseButton size="sm">小按钮</BaseButton>
  <BaseButton size="lg">大按钮</BaseButton>
  
  <!-- 加载状态 -->
  <BaseButton :loading="isSubmitting">提交中...</BaseButton>
  
  <!-- 块级按钮 -->
  <BaseButton block>全宽按钮</BaseButton>
</template>
```

</details>

<details>
<summary><strong>📝 BaseInput.vue - 通用输入框组件</strong></summary>

> **功能特性**: 多种输入类型、标签和提示、错误状态、前后缀插槽  
> **使用场景**: 表单输入、搜索框、数据录入  
> **复杂度**: ⭐⭐
```vue
<template>
  <div class="base-input">
    <label 
      v-if="label" 
      :for="inputId"
      class="base-input__label"
    >
      {{ label }}
      <span v-if="required" class="base-input__required">*</span>
    </label>
    
    <div class="base-input__wrapper">
      <div v-if="$slots.prefix" class="base-input__prefix">
        <slot name="prefix" />
      </div>
      
      <input
        :id="inputId"
        v-model="inputValue"
        :type="type"
        :placeholder="placeholder"
        :disabled="disabled"
        :readonly="readonly"
        :required="required"
        :class="inputClasses"
        @blur="handleBlur"
        @focus="handleFocus"
        @input="handleInput"
      >
      
      <div v-if="$slots.suffix" class="base-input__suffix">
        <slot name="suffix" />
      </div>
    </div>
    
    <div v-if="error || hint" class="base-input__message">
      <p v-if="error" class="base-input__error">{{ error }}</p>
      <p v-else-if="hint" class="base-input__hint">{{ hint }}</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue'

interface Props {
  modelValue?: string | number
  type?: 'text' | 'email' | 'password' | 'number' | 'tel' | 'url'
  label?: string
  placeholder?: string
  hint?: string
  error?: string
  disabled?: boolean
  readonly?: boolean
  required?: boolean
  size?: 'sm' | 'md' | 'lg'
}

interface Emits {
  'update:modelValue': [value: string | number]
  blur: [event: FocusEvent]
  focus: [event: FocusEvent]
  input: [event: Event]
}

const props = withDefaults(defineProps<Props>(), {
  modelValue: '',
  type: 'text',
  disabled: false,
  readonly: false,
  required: false,
  size: 'md'
})

const emit = defineEmits<Emits>()

const inputId = ref(`input-${Math.random().toString(36).substr(2, 9)}`)

const inputValue = computed({
  get: () => props.modelValue,
  set: (value) => emit('update:modelValue', value)
})

const inputClasses = computed(() => {
  const baseClasses = [
    'base-input__field',
    'block',
    'w-full',
    'border',
    'rounded-md',
    'shadow-sm',
    'transition-colors',
    'duration-200',
    'focus:outline-none',
    'focus:ring-2',
    'focus:ring-offset-2'
  ]

  const sizeClasses = {
    sm: ['px-3', 'py-1.5', 'text-sm'],
    md: ['px-3', 'py-2', 'text-sm'],
    lg: ['px-4', 'py-3', 'text-base']
  }

  const stateClasses = props.error
    ? ['border-red-300', 'focus:border-red-500', 'focus:ring-red-500']
    : ['border-gray-300', 'focus:border-blue-500', 'focus:ring-blue-500']

  const disabledClasses = props.disabled
    ? ['bg-gray-50', 'text-gray-500', 'cursor-not-allowed']
    : ['bg-white', 'text-gray-900']

  return [
    ...baseClasses,
    ...sizeClasses[props.size],
    ...stateClasses,
    ...disabledClasses
  ]
})

const handleBlur = (event: FocusEvent) => {
  emit('blur', event)
}

const handleFocus = (event: FocusEvent) => {
  emit('focus', event)
}

const handleInput = (event: Event) => {
  emit('input', event)
}
</script>

<style scoped>
.base-input {
  @apply space-y-1;
}

.base-input__label {
  @apply block text-sm font-medium text-gray-700;
}

.base-input__required {
  @apply text-red-500;
}

.base-input__wrapper {
  @apply relative flex items-center;
}

.base-input__prefix,
.base-input__suffix {
  @apply absolute inset-y-0 flex items-center px-3 text-gray-500;
}

.base-input__prefix {
  @apply left-0;
}

.base-input__suffix {
  @apply right-0;
}

.base-input__field {
  @apply placeholder-gray-400;
}

.base-input__message {
  @apply mt-1;
}

.base-input__error {
  @apply text-sm text-red-600;
}

.base-input__hint {
  @apply text-sm text-gray-500;
}
</style>
```

**使用示例**:
```vue
<template>
  <!-- 基础用法 -->
  <BaseInput v-model="username" label="用户名" placeholder="请输入用户名" />
  
  <!-- 带验证 -->
  <BaseInput 
    v-model="email" 
    type="email"
    label="邮箱地址" 
    :error="emailError"
    required
  />
  
  <!-- 带提示 -->
  <BaseInput 
    v-model="password" 
    type="password"
    label="密码"
    hint="密码长度至少8位"
  />
  
  <!-- 带前后缀 -->
  <BaseInput v-model="amount" label="金额">
    <template #prefix>¥</template>
    <template #suffix>.00</template>
  </BaseInput>
</template>
```

</details>

### 2. 💼 业务组件示例

<details>
<summary><strong>👤 UserCard.vue - 用户卡片组件</strong></summary>

> **功能特性**: 用户信息展示、在线状态、统计数据、操作按钮  
> **使用场景**: 用户列表、团队展示、联系人卡片  
> **复杂度**: ⭐⭐⭐
```vue
<template>
  <div class="user-card">
    <div class="user-card__avatar">
      <img 
        :src="user.avatar || defaultAvatar" 
        :alt="`${user.name}的头像`"
        class="user-card__avatar-image"
        @error="handleImageError"
      >
      <div 
        v-if="user.isOnline" 
        class="user-card__status user-card__status--online"
      />
    </div>
    
    <div class="user-card__content">
      <h3 class="user-card__name">{{ user.name }}</h3>
      <p class="user-card__title">{{ user.title }}</p>
      <p class="user-card__email">{{ user.email }}</p>
      
      <div class="user-card__stats">
        <div class="user-card__stat">
          <span class="user-card__stat-label">项目</span>
          <span class="user-card__stat-value">{{ user.projectCount }}</span>
        </div>
        <div class="user-card__stat">
          <span class="user-card__stat-label">任务</span>
          <span class="user-card__stat-value">{{ user.taskCount }}</span>
        </div>
      </div>
    </div>
    
    <div class="user-card__actions">
      <BaseButton 
        size="sm" 
        variant="ghost"
        @click="handleMessage"
      >
        发消息
      </BaseButton>
      <BaseButton 
        size="sm" 
        variant="primary"
        @click="handleViewProfile"
      >
        查看详情
      </BaseButton>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import BaseButton from '@/components/base/BaseButton.vue'
import type { User } from '@/types/user'

interface Props {
  user: User
}

interface Emits {
  message: [user: User]
  'view-profile': [user: User]
}

const props = defineProps<Props>()
const emit = defineEmits<Emits>()

const defaultAvatar = ref('/images/default-avatar.png')

const handleImageError = (event: Event) => {
  const target = event.target as HTMLImageElement
  target.src = defaultAvatar.value
}

const handleMessage = () => {
  emit('message', props.user)
}

const handleViewProfile = () => {
  emit('view-profile', props.user)
}
</script>

<style scoped>
.user-card {
  @apply bg-white rounded-lg shadow-md p-6 space-y-4 hover:shadow-lg transition-shadow duration-200;
}

.user-card__avatar {
  @apply relative w-16 h-16 mx-auto;
}

.user-card__avatar-image {
  @apply w-full h-full rounded-full object-cover;
}

.user-card__status {
  @apply absolute bottom-0 right-0 w-4 h-4 rounded-full border-2 border-white;
}

.user-card__status--online {
  @apply bg-green-500;
}

.user-card__content {
  @apply text-center space-y-2;
}

.user-card__name {
  @apply text-lg font-semibold text-gray-900;
}

.user-card__title {
  @apply text-sm text-gray-600;
}

.user-card__email {
  @apply text-sm text-gray-500;
}

.user-card__stats {
  @apply flex justify-center space-x-6 pt-4 border-t border-gray-200;
}

.user-card__stat {
  @apply text-center;
}

.user-card__stat-label {
  @apply block text-xs text-gray-500;
}

.user-card__stat-value {
  @apply block text-lg font-semibold text-gray-900;
}

.user-card__actions {
  @apply flex space-x-2;
}
</style>
```

**使用示例**:
```vue
<template>
  <div class="user-grid">
    <UserCard
      v-for="user in users"
      :key="user.id"
      :user="user"
      @message="handleSendMessage"
      @view-profile="handleViewProfile"
    />
  </div>
</template>

<script setup lang="ts">
const handleSendMessage = (user: User) => {
  // 发送消息逻辑
  console.log('发送消息给:', user.name)
}

const handleViewProfile = (user: User) => {
  // 查看用户详情
  router.push(`/users/${user.id}`)
}
</script>
```

</details>

### 3. 📄 页面组件示例

<details>
<summary><strong>📋 UserListPage.vue - 用户列表页面</strong></summary>

> **功能特性**: 数据列表、搜索过滤、分页、加载状态、错误处理  
> **使用场景**: 管理后台、数据展示、CRUD操作  
> **复杂度**: ⭐⭐⭐⭐
```vue
<template>
  <div class="user-list-page">
    <header class="user-list-page__header">
      <div class="user-list-page__title-section">
        <h1 class="user-list-page__title">用户管理</h1>
        <p class="user-list-page__subtitle">管理系统中的所有用户</p>
      </div>
      
      <div class="user-list-page__actions">
        <BaseButton 
          variant="primary"
          @click="handleCreateUser"
        >
          新增用户
        </BaseButton>
      </div>
    </header>
    
    <div class="user-list-page__filters">
      <SearchBox 
        v-model="searchQuery"
        placeholder="搜索用户..."
        @search="handleSearch"
      />
      
      <select 
        v-model="selectedRole"
        class="user-list-page__role-filter"
        @change="handleRoleFilter"
      >
        <option value="">所有角色</option>
        <option value="admin">管理员</option>
        <option value="user">普通用户</option>
        <option value="guest">访客</option>
      </select>
    </div>
    
    <main class="user-list-page__content">
      <div v-if="isLoading" class="user-list-page__loading">
        <p>加载中...</p>
      </div>
      
      <div v-else-if="error" class="user-list-page__error">
        <p>{{ error }}</p>
        <BaseButton 
          variant="secondary"
          @click="handleRetry"
        >
          重试
        </BaseButton>
      </div>
      
      <div v-else-if="filteredUsers.length === 0" class="user-list-page__empty">
        <p>没有找到用户</p>
      </div>
      
      <div v-else class="user-list-page__grid">
        <UserCard
          v-for="user in filteredUsers"
          :key="user.id"
          :user="user"
          @message="handleUserMessage"
          @view-profile="handleViewProfile"
        />
      </div>
    </main>
    
    <footer v-if="totalPages > 1" class="user-list-page__pagination">
      <Pagination
        :current-page="currentPage"
        :total-pages="totalPages"
        @page-change="handlePageChange"
      />
    </footer>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, watch } from 'vue'
import { useRouter } from 'vue-router'
import { useUserStore } from '@/stores/modules/user'
import BaseButton from '@/components/base/BaseButton.vue'
import UserCard from '@/components/common/UserCard.vue'
import SearchBox from '@/components/common/SearchBox.vue'
import Pagination from '@/components/common/Pagination.vue'
import type { User } from '@/types/user'

const router = useRouter()
const userStore = useUserStore()

// 响应式数据
const searchQuery = ref('')
const selectedRole = ref('')
const currentPage = ref(1)
const pageSize = ref(12)

// 计算属性
const isLoading = computed(() => userStore.isLoading)
const error = computed(() => userStore.error)
const users = computed(() => userStore.users)

const filteredUsers = computed(() => {
  let result = users.value

  // 搜索过滤
  if (searchQuery.value) {
    const query = searchQuery.value.toLowerCase()
    result = result.filter(user => 
      user.name.toLowerCase().includes(query) ||
      user.email.toLowerCase().includes(query)
    )
  }

  // 角色过滤
  if (selectedRole.value) {
    result = result.filter(user => user.role === selectedRole.value)
  }

  // 分页
  const start = (currentPage.value - 1) * pageSize.value
  const end = start + pageSize.value
  return result.slice(start, end)
})

const totalPages = computed(() => {
  const total = users.value.length
  return Math.ceil(total / pageSize.value)
})

// 方法
const handleCreateUser = () => {
  router.push('/users/create')
}

const handleSearch = (query: string) => {
  searchQuery.value = query
  currentPage.value = 1
}

const handleRoleFilter = () => {
  currentPage.value = 1
}

const handleUserMessage = (user: User) => {
  // 实现发送消息逻辑
  console.log('发送消息给:', user.name)
}

const handleViewProfile = (user: User) => {
  router.push(`/users/${user.id}`)
}

const handlePageChange = (page: number) => {
  currentPage.value = page
}

const handleRetry = () => {
  loadUsers()
}

const loadUsers = async () => {
  try {
    await userStore.fetchUsers()
  } catch (err) {
    console.error('加载用户失败:', err)
  }
}

// 生命周期
onMounted(() => {
  loadUsers()
})

// 监听器
watch([searchQuery, selectedRole], () => {
  currentPage.value = 1
})
</script>

<style scoped>
.user-list-page {
  @apply container mx-auto px-4 py-8 space-y-6;
}

.user-list-page__header {
  @apply flex justify-between items-start;
}

.user-list-page__title-section {
  @apply space-y-1;
}

.user-list-page__title {
  @apply text-3xl font-bold text-gray-900;
}

.user-list-page__subtitle {
  @apply text-gray-600;
}

.user-list-page__actions {
  @apply flex space-x-3;
}

.user-list-page__filters {
  @apply flex space-x-4 items-center;
}

.user-list-page__role-filter {
  @apply px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500;
}

.user-list-page__content {
  @apply min-h-96;
}

.user-list-page__loading,
.user-list-page__error,
.user-list-page__empty {
  @apply flex flex-col items-center justify-center py-12 text-gray-500;
}

.user-list-page__grid {
  @apply grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6;
}

.user-list-page__pagination {
  @apply flex justify-center;
}
</style>
```

**页面特性**:
- ✅ 响应式布局设计
- ✅ 搜索和过滤功能
- ✅ 分页处理
- ✅ 加载和错误状态
- ✅ 用户交互反馈

</details>

---

## 🗄️ Pinia Store示例

> 📦 **状态管理**: 展示完整的Pinia Store实现，包含状态、计算属性、操作方法

<details>
<summary><strong>👥 useUserStore.ts - 完整用户状态管理</strong></summary>

> **功能特性**: CRUD操作、分页、缓存、错误处理、类型安全  
> **使用场景**: 用户管理、数据缓存、状态同步  
> **复杂度**: ⭐⭐⭐⭐

```typescript
// stores/modules/user.ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'
import { userApi } from '@/services/api/user'
import type { User, CreateUserData, UpdateUserData, UserFilters } from '@/types/user'

export const useUserStore = defineStore('user', () => {
  // State
  const users = ref<User[]>([])
  const currentUser = ref<User | null>(null)
  const isLoading = ref(false)
  const error = ref<string | null>(null)
  const pagination = ref({
    page: 1,
    pageSize: 20,
    total: 0
  })

  // Getters
  const totalUsers = computed(() => users.value.length)
  const activeUsers = computed(() => users.value.filter(user => user.isActive))
  const userById = computed(() => (id: string) => users.value.find(user => user.id === id))
  const isCurrentUserAdmin = computed(() => currentUser.value?.role === 'admin')

  // Actions
  const fetchUsers = async (filters?: UserFilters) => {
    try {
      isLoading.value = true
      error.value = null

      const response = await userApi.getUsers({
        page: pagination.value.page,
        pageSize: pagination.value.pageSize,
        ...filters
      })

      users.value = response.data.users
      pagination.value.total = response.data.total

      return response
    } catch (err) {
      error.value = err instanceof Error ? err.message : '获取用户列表失败'
      throw err
    } finally {
      isLoading.value = false
    }
  }

  const fetchUserById = async (id: string) => {
    try {
      isLoading.value = true
      error.value = null

      const response = await userApi.getUserById(id)
      const user = response.data

      // 更新本地缓存
      const index = users.value.findIndex(u => u.id === id)
      if (index !== -1) {
        users.value[index] = user
      } else {
        users.value.push(user)
      }

      return user
    } catch (err) {
      error.value = err instanceof Error ? err.message : '获取用户详情失败'
      throw err
    } finally {
      isLoading.value = false
    }
  }

  const createUser = async (userData: CreateUserData) => {
    try {
      isLoading.value = true
      error.value = null

      const response = await userApi.createUser(userData)
      const newUser = response.data

      users.value.unshift(newUser)
      pagination.value.total += 1

      return newUser
    } catch (err) {
      error.value = err instanceof Error ? err.message : '创建用户失败'
      throw err
    } finally {
      isLoading.value = false
    }
  }

  const updateUser = async (id: string, userData: UpdateUserData) => {
    try {
      isLoading.value = true
      error.value = null

      const response = await userApi.updateUser(id, userData)
      const updatedUser = response.data

      // 更新本地缓存
      const index = users.value.findIndex(u => u.id === id)
      if (index !== -1) {
        users.value[index] = updatedUser
      }

      // 如果是当前用户，更新当前用户信息
      if (currentUser.value?.id === id) {
        currentUser.value = updatedUser
      }

      return updatedUser
    } catch (err) {
      error.value = err instanceof Error ? err.message : '更新用户失败'
      throw err
    } finally {
      isLoading.value = false
    }
  }

  const deleteUser = async (id: string) => {
    try {
      isLoading.value = true
      error.value = null

      await userApi.deleteUser(id)

      // 从本地缓存中移除
      const index = users.value.findIndex(u => u.id === id)
      if (index !== -1) {
        users.value.splice(index, 1)
        pagination.value.total -= 1
      }

      return true
    } catch (err) {
      error.value = err instanceof Error ? err.message : '删除用户失败'
      throw err
    } finally {
      isLoading.value = false
    }
  }

  const setCurrentUser = (user: User | null) => {
    currentUser.value = user
  }

  const clearError = () => {
    error.value = null
  }

  const resetState = () => {
    users.value = []
    currentUser.value = null
    error.value = null
    pagination.value = { page: 1, pageSize: 20, total: 0 }
  }

  return {
    // State
    users,
    currentUser,
    isLoading,
    error,
    pagination,

    // Getters
    totalUsers,
    activeUsers,
    userById,
    isCurrentUserAdmin,

    // Actions
    fetchUsers,
    fetchUserById,
    createUser,
    updateUser,
    deleteUser,
    setCurrentUser,
    clearError,
    resetState
  }
})
```

**Store特性**:
- ✅ 完整的CRUD操作
- ✅ 响应式状态管理
- ✅ 错误处理机制
- ✅ 本地缓存同步
- ✅ TypeScript类型安全

**使用示例**:
```vue
<script setup lang="ts">
import { useUserStore } from '@/stores/modules/user'

const userStore = useUserStore()

// 获取用户列表
await userStore.fetchUsers()

// 创建新用户
await userStore.createUser({
  name: '张三',
  email: 'zhangsan@example.com'
})

// 更新用户
await userStore.updateUser('user-id', {
  name: '李四'
})
</script>
```

</details>

---

## 🛠️ 工具函数示例

> 🔧 **工具集合**: 常用的格式化、验证、工具函数，提高开发效率

### 📊 格式化工具函数

<details>
<summary><strong>📝 format.ts - 数据格式化工具</strong></summary>

> **功能特性**: 日期、文件大小、数字、货币、文本格式化  
> **使用场景**: 数据展示、用户界面、报表生成  
> **复杂度**: ⭐⭐

```typescript
// utils/helpers/format.ts

/**
 * 格式化日期
 */
export const formatDate = (date: Date | string | number, format = 'YYYY-MM-DD'): string => {
  const d = new Date(date)
  
  if (isNaN(d.getTime())) {
    return '无效日期'
  }

  const year = d.getFullYear()
  const month = String(d.getMonth() + 1).padStart(2, '0')
  const day = String(d.getDate()).padStart(2, '0')
  const hours = String(d.getHours()).padStart(2, '0')
  const minutes = String(d.getMinutes()).padStart(2, '0')
  const seconds = String(d.getSeconds()).padStart(2, '0')

  const formatMap: Record<string, string> = {
    'YYYY': year.toString(),
    'MM': month,
    'DD': day,
    'HH': hours,
    'mm': minutes,
    'ss': seconds
  }

  return format.replace(/YYYY|MM|DD|HH|mm|ss/g, match => formatMap[match])
}

/**
 * 格式化文件大小
 */
export const formatFileSize = (bytes: number): string => {
  if (bytes === 0) return '0 B'

  const k = 1024
  const sizes = ['B', 'KB', 'MB', 'GB', 'TB']
  const i = Math.floor(Math.log(bytes) / Math.log(k))

  return `${parseFloat((bytes / Math.pow(k, i)).toFixed(2))} ${sizes[i]}`
}

/**
 * 格式化数字
 */
export const formatNumber = (num: number, options?: Intl.NumberFormatOptions): string => {
  return new Intl.NumberFormat('zh-CN', options).format(num)
}

/**
 * 格式化货币
 */
export const formatCurrency = (amount: number, currency = 'CNY'): string => {
  return new Intl.NumberFormat('zh-CN', {
    style: 'currency',
    currency
  }).format(amount)
}

/**
 * 格式化百分比
 */
export const formatPercentage = (value: number, decimals = 2): string => {
  return `${(value * 100).toFixed(decimals)}%`
}

/**
 * 截断文本
 */
export const truncateText = (text: string, maxLength: number, suffix = '...'): string => {
  if (text.length <= maxLength) return text
  return text.slice(0, maxLength - suffix.length) + suffix
}

/**
 * 首字母大写
 */
export const capitalize = (str: string): string => {
  return str.charAt(0).toUpperCase() + str.slice(1).toLowerCase()
}

/**
 * 驼峰转短横线
 */
export const camelToKebab = (str: string): string => {
  return str.replace(/([a-z0-9])([A-Z])/g, '$1-$2').toLowerCase()
}

/**
 * 短横线转驼峰
 */
export const kebabToCamel = (str: string): string => {
  return str.replace(/-([a-z])/g, (_, letter) => letter.toUpperCase())
}
```

**使用示例**:
```typescript
// 日期格式化
formatDate(new Date(), 'YYYY-MM-DD HH:mm:ss') // "2024-03-01 14:30:00"

// 文件大小格式化
formatFileSize(1024 * 1024) // "1.00 MB"

// 货币格式化
formatCurrency(12345.67) // "¥12,345.67"

// 文本截断
truncateText('这是一段很长的文本内容', 10) // "这是一段很长的文..."
```

</details>

### ✅ 验证工具函数

<details>
<summary><strong>🔍 validation.ts - 数据验证工具</strong></summary>

> **功能特性**: 邮箱、手机号、身份证、URL、密码强度验证  
> **使用场景**: 表单验证、数据校验、用户输入检查  
> **复杂度**: ⭐⭐⭐

```typescript
// utils/helpers/validation.ts

/**
 * 邮箱验证
 */
export const isValidEmail = (email: string): boolean => {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
  return emailRegex.test(email)
}

/**
 * 手机号验证（中国大陆）
 */
export const isValidPhone = (phone: string): boolean => {
  const phoneRegex = /^1[3-9]\d{9}$/
  return phoneRegex.test(phone)
}

/**
 * 身份证号验证（中国大陆）
 */
export const isValidIdCard = (idCard: string): boolean => {
  const idCardRegex = /(^\d{15}$)|(^\d{18}$)|(^\d{17}(\d|X|x)$)/
  return idCardRegex.test(idCard)
}

/**
 * URL验证
 */
export const isValidUrl = (url: string): boolean => {
  try {
    new URL(url)
    return true
  } catch {
    return false
  }
}

/**
 * 密码强度验证
 */
export const validatePasswordStrength = (password: string): {
  isValid: boolean
  score: number
  feedback: string[]
} => {
  const feedback: string[] = []
  let score = 0

  if (password.length < 8) {
    feedback.push('密码长度至少8位')
  } else {
    score += 1
  }

  if (!/[a-z]/.test(password)) {
    feedback.push('密码需包含小写字母')
  } else {
    score += 1
  }

  if (!/[A-Z]/.test(password)) {
    feedback.push('密码需包含大写字母')
  } else {
    score += 1
  }

  if (!/\d/.test(password)) {
    feedback.push('密码需包含数字')
  } else {
    score += 1
  }

  if (!/[!@#$%^&*(),.?":{}|<>]/.test(password)) {
    feedback.push('密码需包含特殊字符')
  } else {
    score += 1
  }

  return {
    isValid: score >= 4,
    score,
    feedback
  }
}

/**
 * 表单验证规则
 */
export const validationRules = {
  required: (value: any) => {
    if (Array.isArray(value)) return value.length > 0
    return value !== null && value !== undefined && value !== ''
  },

  minLength: (min: number) => (value: string) => {
    return value.length >= min
  },

  maxLength: (max: number) => (value: string) => {
    return value.length <= max
  },

  min: (min: number) => (value: number) => {
    return value >= min
  },

  max: (max: number) => (value: number) => {
    return value <= max
  },

  pattern: (regex: RegExp) => (value: string) => {
    return regex.test(value)
  }
}
```

**使用示例**:
```typescript
// 邮箱验证
isValidEmail('user@example.com') // true

// 密码强度检查
const result = validatePasswordStrength('MyPassword123!')
console.log(result.isValid) // true
console.log(result.score) // 5
console.log(result.feedback) // []

// 表单验证规则
const rules = {
  username: [validationRules.required, validationRules.minLength(3)],
  email: [validationRules.required, (value) => isValidEmail(value)]
}
```

</details>

---

## 🔧 配置文件模板集合

> ⚙️ **配置模板**: 生产就绪的配置文件模板，开箱即用

### ⚡ Vite配置模板

<details>
<summary><strong>📄 vite.config.ts - 构建工具配置</strong></summary>

> **功能特性**: 路径别名、CSS预处理、构建优化、开发服务器  
> **使用场景**: 项目构建、开发环境、生产部署  
> **复杂度**: ⭐⭐⭐

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import { resolve } from 'path'

export default defineConfig({
  plugins: [vue()],
  
  resolve: {
    alias: {
      '@': resolve(__dirname, 'src'),
      '@/components': resolve(__dirname, 'src/components'),
      '@/utils': resolve(__dirname, 'src/utils'),
      '@/stores': resolve(__dirname, 'src/stores'),
      '@/types': resolve(__dirname, 'src/types'),
      '@/services': resolve(__dirname, 'src/services'),
      '@/assets': resolve(__dirname, 'src/assets')
    }
  },

  css: {
    preprocessorOptions: {
      scss: {
        additionalData: `@import "@/assets/styles/variables.scss";`
      }
    }
  },

  build: {
    target: 'es2015',
    outDir: 'dist',
    assetsDir: 'assets',
    sourcemap: false,
    minify: 'terser',
    
    rollupOptions: {
      output: {
        chunkFileNames: 'js/[name]-[hash].js',
        entryFileNames: 'js/[name]-[hash].js',
        assetFileNames: '[ext]/[name]-[hash].[ext]',
        
        manualChunks: {
          vue: ['vue', 'vue-router', 'pinia'],
          vendor: ['axios', 'lodash-es'],
          ui: ['element-plus']
        }
      }
    },

    terserOptions: {
      compress: {
        drop_console: true,
        drop_debugger: true
      }
    }
  },

  server: {
    port: 3000,
    open: true,
    cors: true,
    
    proxy: {
      '/api': {
        target: 'http://localhost:8080',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, '')
      }
    }
  },

  preview: {
    port: 4173,
    open: true
  }
})
```

**配置特性**:
- ✅ 路径别名配置
- ✅ CSS预处理器支持
- ✅ 代码分割优化
- ✅ 开发服务器代理
- ✅ 构建性能优化

</details>

### 📘 TypeScript配置模板

<details>
<summary><strong>📄 tsconfig.json - TypeScript配置</strong></summary>

> **功能特性**: 严格类型检查、路径映射、编译选项  
> **使用场景**: 类型检查、代码编译、IDE支持  
> **复杂度**: ⭐⭐

```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,

    /* Bundler mode */
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "preserve",

    /* Linting */
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,

    /* Path mapping */
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"],
      "@/components/*": ["src/components/*"],
      "@/utils/*": ["src/utils/*"],
      "@/stores/*": ["src/stores/*"],
      "@/types/*": ["src/types/*"],
      "@/services/*": ["src/services/*"],
      "@/assets/*": ["src/assets/*"]
    }
  },
  "include": [
    "src/**/*.ts",
    "src/**/*.d.ts",
    "src/**/*.tsx",
    "src/**/*.vue"
  ],
  "exclude": [
    "node_modules",
    "dist"
  ],
  "references": [
    {
      "path": "./tsconfig.node.json"
    }
  ]
}
```

**配置特性**:
- ✅ 严格类型检查
- ✅ 路径映射支持
- ✅ 现代ES特性
- ✅ Vue文件支持
- ✅ 开发工具集成

</details>

### Package.json脚本模板

```json
{
  "name": "vue-project-example",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vue-tsc && vite build",
    "preview": "vite preview",
    "lint": "eslint . --fix",
    "lint:check": "eslint .",
    "format": "prettier --write src/",
    "format:check": "prettier --check src/",
    "type-check": "vue-tsc --noEmit",
    "test:unit": "vitest",
    "test:unit:run": "vitest run",
    "test:e2e": "playwright test",
    "test:coverage": "vitest run --coverage",
    "prepare": "husky install",
    "commit": "git-cz"
  },
  "dependencies": {
    "vue": "^3.3.0",
    "vue-router": "^4.2.0",
    "pinia": "^2.1.0",
    "axios": "^1.4.0"
  },
  "devDependencies": {
    "@eslint/js": "^9.0.0",
    "@typescript-eslint/eslint-plugin": "^7.0.0",
    "@typescript-eslint/parser": "^7.0.0",
    "@vitejs/plugin-vue": "^5.0.0",
    "@vue/eslint-config-prettier": "^9.0.0",
    "@vue/eslint-config-typescript": "^13.0.0",
    "@vue/test-utils": "^2.4.0",
    "@vue/tsconfig": "^0.5.0",
    "eslint": "^9.0.0",
    "eslint-plugin-import": "^2.29.0",
    "eslint-plugin-vue": "^9.20.0",
    "globals": "^14.0.0",
    "prettier": "^3.2.0",
    "typescript": "^5.3.0",
    "vite": "^5.0.0",
    "vitest": "^1.2.0",
    "vue-tsc": "^1.8.0"
  }
}
```

---

## 📚 代码片段库

### VS Code代码片段

创建 `.vscode/vue.code-snippets`:

```json
{
  "Vue Component with TypeScript": {
    "prefix": "vue-ts",
    "body": [
      "<template>",
      "  <div class=\"${1:component-name}\">",
      "    $2",
      "  </div>",
      "</template>",
      "",
      "<script setup lang=\"ts\">",
      "import { ref, computed } from 'vue'",
      "",
      "interface Props {",
      "  $3",
      "}",
      "",
      "interface Emits {",
      "  $4",
      "}",
      "",
      "const props = defineProps<Props>()",
      "const emit = defineEmits<Emits>()",
      "",
      "$5",
      "</script>",
      "",
      "<style scoped>",
      ".${1:component-name} {",
      "  $6",
      "}",
      "</style>"
    ],
    "description": "Create a Vue component with TypeScript"
  },

  "Pinia Store": {
    "prefix": "pinia-store",
    "body": [
      "import { defineStore } from 'pinia'",
      "import { ref, computed } from 'vue'",
      "",
      "export const use${1:Store}Store = defineStore('${2:store}', () => {",
      "  // State",
      "  const ${3:data} = ref($4)",
      "  const isLoading = ref(false)",
      "  const error = ref<string | null>(null)",
      "",
      "  // Getters",
      "  const ${5:computed} = computed(() => {",
      "    return $6",
      "  })",
      "",
      "  // Actions",
      "  const ${7:action} = async () => {",
      "    try {",
      "      isLoading.value = true",
      "      error.value = null",
      "      $8",
      "    } catch (err) {",
      "      error.value = err instanceof Error ? err.message : '操作失败'",
      "      throw err",
      "    } finally {",
      "      isLoading.value = false",
      "    }",
      "  }",
      "",
      "  return {",
      "    ${3:data},",
      "    isLoading,",
      "    error,",
      "    ${5:computed},",
      "    ${7:action}",
      "  }",
      "})"
    ],
    "description": "Create a Pinia store"
  },

  "Composable Function": {
    "prefix": "use-composable",
    "body": [
      "import { ref, computed, onMounted, onUnmounted } from 'vue'",
      "",
      "export function use${1:Composable}() {",
      "  const ${2:state} = ref($3)",
      "  const isLoading = ref(false)",
      "  const error = ref<string | null>(null)",
      "",
      "  const ${4:computed} = computed(() => {",
      "    return $5",
      "  })",
      "",
      "  const ${6:method} = async () => {",
      "    try {",
      "      isLoading.value = true",
      "      error.value = null",
      "      $7",
      "    } catch (err) {",
      "      error.value = err instanceof Error ? err.message : '操作失败'",
      "    } finally {",
      "      isLoading.value = false",
      "    }",
      "  }",
      "",
      "  onMounted(() => {",
      "    $8",
      "  })",
      "",
      "  onUnmounted(() => {",
      "    $9",
      "  })",
      "",
      "  return {",
      "    ${2:state},",
      "    isLoading,",
      "    error,",
      "    ${4:computed},",
      "    ${6:method}",
      "  }",
      "}"
    ],
    "description": "Create a composable function"
  }
}
```

---

这个示例文档提供了完整的项目结构、组件示例、Store示例、工具函数和配置文件模板，开发者可以直接复制使用，快速搭建符合规范的Vue项目。