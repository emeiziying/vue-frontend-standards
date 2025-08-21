# Vue组件结构规范

## 概述

本文档定义了Vue组件的标准结构、模板格式、script setup语法规范和样式编写指南，确保团队开发的组件具有一致性和可维护性。

## 组件文件结构

### 标准组件模板

所有Vue组件应遵循以下标准结构：

```vue
<template>
  <!-- HTML模板内容 -->
</template>

<script setup lang="ts">
  // TypeScript逻辑代码
</script>

<style scoped>
  /* 组件样式 */
</style>
```

### 文件命名规范

- **组件文件名**：使用PascalCase命名，如 `UserProfile.vue`、`ProductCard.vue`
- **页面组件**：使用PascalCase + Page后缀，如 `UserProfilePage.vue`
- **布局组件**：使用PascalCase + Layout后缀，如 `DefaultLayout.vue`

## Template模板规范

### 基本结构

```vue
<template>
  <div class="component-name">
    <!-- 主要内容区域 -->
    <header v-if="showHeader" class="component-name__header">
      <h2 class="component-name__title">{{ title }}</h2>
    </header>
    
    <main class="component-name__content">
      <!-- 内容插槽 -->
      <slot name="content">
        <p class="component-name__default-text">默认内容</p>
      </slot>
    </main>
    
    <footer v-if="showFooter" class="component-name__footer">
      <slot name="footer" />
    </footer>
  </div>
</template>
```

### 模板编写规则

1. **根元素**：每个组件必须有一个根元素，使用组件名作为CSS类名
2. **条件渲染**：优先使用 `v-if` 而非 `v-show`，除非需要频繁切换显示状态
3. **列表渲染**：使用 `v-for` 时必须提供 `key` 属性
4. **属性绑定**：动态属性使用 `v-bind` 或简写 `:`
5. **事件监听**：使用 `v-on` 或简写 `@`

### 指令使用规范

```vue
<template>
  <!-- 条件渲染 -->
  <div v-if="isVisible" class="content">
    <!-- 列表渲染 -->
    <ul>
      <li 
        v-for="item in items" 
        :key="item.id"
        :class="{ active: item.isActive }"
        @click="handleItemClick(item)"
      >
        {{ item.name }}
      </li>
    </ul>
  </div>
  
  <!-- 表单绑定 -->
  <form @submit.prevent="handleSubmit">
    <input 
      v-model="formData.name"
      type="text"
      placeholder="请输入姓名"
      required
    >
    <button type="submit" :disabled="isSubmitting">
      {{ isSubmitting ? '提交中...' : '提交' }}
    </button>
  </form>
</template>
```

## Script Setup语法规范

### 基本结构

```vue
<script setup lang="ts">
// 1. 导入依赖
import { ref, computed, onMounted, watch } from 'vue'
import { useRouter } from 'vue-router'
import type { User } from '@/types/user'

// 2. 定义Props
interface Props {
  title: string
  count?: number
  user?: User
}

const props = withDefaults(defineProps<Props>(), {
  count: 0,
  user: undefined
})

// 3. 定义Emits
interface Emits {
  update: [value: string]
  change: [event: Event]
  submit: [data: FormData]
}

const emit = defineEmits<Emits>()

// 4. 响应式数据
const isLoading = ref(false)
const formData = ref({
  name: '',
  email: ''
})

// 5. 计算属性
const displayTitle = computed(() => {
  return props.title.toUpperCase()
})

// 6. 方法定义
const handleSubmit = () => {
  emit('submit', new FormData())
}

// 7. 生命周期钩子
onMounted(() => {
  console.log('组件已挂载')
})

// 8. 监听器
watch(() => props.count, (newVal, oldVal) => {
  console.log(`count changed from ${oldVal} to ${newVal}`)
})
</script>
```

### 代码组织规则

1. **导入顺序**：
   - Vue核心API
   - Vue生态系统（Router、Pinia等）
   - 第三方库
   - 项目内部模块
   - 类型定义

2. **代码块顺序**：
   - Props定义
   - Emits定义
   - 响应式数据
   - 计算属性
   - 方法定义
   - 生命周期钩子
   - 监听器

3. **命名规范**：
   - 响应式变量：camelCase
   - 方法名：camelCase，以动词开头
   - 常量：SCREAMING_SNAKE_CASE

### TypeScript类型定义

```vue
<script setup lang="ts">
// Props接口定义
interface ComponentProps {
  // 必需属性
  title: string
  items: Array<Item>
  
  // 可选属性
  subtitle?: string
  maxCount?: number
  
  // 函数属性
  onUpdate?: (value: string) => void
  
  // 联合类型
  status: 'loading' | 'success' | 'error'
  
  // 泛型
  data: Record<string, any>
}

// Emits接口定义
interface ComponentEmits {
  // 事件名: [参数类型列表]
  update: [value: string]
  change: [event: Event]
  'item-select': [item: Item, index: number]
}

// 本地状态类型
interface FormState {
  name: string
  email: string
  isValid: boolean
}

const props = defineProps<ComponentProps>()
const emit = defineEmits<ComponentEmits>()

const formState = ref<FormState>({
  name: '',
  email: '',
  isValid: false
})
</script>
```

## 组件样式编写指南

### 基本样式结构

```vue
<style scoped>
/* 1. 组件根样式 */
.component-name {
  /* 布局属性 */
  display: flex;
  flex-direction: column;
  
  /* 尺寸属性 */
  width: 100%;
  min-height: 200px;
  
  /* 间距属性 */
  padding: 1rem;
  margin: 0;
  
  /* 外观属性 */
  background-color: #ffffff;
  border: 1px solid #e5e7eb;
  border-radius: 0.5rem;
}

/* 2. 子元素样式 */
.component-name__header {
  margin-bottom: 1rem;
  padding-bottom: 0.5rem;
  border-bottom: 1px solid #e5e7eb;
}

.component-name__title {
  font-size: 1.25rem;
  font-weight: 600;
  color: #1f2937;
  margin: 0;
}

.component-name__content {
  flex: 1;
  padding: 1rem 0;
}

.component-name__footer {
  margin-top: 1rem;
  padding-top: 0.5rem;
  border-top: 1px solid #e5e7eb;
}

/* 3. 状态样式 */
.component-name--loading {
  opacity: 0.6;
  pointer-events: none;
}

.component-name--error {
  border-color: #ef4444;
  background-color: #fef2f2;
}

/* 4. 响应式样式 */
@media (max-width: 768px) {
  .component-name {
    padding: 0.75rem;
  }
  
  .component-name__title {
    font-size: 1.125rem;
  }
}
</style>
```

### CSS类命名规范

采用BEM（Block Element Modifier）命名方法：

- **Block（块）**：组件名，如 `.user-card`
- **Element（元素）**：块内的元素，如 `.user-card__avatar`
- **Modifier（修饰符）**：状态或变体，如 `.user-card--active`

```css
/* 正确示例 */
.user-card { }
.user-card__avatar { }
.user-card__name { }
.user-card__email { }
.user-card--active { }
.user-card--loading { }

/* 错误示例 */
.userCard { }
.user_card_avatar { }
.userCardActive { }
```

### 样式编写规则

1. **使用scoped**：所有组件样式必须使用 `scoped` 属性
2. **避免深度选择器**：尽量不使用 `:deep()` 选择器
3. **CSS属性顺序**：
   - 布局属性（display, position, float等）
   - 尺寸属性（width, height, padding, margin等）
   - 外观属性（color, background, border等）
   - 文字属性（font, text-align等）
   - 其他属性（cursor, z-index等）

4. **响应式设计**：使用移动优先的响应式设计
5. **CSS变量**：使用CSS自定义属性定义主题色彩

### TailwindCSS集成

当使用TailwindCSS时，遵循以下规范：

```vue
<template>
  <div class="flex flex-col w-full min-h-[200px] p-4 bg-white border border-gray-200 rounded-lg">
    <header v-if="showHeader" class="mb-4 pb-2 border-b border-gray-200">
      <h2 class="text-xl font-semibold text-gray-800 m-0">{{ title }}</h2>
    </header>
    
    <main class="flex-1 py-4">
      <slot name="content">
        <p class="text-gray-600">默认内容</p>
      </slot>
    </main>
  </div>
</template>

<style scoped>
/* 仅用于无法通过Tailwind实现的自定义样式 */
.custom-animation {
  animation: fadeIn 0.3s ease-in-out;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}
</style>
```

## 组件导出规范

### 默认导出

```vue
<script setup lang="ts">
// 组件逻辑
</script>

<!-- 如需要组件名称，使用defineOptions -->
<script lang="ts">
export default {
  name: 'UserProfile'
}
</script>
```

### 组件注册

```typescript
// 全局注册（main.ts）
import { createApp } from 'vue'
import UserProfile from '@/components/UserProfile.vue'

const app = createApp(App)
app.component('UserProfile', UserProfile)

// 局部注册（在其他组件中）
import UserProfile from '@/components/UserProfile.vue'
```

## 最佳实践

1. **单一职责**：每个组件只负责一个功能
2. **可复用性**：设计时考虑组件的复用性
3. **Props验证**：使用TypeScript进行类型验证
4. **事件命名**：使用kebab-case命名事件
5. **插槽使用**：合理使用具名插槽和作用域插槽
6. **性能优化**：合理使用v-memo和v-once指令

## 示例组件

```vue
<template>
  <article class="article-card">
    <header class="article-card__header">
      <h3 class="article-card__title">{{ article.title }}</h3>
      <time class="article-card__date">{{ formattedDate }}</time>
    </header>
    
    <div class="article-card__content">
      <p class="article-card__excerpt">{{ article.excerpt }}</p>
    </div>
    
    <footer class="article-card__footer">
      <button 
        class="article-card__read-more"
        @click="handleReadMore"
      >
        阅读更多
      </button>
    </footer>
  </article>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import type { Article } from '@/types/article'

interface Props {
  article: Article
}

interface Emits {
  'read-more': [article: Article]
}

const props = defineProps<Props>()
const emit = defineEmits<Emits>()

const formattedDate = computed(() => {
  return new Date(props.article.publishedAt).toLocaleDateString('zh-CN')
})

const handleReadMore = () => {
  emit('read-more', props.article)
}
</script>

<style scoped>
.article-card {
  display: flex;
  flex-direction: column;
  padding: 1.5rem;
  background-color: #ffffff;
  border: 1px solid #e5e7eb;
  border-radius: 0.5rem;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  transition: box-shadow 0.2s ease-in-out;
}

.article-card:hover {
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.article-card__header {
  margin-bottom: 1rem;
}

.article-card__title {
  font-size: 1.25rem;
  font-weight: 600;
  color: #1f2937;
  margin: 0 0 0.5rem 0;
}

.article-card__date {
  font-size: 0.875rem;
  color: #6b7280;
}

.article-card__content {
  flex: 1;
  margin-bottom: 1rem;
}

.article-card__excerpt {
  color: #4b5563;
  line-height: 1.6;
  margin: 0;
}

.article-card__footer {
  display: flex;
  justify-content: flex-end;
}

.article-card__read-more {
  padding: 0.5rem 1rem;
  background-color: #3b82f6;
  color: #ffffff;
  border: none;
  border-radius: 0.25rem;
  cursor: pointer;
  font-size: 0.875rem;
  transition: background-color 0.2s ease-in-out;
}

.article-card__read-more:hover {
  background-color: #2563eb;
}

.article-card__read-more:focus {
  outline: 2px solid #3b82f6;
  outline-offset: 2px;
}
</style>
```