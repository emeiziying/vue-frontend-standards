# CSS和样式编写规范

## 基础CSS规范

### 选择器命名规范

#### BEM命名方法论
采用BEM (Block Element Modifier) 命名规范，确保CSS类名的可读性和可维护性。

**基本语法：**
```css
.block {}
.block__element {}
.block--modifier {}
.block__element--modifier {}
```

**命名规则：**
- 使用小写字母和连字符
- 避免使用缩写，保持语义化
- 块名应该描述其用途，而不是外观

**示例：**
```css
/* 正确的BEM命名 */
.user-card {}
.user-card__avatar {}
.user-card__name {}
.user-card__bio {}
.user-card--featured {}
.user-card__avatar--large {}

/* 错误的命名 */
.red-button {}  /* 基于外观 */
.uc {}          /* 缩写不清晰 */
.userCard {}    /* 驼峰命名 */
```

#### 组件级CSS命名
在Vue组件中，推荐使用scoped样式配合BEM命名：

```vue
<template>
  <div class="product-list">
    <div class="product-list__item" v-for="item in items">
      <h3 class="product-list__title">{{ item.title }}</h3>
      <p class="product-list__price product-list__price--discount">
        {{ item.price }}
      </p>
    </div>
  </div>
</template>

<style scoped>
.product-list {
  display: grid;
  gap: 1rem;
}

.product-list__item {
  padding: 1rem;
  border: 1px solid #e5e7eb;
  border-radius: 0.5rem;
}

.product-list__title {
  font-size: 1.25rem;
  font-weight: 600;
  margin-bottom: 0.5rem;
}

.product-list__price {
  color: #374151;
  font-weight: 500;
}

.product-list__price--discount {
  color: #dc2626;
  text-decoration: line-through;
}
</style>
```

### 样式组织和结构标准

#### CSS属性排序
按照以下顺序组织CSS属性，提高代码可读性：

1. **定位属性** - position, top, right, bottom, left, z-index
2. **盒模型** - display, flex, grid, width, height, margin, padding, border
3. **字体和文本** - font, color, text-align, line-height
4. **背景和装饰** - background, box-shadow, border-radius
5. **动画和变换** - transform, transition, animation

```css
.example-class {
  /* 定位 */
  position: relative;
  top: 0;
  left: 0;
  z-index: 10;
  
  /* 盒模型 */
  display: flex;
  flex-direction: column;
  width: 100%;
  height: auto;
  margin: 1rem 0;
  padding: 1rem;
  border: 1px solid #e5e7eb;
  
  /* 字体和文本 */
  font-size: 1rem;
  font-weight: 400;
  color: #374151;
  text-align: left;
  line-height: 1.5;
  
  /* 背景和装饰 */
  background-color: #ffffff;
  border-radius: 0.5rem;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  
  /* 动画和变换 */
  transform: translateY(0);
  transition: all 0.2s ease-in-out;
}
```

#### CSS文件组织结构
```
styles/
├── base/
│   ├── reset.css          # CSS重置
│   ├── typography.css     # 字体样式
│   └── variables.css      # CSS变量
├── components/
│   ├── buttons.css        # 按钮组件样式
│   ├── forms.css          # 表单组件样式
│   └── cards.css          # 卡片组件样式
├── layout/
│   ├── header.css         # 头部布局
│   ├── sidebar.css        # 侧边栏布局
│   └── footer.css         # 底部布局
├── pages/
│   ├── home.css           # 首页样式
│   └── profile.css        # 个人资料页样式
└── utilities/
    ├── spacing.css        # 间距工具类
    └── colors.css         # 颜色工具类
```

#### CSS变量使用规范
使用CSS自定义属性定义设计系统的核心值：

```css
:root {
  /* 颜色系统 */
  --color-primary: #3b82f6;
  --color-primary-dark: #1d4ed8;
  --color-primary-light: #93c5fd;
  
  --color-secondary: #6b7280;
  --color-success: #10b981;
  --color-warning: #f59e0b;
  --color-error: #ef4444;
  
  /* 字体系统 */
  --font-family-sans: 'Inter', system-ui, sans-serif;
  --font-family-mono: 'Fira Code', monospace;
  
  --font-size-xs: 0.75rem;
  --font-size-sm: 0.875rem;
  --font-size-base: 1rem;
  --font-size-lg: 1.125rem;
  --font-size-xl: 1.25rem;
  
  /* 间距系统 */
  --spacing-xs: 0.25rem;
  --spacing-sm: 0.5rem;
  --spacing-md: 1rem;
  --spacing-lg: 1.5rem;
  --spacing-xl: 2rem;
  
  /* 阴影系统 */
  --shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.05);
  --shadow-md: 0 4px 6px rgba(0, 0, 0, 0.1);
  --shadow-lg: 0 10px 15px rgba(0, 0, 0, 0.1);
  
  /* 边框半径 */
  --radius-sm: 0.25rem;
  --radius-md: 0.5rem;
  --radius-lg: 1rem;
  
  /* 断点 */
  --breakpoint-sm: 640px;
  --breakpoint-md: 768px;
  --breakpoint-lg: 1024px;
  --breakpoint-xl: 1280px;
}
```

### 响应式设计指南

#### 移动优先设计原则
采用移动优先的响应式设计方法：

```css
/* 基础样式（移动端） */
.container {
  width: 100%;
  padding: 1rem;
}

.grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 1rem;
}

/* 平板端 */
@media (min-width: 768px) {
  .container {
    max-width: 768px;
    margin: 0 auto;
    padding: 2rem;
  }
  
  .grid {
    grid-template-columns: repeat(2, 1fr);
    gap: 2rem;
  }
}

/* 桌面端 */
@media (min-width: 1024px) {
  .container {
    max-width: 1024px;
    padding: 3rem;
  }
  
  .grid {
    grid-template-columns: repeat(3, 1fr);
    gap: 3rem;
  }
}

/* 大屏幕 */
@media (min-width: 1280px) {
  .container {
    max-width: 1280px;
  }
  
  .grid {
    grid-template-columns: repeat(4, 1fr);
  }
}
```

#### 响应式断点系统
定义标准的响应式断点：

```css
/* 断点定义 */
@custom-media --sm (min-width: 640px);
@custom-media --md (min-width: 768px);
@custom-media --lg (min-width: 1024px);
@custom-media --xl (min-width: 1280px);
@custom-media --2xl (min-width: 1536px);

/* 使用示例 */
.responsive-text {
  font-size: 1rem;
}

@media (--md) {
  .responsive-text {
    font-size: 1.125rem;
  }
}

@media (--lg) {
  .responsive-text {
    font-size: 1.25rem;
  }
}
```

#### 响应式图片和媒体
```css
/* 响应式图片 */
.responsive-image {
  width: 100%;
  height: auto;
  object-fit: cover;
}

/* 响应式视频 */
.responsive-video {
  position: relative;
  width: 100%;
  height: 0;
  padding-bottom: 56.25%; /* 16:9 宽高比 */
}

.responsive-video iframe {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
```

#### 容器查询（现代浏览器）
```css
.card-container {
  container-type: inline-size;
}

.card {
  padding: 1rem;
}

@container (min-width: 300px) {
  .card {
    padding: 2rem;
    display: flex;
    gap: 1rem;
  }
}

@container (min-width: 500px) {
  .card {
    padding: 3rem;
    gap: 2rem;
  }
}
```

### CSS性能优化

#### 选择器性能
```css
/* 高效选择器 */
.button {}
.card__title {}
#header {}

/* 避免的低效选择器 */
div > div > div > .title {}  /* 过深的嵌套 */
* {}                         /* 通配符选择器 */
[class*="col-"] {}          /* 复杂的属性选择器 */
```

#### CSS文件优化
```css
/* 使用简写属性 */
.optimized {
  margin: 1rem 2rem 1rem 2rem;  /* 可简化 */
  margin: 1rem 2rem;            /* 简化后 */
  
  background: url('image.jpg') no-repeat center center / cover;
}

/* 避免重复声明 */
.button {
  padding: 1rem 2rem;
  border-radius: 0.5rem;
  transition: all 0.2s ease;
}

.button--primary {
  background-color: var(--color-primary);
  color: white;
}

.button--secondary {
  background-color: var(--color-secondary);
  color: white;
}
```
#
# TailwindCSS使用规范

### 原子化CSS类使用指南

#### 基本原则
TailwindCSS采用原子化CSS的方法，每个类只负责一个样式属性。遵循以下原则：

1. **优先使用Tailwind类**：能用Tailwind类实现的样式，避免写自定义CSS
2. **保持类名简洁**：合理组织类名，避免过长的类名列表
3. **语义化组合**：将相关的类名组合在一起，提高可读性

#### 类名组织规范
按照以下顺序组织Tailwind类名：

```vue
<template>
  <!-- 布局 → 尺寸 → 间距 → 字体 → 颜色 → 边框 → 背景 → 效果 → 交互 -->
  <div class="
    flex flex-col items-center justify-center
    w-full max-w-md h-auto min-h-screen
    p-6 mx-auto my-8
    text-lg font-semibold text-center leading-relaxed
    text-gray-800 
    border border-gray-200 rounded-lg
    bg-white bg-opacity-95
    shadow-lg hover:shadow-xl
    transition-all duration-300 ease-in-out
    cursor-pointer hover:scale-105
  ">
    内容区域
  </div>
</template>
```

#### 响应式类使用
```vue
<template>
  <!-- 移动优先的响应式设计 -->
  <div class="
    grid grid-cols-1 gap-4
    sm:grid-cols-2 sm:gap-6
    md:grid-cols-3 md:gap-8
    lg:grid-cols-4 lg:gap-10
    xl:grid-cols-5 xl:gap-12
  ">
    <div class="
      p-4 
      sm:p-6 
      md:p-8
      text-sm 
      sm:text-base 
      md:text-lg
    ">
      响应式卡片
    </div>
  </div>
</template>
```

#### 状态变体使用
```vue
<template>
  <!-- 交互状态 -->
  <button class="
    px-6 py-3
    text-white font-medium
    bg-blue-600 
    hover:bg-blue-700 
    focus:bg-blue-700 
    active:bg-blue-800
    disabled:bg-gray-400 disabled:cursor-not-allowed
    border border-transparent 
    rounded-md
    shadow-sm 
    hover:shadow-md 
    focus:shadow-md
    transition-all duration-200
    focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2
  ">
    提交按钮
  </button>

  <!-- 暗色模式支持 -->
  <div class="
    bg-white text-gray-900
    dark:bg-gray-900 dark:text-white
    border border-gray-200 
    dark:border-gray-700
  ">
    支持暗色模式的内容
  </div>
</template>
```

### 自定义主题配置标准

#### Tailwind配置文件结构
```javascript
// tailwind.config.js
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{vue,js,ts,jsx,tsx}",
  ],
  darkMode: 'class', // 或 'media'
  theme: {
    extend: {
      // 自定义颜色系统
      colors: {
        primary: {
          50: '#eff6ff',
          100: '#dbeafe',
          200: '#bfdbfe',
          300: '#93c5fd',
          400: '#60a5fa',
          500: '#3b82f6',  // 主色调
          600: '#2563eb',
          700: '#1d4ed8',
          800: '#1e40af',
          900: '#1e3a8a',
          950: '#172554',
        },
        secondary: {
          50: '#f9fafb',
          100: '#f3f4f6',
          200: '#e5e7eb',
          300: '#d1d5db',
          400: '#9ca3af',
          500: '#6b7280',  // 次要色调
          600: '#4b5563',
          700: '#374151',
          800: '#1f2937',
          900: '#111827',
          950: '#030712',
        },
        success: '#10b981',
        warning: '#f59e0b',
        error: '#ef4444',
        info: '#06b6d4',
      },
      
      // 自定义字体
      fontFamily: {
        sans: ['Inter', 'system-ui', 'sans-serif'],
        serif: ['Georgia', 'serif'],
        mono: ['Fira Code', 'monospace'],
        display: ['Poppins', 'sans-serif'],
      },
      
      // 自定义间距
      spacing: {
        '18': '4.5rem',
        '88': '22rem',
        '128': '32rem',
      },
      
      // 自定义断点
      screens: {
        'xs': '475px',
        '3xl': '1600px',
      },
      
      // 自定义阴影
      boxShadow: {
        'soft': '0 2px 15px -3px rgba(0, 0, 0, 0.07), 0 10px 20px -2px rgba(0, 0, 0, 0.04)',
        'strong': '0 10px 25px -5px rgba(0, 0, 0, 0.1), 0 20px 40px -5px rgba(0, 0, 0, 0.04)',
      },
      
      // 自定义动画
      animation: {
        'fade-in': 'fadeIn 0.5s ease-in-out',
        'slide-up': 'slideUp 0.3s ease-out',
        'bounce-gentle': 'bounceGentle 2s infinite',
      },
      
      keyframes: {
        fadeIn: {
          '0%': { opacity: '0' },
          '100%': { opacity: '1' },
        },
        slideUp: {
          '0%': { transform: 'translateY(10px)', opacity: '0' },
          '100%': { transform: 'translateY(0)', opacity: '1' },
        },
        bounceGentle: {
          '0%, 100%': { transform: 'translateY(-5%)' },
          '50%': { transform: 'translateY(0)' },
        },
      },
    },
  },
  plugins: [
    require('@tailwindcss/forms'),
    require('@tailwindcss/typography'),
    require('@tailwindcss/aspect-ratio'),
  ],
}
```

#### CSS变量与Tailwind集成
```css
/* src/styles/variables.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  :root {
    --color-primary: theme('colors.primary.500');
    --color-secondary: theme('colors.secondary.500');
    --font-family-primary: theme('fontFamily.sans');
    --spacing-section: theme('spacing.20');
  }
  
  html {
    font-family: var(--font-family-primary);
  }
  
  body {
    @apply bg-gray-50 text-gray-900 antialiased;
  }
}

@layer components {
  /* 自定义组件类 */
  .btn {
    @apply px-4 py-2 font-medium rounded-md transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-offset-2;
  }
  
  .btn-primary {
    @apply btn bg-primary-600 text-white hover:bg-primary-700 focus:ring-primary-500;
  }
  
  .btn-secondary {
    @apply btn bg-secondary-600 text-white hover:bg-secondary-700 focus:ring-secondary-500;
  }
  
  .card {
    @apply bg-white rounded-lg shadow-md border border-gray-200 overflow-hidden;
  }
  
  .input {
    @apply w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm placeholder-gray-400 focus:outline-none focus:ring-2 focus:ring-primary-500 focus:border-primary-500;
  }
}

@layer utilities {
  /* 自定义工具类 */
  .text-balance {
    text-wrap: balance;
  }
  
  .scrollbar-hide {
    -ms-overflow-style: none;
    scrollbar-width: none;
  }
  
  .scrollbar-hide::-webkit-scrollbar {
    display: none;
  }
}
```

### 组件样式最佳实践

#### Vue组件中的Tailwind使用
```vue
<template>
  <div class="user-profile">
    <!-- 头部区域 -->
    <header class="user-profile__header">
      <div class="flex items-center space-x-4 p-6 bg-gradient-to-r from-primary-500 to-primary-600">
        <img 
          :src="user.avatar" 
          :alt="user.name"
          class="w-16 h-16 rounded-full border-4 border-white shadow-lg object-cover"
        >
        <div class="flex-1 min-w-0">
          <h1 class="text-2xl font-bold text-white truncate">
            {{ user.name }}
          </h1>
          <p class="text-primary-100 text-sm">
            {{ user.title }}
          </p>
        </div>
      </div>
    </header>

    <!-- 内容区域 -->
    <main class="user-profile__content">
      <div class="max-w-4xl mx-auto p-6 space-y-8">
        <!-- 统计卡片 -->
        <section class="grid grid-cols-1 md:grid-cols-3 gap-6">
          <div 
            v-for="stat in stats" 
            :key="stat.label"
            class="card p-6 text-center hover:shadow-lg transition-shadow duration-300"
          >
            <div class="text-3xl font-bold text-primary-600 mb-2">
              {{ stat.value }}
            </div>
            <div class="text-sm text-gray-600 uppercase tracking-wide">
              {{ stat.label }}
            </div>
          </div>
        </section>

        <!-- 详细信息 -->
        <section class="card">
          <div class="p-6 border-b border-gray-200">
            <h2 class="text-xl font-semibold text-gray-900">
              个人信息
            </h2>
          </div>
          <div class="p-6 space-y-4">
            <div 
              v-for="field in profileFields" 
              :key="field.key"
              class="flex flex-col sm:flex-row sm:items-center"
            >
              <label class="w-full sm:w-32 text-sm font-medium text-gray-700 mb-1 sm:mb-0">
                {{ field.label }}
              </label>
              <div class="flex-1 text-gray-900">
                {{ user[field.key] }}
              </div>
            </div>
          </div>
        </section>
      </div>
    </main>
  </div>
</template>

<script setup lang="ts">
interface User {
  name: string
  title: string
  avatar: string
  email: string
  phone: string
  location: string
}

interface Stat {
  label: string
  value: string | number
}

interface ProfileField {
  key: keyof User
  label: string
}

const user = ref<User>({
  name: '张三',
  title: '前端开发工程师',
  avatar: '/avatars/user.jpg',
  email: 'zhangsan@example.com',
  phone: '+86 138 0013 8000',
  location: '北京市朝阳区'
})

const stats = ref<Stat[]>([
  { label: '项目数量', value: 12 },
  { label: '代码提交', value: '1.2k' },
  { label: '工作年限', value: '3年' }
])

const profileFields = ref<ProfileField[]>([
  { key: 'email', label: '邮箱' },
  { key: 'phone', label: '电话' },
  { key: 'location', label: '位置' }
])
</script>

<style scoped>
/* 只在必要时使用自定义CSS */
.user-profile__header {
  /* 复杂的渐变效果可能需要自定义CSS */
  background: linear-gradient(135deg, theme('colors.primary.500') 0%, theme('colors.primary.600') 100%);
}

/* 使用@apply指令复用Tailwind类 */
.user-profile__content {
  @apply min-h-screen bg-gray-50;
}
</style>
```

#### 组件库样式规范
```vue
<!-- BaseButton.vue -->
<template>
  <button 
    :class="buttonClasses"
    :disabled="disabled"
    @click="$emit('click', $event)"
  >
    <Icon v-if="icon" :name="icon" class="w-4 h-4" />
    <span v-if="$slots.default">
      <slot />
    </span>
  </button>
</template>

<script setup lang="ts">
type ButtonVariant = 'primary' | 'secondary' | 'outline' | 'ghost'
type ButtonSize = 'sm' | 'md' | 'lg'

interface Props {
  variant?: ButtonVariant
  size?: ButtonSize
  disabled?: boolean
  icon?: string
}

const props = withDefaults(defineProps<Props>(), {
  variant: 'primary',
  size: 'md',
  disabled: false
})

const emit = defineEmits<{
  click: [event: MouseEvent]
}>()

const buttonClasses = computed(() => {
  const baseClasses = 'inline-flex items-center justify-center font-medium rounded-md transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-offset-2'
  
  const variantClasses = {
    primary: 'bg-primary-600 text-white hover:bg-primary-700 focus:ring-primary-500 disabled:bg-gray-400',
    secondary: 'bg-secondary-600 text-white hover:bg-secondary-700 focus:ring-secondary-500 disabled:bg-gray-400',
    outline: 'border-2 border-primary-600 text-primary-600 hover:bg-primary-50 focus:ring-primary-500 disabled:border-gray-300 disabled:text-gray-400',
    ghost: 'text-primary-600 hover:bg-primary-50 focus:ring-primary-500 disabled:text-gray-400'
  }
  
  const sizeClasses = {
    sm: 'px-3 py-1.5 text-sm gap-1.5',
    md: 'px-4 py-2 text-base gap-2',
    lg: 'px-6 py-3 text-lg gap-2.5'
  }
  
  return [
    baseClasses,
    variantClasses[props.variant],
    sizeClasses[props.size],
    props.disabled && 'cursor-not-allowed'
  ].filter(Boolean).join(' ')
})
</script>
```

#### 条件样式和动态类
```vue
<template>
  <div class="notification-list">
    <div 
      v-for="notification in notifications"
      :key="notification.id"
      :class="getNotificationClasses(notification)"
    >
      <Icon :name="getNotificationIcon(notification.type)" />
      <div class="flex-1 min-w-0">
        <p class="font-medium truncate">{{ notification.title }}</p>
        <p class="text-sm opacity-75 line-clamp-2">{{ notification.message }}</p>
      </div>
      <button 
        @click="dismissNotification(notification.id)"
        class="ml-4 p-1 rounded-full hover:bg-black hover:bg-opacity-10 transition-colors"
      >
        <Icon name="x" class="w-4 h-4" />
      </button>
    </div>
  </div>
</template>

<script setup lang="ts">
type NotificationType = 'success' | 'warning' | 'error' | 'info'

interface Notification {
  id: string
  type: NotificationType
  title: string
  message: string
  read: boolean
}

const notifications = ref<Notification[]>([])

const getNotificationClasses = (notification: Notification) => {
  const baseClasses = 'flex items-start p-4 border-l-4 bg-white shadow-sm rounded-r-md mb-3 transition-all duration-200'
  
  const typeClasses = {
    success: 'border-green-500 bg-green-50 text-green-800',
    warning: 'border-yellow-500 bg-yellow-50 text-yellow-800',
    error: 'border-red-500 bg-red-50 text-red-800',
    info: 'border-blue-500 bg-blue-50 text-blue-800'
  }
  
  const readClasses = notification.read 
    ? 'opacity-60' 
    : 'shadow-md hover:shadow-lg'
  
  return `${baseClasses} ${typeClasses[notification.type]} ${readClasses}`
}

const getNotificationIcon = (type: NotificationType) => {
  const icons = {
    success: 'check-circle',
    warning: 'exclamation-triangle',
    error: 'x-circle',
    info: 'information-circle'
  }
  return icons[type]
}
</script>
```

### Tailwind与传统CSS的混合使用

#### 何时使用自定义CSS
```vue
<template>
  <div class="complex-animation">
    <!-- 简单样式使用Tailwind -->
    <div class="flex items-center justify-between p-4 bg-white rounded-lg shadow-md">
      <h3 class="text-lg font-semibold text-gray-900">标题</h3>
      
      <!-- 复杂动画使用自定义CSS -->
      <div class="loading-spinner">
        <div class="spinner-ring"></div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* 复杂的动画效果 */
.loading-spinner {
  @apply relative w-8 h-8;
}

.spinner-ring {
  @apply absolute inset-0 border-2 border-gray-200 rounded-full;
  border-top-color: theme('colors.primary.500');
  animation: spin 1s linear infinite;
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}

/* 复杂的伪元素样式 */
.complex-animation::before {
  content: '';
  @apply absolute inset-0 bg-gradient-to-r from-transparent via-white to-transparent opacity-0;
  transform: translateX(-100%);
  animation: shimmer 2s infinite;
}

@keyframes shimmer {
  0% {
    transform: translateX(-100%);
    opacity: 0;
  }
  50% {
    opacity: 1;
  }
  100% {
    transform: translateX(100%);
    opacity: 0;
  }
}
</style>
```

#### 性能优化建议
```javascript
// 生产环境优化配置
module.exports = {
  content: [
    "./index.html",
    "./src/**/*.{vue,js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {}
  },
  plugins: [],
  // 生产环境移除未使用的样式
  purge: {
    enabled: process.env.NODE_ENV === 'production',
    content: [
      './src/**/*.vue',
      './src/**/*.js',
      './src/**/*.ts'
    ],
    // 保护动态生成的类名
    safelist: [
      'bg-red-500',
      'bg-green-500',
      'bg-blue-500',
      /^text-(red|green|blue)-(400|500|600)$/,
    ]
  }
}
```

### 团队协作规范

#### 类名约定
```vue
<template>
  <!-- 推荐：语义化的组合 -->
  <article class="blog-post">
    <header class="flex items-center justify-between p-6 border-b border-gray-200">
      <h1 class="text-2xl font-bold text-gray-900">{{ title }}</h1>
      <time class="text-sm text-gray-500">{{ publishDate }}</time>
    </header>
    
    <div class="prose prose-lg max-w-none p-6">
      <slot />
    </div>
  </article>

  <!-- 避免：过长的类名列表 -->
  <div class="flex flex-col items-start justify-start w-full max-w-4xl mx-auto my-8 p-6 bg-white border border-gray-200 rounded-lg shadow-sm hover:shadow-md transition-shadow duration-200">
    <!-- 内容 -->
  </div>
</template>
```

#### 代码审查检查清单
- [ ] 是否优先使用了Tailwind类而不是自定义CSS？
- [ ] 类名是否按照规定的顺序组织？
- [ ] 响应式类是否遵循移动优先原则？
- [ ] 是否正确使用了设计系统中的颜色和间距？
- [ ] 复杂的样式是否合理地使用了@apply指令？
- [ ] 是否避免了不必要的自定义CSS？
- [ ] 动态类名是否包含在safelist中？