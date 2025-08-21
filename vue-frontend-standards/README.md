# Vue前端开发规范

<div align="center">

![Vue.js](https://img.shields.io/badge/Vue.js-4FC08D?style=for-the-badge&logo=vue.js&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?style=for-the-badge&logo=typescript&logoColor=white)
![TailwindCSS](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white)
![Vite](https://img.shields.io/badge/Vite-646CFF?style=for-the-badge&logo=vite&logoColor=white)

**一套完整的Vue前端开发规范文档**

[📖 快速开始](#快速开始) • [📋 文档目录](#文档导航) • [💡 代码示例](#代码示例) • [🔧 配置模板](#配置文件模板)

</div>

---

## 📋 概述

本文档是一套完整的Vue前端开发规范，涵盖了从项目初始化到部署上线的完整开发生命周期。旨在帮助开发团队建立统一的代码标准、工具配置和开发流程，确保项目的可维护性、可扩展性和团队协作效率。

### ✨ 规范特色

- 🎯 **全面覆盖** - 涵盖Vue开发的各个方面，从组件编写到部署上线
- 📐 **标准统一** - 建立一致的代码风格和项目结构
- 🔧 **工具集成** - 完整的ESLint、Prettier、TailwindCSS配置
- 🚀 **最佳实践** - 基于Vue 3 + TypeScript的现代开发模式
- 👥 **团队协作** - 规范的Git工作流和代码审查流程

## 🚀 快速开始

> 💡 **提示**: 完整的快速开始指南请查看 [QUICK_START.md](./QUICK_START.md)

### 🆕 新项目设置

<details>
<summary><strong>📦 1. 项目初始化</strong></summary>

```bash
# 创建Vue项目
npm create vue@latest my-project

# 推荐配置选择
✅ TypeScript
✅ Router  
✅ Pinia
✅ Vitest
✅ ESLint
✅ Prettier

cd my-project && npm install
```

</details>

<details>
<summary><strong>⚙️ 2. 配置开发工具</strong></summary>

- 📋 复制 [ESLint配置](./10-eslint-configuration.md#eslintconfigjs-配置模板-eslint-9-扁平配置)
- 🎨 复制 [Prettier配置](./11-prettier-configuration.md#prettierrc配置文件模板)  
- 🌊 复制 [TailwindCSS配置](./09-css-style-standards.md#tailwindcss配置)

</details>

<details>
<summary><strong>📁 3. 设置项目结构</strong></summary>

- 📖 参考 [项目结构规范](./01-project-structure.md#标准目录结构)
- 🏗️ 创建必要的目录和文件

```bash
mkdir -p src/{components/{base,common,layout},composables,utils,services,types}
```

</details>

<details>
<summary><strong>🎯 4. 开始开发</strong></summary>

- 📚 阅读 [Vue组件规范](./02-vue-component-structure.md)
- 💡 查看 [代码示例](#代码示例)
- 🔍 使用 [质量检查清单](./25-code-quality-checklist.md)

</details>

### 🔄 现有项目迁移

<details>
<summary><strong>📊 1. 评估现状</strong></summary>

- ✅ 检查当前项目结构
- 📋 分析代码规范差异
- 🔍 使用 [代码质量检查清单](./25-code-quality-checklist.md)

</details>

<details>
<summary><strong>🔧 2. 渐进式迁移</strong></summary>

- **阶段一**: 配置工具链（ESLint、Prettier）
- **阶段二**: 逐步调整代码结构  
- **阶段三**: 更新组件写法
- **阶段四**: 完善测试和文档

</details>

<details>
<summary><strong>👥 3. 团队培训</strong></summary>

- 📚 组织规范培训
- 🔍 建立 [代码审查流程](./19-code-review-standards.md)
- 📝 制定 [Git工作流](./17-git-commit-standards.md)

</details>

## 📚 文档导航

> 📖 **完整目录**: 查看 [TABLE_OF_CONTENTS.md](./TABLE_OF_CONTENTS.md) 获取详细的文档索引  
> 🔍 **快速索引**: 查看 [INDEX.md](./INDEX.md) 获取多维度导航和快速查找  
> ⭐ **ESLint 9**: 所有ESLint配置已升级到最新版本

### 📁 项目结构与组织

<table>
<thead>
<tr>
<th width="200">📄 文档</th>
<th width="300">📝 描述</th>
<th>🔑 关键内容</th>
</tr>
</thead>
<tbody>
<tr>
<td>

**[01-项目结构规范](./01-project-structure.md)**

</td>
<td>标准目录结构、文件命名规范</td>
<td>

• 目录组织  
• 命名规范  
• 模块导入

</td>
</tr>
</tbody>
</table>

### 🎯 Vue开发规范

<table>
<thead>
<tr>
<th width="200">📄 文档</th>
<th width="300">📝 描述</th>
<th>🔑 关键内容</th>
</tr>
</thead>
<tbody>
<tr>
<td>

**[02-Vue组件结构](./02-vue-component-structure.md)**

</td>
<td>组件模板、script setup语法</td>
<td>

• 组件结构  
• 模板规范  
• 样式指南

</td>
</tr>
<tr>
<td>

**[03-Props和Events](./03-vue-props-events.md)**

</td>
<td>组件通信规范</td>
<td>

• Props定义  
• 事件命名  
• 类型约束

</td>
</tr>
<tr>
<td>

**[04-生命周期管理](./04-vue-lifecycle.md)**

</td>
<td>组合式API生命周期</td>
<td>

• 生命周期钩子  
• 副作用处理  
• 清理机制

</td>
</tr>
</tbody>
</table>

### 💻 JavaScript/TypeScript规范

<table>
<thead>
<tr>
<th width="200">📄 文档</th>
<th width="300">📝 描述</th>
<th>🔑 关键内容</th>
</tr>
</thead>
<tbody>
<tr>
<td>

**[05-基础语法规范](./05-javascript-typescript-basics.md)**

</td>
<td>变量、函数、对象操作</td>
<td>

• 命名规范  
• 语法标准  
• 最佳实践

</td>
</tr>
<tr>
<td>

**[06-TypeScript类型](./06-typescript-types.md)**

</td>
<td>类型定义和使用</td>
<td>

• 接口定义  
• 泛型使用  
• 类型守卫

</td>
</tr>
<tr>
<td>

**[07-异步编程](./07-async-programming.md)**

</td>
<td>Promise、async/await</td>
<td>

• 异步模式  
• 错误处理  
• 并发控制

</td>
</tr>
</tbody>
</table>

### 🎨 模板与样式规范

<table>
<thead>
<tr>
<th width="200">📄 文档</th>
<th width="300">📝 描述</th>
<th>🔑 关键内容</th>
</tr>
</thead>
<tbody>
<tr>
<td>

**[08-HTML模板标准](./08-html-template-standards.md)**

</td>
<td>Vue模板语法规范</td>
<td>

• 指令使用  
• 条件渲染  
• 插槽机制

</td>
</tr>
<tr>
<td>

**[09-CSS样式标准](./09-css-style-standards.md)**

</td>
<td>CSS和TailwindCSS规范</td>
<td>

• 样式组织  
• 命名规范  
• 响应式设计

</td>
</tr>
</tbody>
</table>

### 🔧 工具配置

<table>
<thead>
<tr>
<th width="200">📄 文档</th>
<th width="300">📝 描述</th>
<th>🔑 关键内容</th>
</tr>
</thead>
<tbody>
<tr>
<td>

**[10-ESLint配置](./10-eslint-configuration.md)**

</td>
<td>代码质量检查配置</td>
<td>

• 规则配置  
• 插件集成  
• 自定义规则

</td>
</tr>
<tr>
<td>

**[11-Prettier配置](./11-prettier-configuration.md)**

</td>
<td>代码格式化配置</td>
<td>

• 格式化规则  
• 编辑器集成  
• ESLint协调

</td>
</tr>
<tr>
<td>

**[12-构建工具配置](./12-build-tool-configuration.md)**

</td>
<td>Vite构建配置</td>
<td>

• 构建优化  
• 环境配置  
• 插件使用

</td>
</tr>
</tbody>
</table>

### 🗄️ 状态管理与路由

<table>
<thead>
<tr>
<th width="200">📄 文档</th>
<th width="300">📝 描述</th>
<th>🔑 关键内容</th>
</tr>
</thead>
<tbody>
<tr>
<td>

**[13-Pinia Store结构](./13-pinia-store-structure.md)**

</td>
<td>状态管理结构规范</td>
<td>

• Store定义  
• 模块化  
• 组织方式

</td>
</tr>
<tr>
<td>

**[14-Pinia状态模式](./14-pinia-state-patterns.md)**

</td>
<td>状态管理模式</td>
<td>

• 异步处理  
• 错误管理  
• 最佳实践

</td>
</tr>
<tr>
<td>

**[15-Vue Router配置](./15-vue-router-configuration.md)**

</td>
<td>路由配置规范</td>
<td>

• 路由定义  
• 嵌套路由  
• 动态路由

</td>
</tr>
<tr>
<td>

**[16-路由守卫权限](./16-vue-router-guards.md)**

</td>
<td>路由守卫和权限控制</td>
<td>

• 导航守卫  
• 权限验证  
• 路由元信息

</td>
</tr>
</tbody>
</table>

### 🔄 Git工作流

<table>
<thead>
<tr>
<th width="200">📄 文档</th>
<th width="300">📝 描述</th>
<th>🔑 关键内容</th>
</tr>
</thead>
<tbody>
<tr>
<td>

**[17-Git提交规范](./17-git-commit-standards.md)**

</td>
<td>提交信息规范</td>
<td>

• Commit格式  
• 类型定义  
• 最佳实践

</td>
</tr>
<tr>
<td>

**[18-分支管理](./18-git-branch-management.md)**

</td>
<td>分支策略和管理</td>
<td>

• 分支命名  
• 合并策略  
• 发布流程

</td>
</tr>
<tr>
<td>

**[19-代码审查](./19-code-review-standards.md)**

</td>
<td>代码审查流程</td>
<td>

• PR模板  
• 审查清单  
• 流程标准

</td>
</tr>
</tbody>
</table>

### ⚡ 性能与测试

<table>
<thead>
<tr>
<th width="200">📄 文档</th>
<th width="300">📝 描述</th>
<th>🔑 关键内容</th>
</tr>
</thead>
<tbody>
<tr>
<td>

**[20-Vue性能优化](./20-vue-performance-optimization.md)**

</td>
<td>Vue应用性能优化</td>
<td>

• 组件优化  
• 懒加载  
• 内存管理

</td>
</tr>
<tr>
<td>

**[21-构建优化](./21-build-optimization.md)**

</td>
<td>构建性能优化</td>
<td>

• 代码分割  
• 资源压缩  
• 缓存策略

</td>
</tr>
<tr>
<td>

**[22-单元测试](./22-unit-testing-standards.md)**

</td>
<td>单元测试规范</td>
<td>

• 测试框架  
• 组件测试  
• 覆盖率要求

</td>
</tr>
<tr>
<td>

**[23-集成测试](./23-integration-testing-standards.md)**

</td>
<td>集成测试规范</td>
<td>

• E2E测试  
• API测试  
• 测试策略

</td>
</tr>
</tbody>
</table>

### 🛡️ 质量保证

<table>
<thead>
<tr>
<th width="200">📄 文档</th>
<th width="300">📝 描述</th>
<th>🔑 关键内容</th>
</tr>
</thead>
<tbody>
<tr>
<td>

**[24-错误处理日志](./24-error-handling-logging.md)**

</td>
<td>错误处理和日志</td>
<td>

• 全局错误处理  
• 日志规范  
• 监控机制

</td>
</tr>
<tr>
<td>

**[25-代码质量清单](./25-code-quality-checklist.md)**

</td>
<td>代码质量检查</td>
<td>

• 质量标准  
• 检查清单  
• 评估指标

</td>
</tr>
</tbody>
</table>

## 💡 代码示例

> 📚 **更多示例**: 查看 [EXAMPLES.md](./EXAMPLES.md) 获取完整的代码示例集合

### 🎯 标准Vue组件示例

<details>
<summary><strong>📄 ArticleCard.vue - 完整组件示例</strong></summary>

```vue
<template>
  <article class="article-card">
    <!-- 📋 文章头部信息 -->
    <header class="article-card__header">
      <h3 class="article-card__title">{{ article.title }}</h3>
      <time class="article-card__date">{{ formattedDate }}</time>
    </header>
    
    <!-- 📝 文章内容摘要 -->
    <div class="article-card__content">
      <p class="article-card__excerpt">{{ article.excerpt }}</p>
    </div>
    
    <!-- 🔗 操作按钮区域 -->
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

// 🔧 Props接口定义
interface Props {
  article: Article
}

// 📡 Events接口定义
interface Emits {
  'read-more': [article: Article]
}

const props = defineProps<Props>()
const emit = defineEmits<Emits>()

// 📅 格式化日期的计算属性
const formattedDate = computed(() => {
  return new Date(props.article.publishedAt).toLocaleDateString('zh-CN')
})

// 🎯 处理"阅读更多"点击事件
const handleReadMore = () => {
  emit('read-more', props.article)
}
</script>

<style scoped>
/* 🎨 文章卡片主容器 */
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

/* 📋 头部样式 */
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

/* 📝 内容区域样式 */
.article-card__content {
  flex: 1;
  margin-bottom: 1rem;
}

.article-card__excerpt {
  color: #4b5563;
  line-height: 1.6;
  margin: 0;
}

/* 🔗 底部操作区域 */
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
</style>
```

</details>

### 🗄️ Pinia Store示例

<details>
<summary><strong>📦 useUserStore.ts - 用户状态管理示例</strong></summary>

```typescript
// 📁 stores/modules/user.ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'
import type { User, LoginCredentials } from '@/types/user'
import { userApi } from '@/services/api/user'

export const useUserStore = defineStore('user', () => {
  // 🏪 State - 状态定义
  const user = ref<User | null>(null)
  const isLoading = ref(false)
  const error = ref<string | null>(null)

  // 🔍 Getters - 计算属性
  const isLoggedIn = computed(() => !!user.value)
  const userName = computed(() => user.value?.name ?? '未登录')
  const userRole = computed(() => user.value?.role ?? 'guest')

  // 🎯 Actions - 操作方法
  const login = async (credentials: LoginCredentials) => {
    try {
      isLoading.value = true
      error.value = null
      
      const response = await userApi.login(credentials)
      user.value = response.data
      
      // 💾 可选：保存到本地存储
      localStorage.setItem('user', JSON.stringify(response.data))
      
      return response
    } catch (err) {
      error.value = err instanceof Error ? err.message : '登录失败'
      throw err
    } finally {
      isLoading.value = false
    }
  }

  const logout = () => {
    user.value = null
    error.value = null
    
    // 🗑️ 清理本地存储
    localStorage.removeItem('user')
  }

  const updateProfile = async (profileData: Partial<User>) => {
    try {
      isLoading.value = true
      error.value = null
      
      const response = await userApi.updateProfile(profileData)
      user.value = { ...user.value, ...response.data }
      
      return response
    } catch (err) {
      error.value = err instanceof Error ? err.message : '更新失败'
      throw err
    } finally {
      isLoading.value = false
    }
  }

  return {
    // 🏪 State
    user,
    isLoading,
    error,
    
    // 🔍 Getters
    isLoggedIn,
    userName,
    userRole,
    
    // 🎯 Actions
    login,
    logout,
    updateProfile
  }
})
```

</details>

## ⚙️ 配置文件模板

> 🔧 **完整配置**: 查看 [TEMPLATES.md](./TEMPLATES.md) 获取所有配置文件模板

### 📋 ESLint配置 (ESLint 9 + Vue官方推荐)

<details>
<summary><strong>📄 eslint.config.js - Vue官方推荐配置</strong></summary>

```javascript
// 📄 eslint.config.js - Vue官方推荐的ESLint 9配置
import pluginVue from 'eslint-plugin-vue'
import { defineConfigWithVueTs, vueTsConfigs } from '@vue/eslint-config-typescript'
import pluginVitest from '@vitest/eslint-plugin'
import pluginPlaywright from 'eslint-plugin-playwright'
import oxlint from 'eslint-plugin-oxlint'
import skipFormatting from '@vue/eslint-config-prettier/skip-formatting'

export default defineConfigWithVueTs(
  // 📁 指定要检查的文件
  {
    name: 'app/files-to-lint',
    files: ['**/*.{ts,mts,tsx,vue}'],
  },
  
  // 🚫 指定要忽略的文件
  {
    name: 'app/files-to-ignore',
    ignores: ['**/dist/**', '**/dist-ssr/**', '**/coverage/**'],
  },

  // 🎯 Vue基础配置
  pluginVue.configs['flat/essential'],
  
  // 📘 TypeScript推荐配置
  vueTsConfigs.recommended,

  // 🧪 Vitest测试配置 (可选)
  {
    ...pluginVitest.configs.recommended,
    files: ['src/**/__tests__/*'],
  },

  // 🎭 Playwright E2E测试配置 (可选)
  {
    ...pluginPlaywright.configs['flat/recommended'],
    files: ['e2e/**/*.{test,spec}.{js,ts,jsx,tsx}'],
  },

  // ⚡ Oxlint性能优化配置 (可选)
  ...oxlint.configs['flat/recommended'],

  // 🎨 Prettier格式化跳过配置 (必须放在最后)
  skipFormatting,
)
```

**自定义规则配置**:
```javascript
// 在上述配置基础上添加自定义规则
export default defineConfigWithVueTs(
  // ... 基础配置 ...
  
  // 🔧 自定义规则配置
  {
    name: 'app/custom-rules',
    rules: {
      // 🎯 Vue规则
      'vue/multi-word-component-names': 'error',
      'vue/component-definition-name-casing': ['error', 'PascalCase'],
      'vue/component-name-in-template-casing': ['error', 'PascalCase'],
      
      // 📘 TypeScript规则
      '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
      '@typescript-eslint/no-explicit-any': 'warn',
      
      // 🔍 代码质量规则
      'no-console': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
      'no-debugger': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
    }
  },
  
  skipFormatting,
)
```

</details>

### 🎨 Prettier配置

<details>
<summary><strong>📄 .prettierrc - 代码格式化配置</strong></summary>

```json
{
  "semi": false,                    // 🚫 不使用分号
  "singleQuote": true,             // ✨ 使用单引号
  "tabWidth": 2,                   // 📏 缩进宽度2个空格
  "trailingComma": "es5",          // 🔚 ES5兼容的尾随逗号
  "printWidth": 100,               // 📐 每行最大长度100字符
  "endOfLine": "lf",               // 🔄 使用LF换行符
  "arrowParens": "avoid",          // 🏹 箭头函数参数省略括号
  "bracketSpacing": true,          // 🔲 对象字面量括号间距
  "bracketSameLine": false,        // 📝 JSX标签换行
  "vueIndentScriptAndStyle": false // 🎯 Vue文件script和style不缩进
}
```

</details>

### 🌊 TailwindCSS配置

<details>
<summary><strong>📄 tailwind.config.js - 原子化CSS配置</strong></summary>

```javascript
// 📄 tailwind.config.js
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",                           // 🏠 HTML入口文件
    "./src/**/*.{vue,js,ts,jsx,tsx}",        // 📁 源代码文件
  ],
  theme: {
    extend: {
      // 🎨 自定义颜色系统
      colors: {
        primary: {
          50: '#eff6ff',
          100: '#dbeafe',
          200: '#bfdbfe',
          300: '#93c5fd',
          400: '#60a5fa',
          500: '#3b82f6',    // 主色调
          600: '#2563eb',
          700: '#1d4ed8',
          800: '#1e40af',
          900: '#1e3a8a',
        },
        secondary: {
          50: '#f8fafc',
          500: '#64748b',
          900: '#0f172a',
        }
      },
      // 📏 自定义间距
      spacing: {
        '18': '4.5rem',
        '88': '22rem',
      },
      // 🔤 自定义字体
      fontFamily: {
        sans: ['Inter', 'system-ui', 'sans-serif'],
      },
      // 📱 自定义断点
      screens: {
        'xs': '475px',
      }
    },
  },
  plugins: [
    // 🔌 推荐插件
    // require('@tailwindcss/forms'),
    // require('@tailwindcss/typography'),
  ],
}
```

</details>

## 📋 最佳实践总结

### ✅ 推荐做法

<details>
<summary><strong>🎯 1. 保持一致性</strong></summary>

- ✅ 使用统一的命名规范
- ✅ 遵循相同的代码结构  
- ✅ 采用一致的工具配置
- ✅ 建立团队编码标准

**示例**:
```typescript
// ✅ 好的做法 - 一致的命名
const userName = ref('')
const userProfile = ref<UserProfile | null>(null)
const isUserLoggedIn = computed(() => !!userProfile.value)

// ❌ 避免 - 不一致的命名
const name = ref('')
const profile = ref<UserProfile | null>(null)  
const loggedIn = computed(() => !!profile.value)
```

</details>

<details>
<summary><strong>🔧 2. 注重可维护性</strong></summary>

- ✅ 编写清晰的注释和文档
- ✅ 保持函数和组件的单一职责
- ✅ 使用TypeScript增强类型安全
- ✅ 合理拆分组件和模块

**示例**:
```vue
<!-- ✅ 好的做法 - 清晰的组件职责 -->
<template>
  <UserAvatar :user="user" :size="avatarSize" />
  <UserInfo :user="user" />
  <UserActions :user="user" @edit="handleEdit" />
</template>
```

</details>

<details>
<summary><strong>⚡ 3. 优化性能</strong></summary>

- ✅ 合理使用Vue的响应式特性
- ✅ 实施代码分割和懒加载
- ✅ 优化构建配置
- ✅ 使用虚拟滚动处理大列表

**示例**:
```typescript
// ✅ 好的做法 - 懒加载路由
const routes = [
  {
    path: '/dashboard',
    component: () => import('@/views/DashboardPage.vue')
  }
]
```

</details>

<details>
<summary><strong>🛡️ 4. 确保质量</strong></summary>

- ✅ 编写充分的测试覆盖
- ✅ 进行代码审查
- ✅ 使用自动化检查工具
- ✅ 建立CI/CD流程

**示例**:
```typescript
// ✅ 好的做法 - 完整的测试
describe('UserStore', () => {
  it('should login user successfully', async () => {
    const store = useUserStore()
    await store.login({ email: 'test@example.com', password: '123456' })
    expect(store.isLoggedIn).toBe(true)
  })
})
```

</details>

### ❌ 避免做法

<details>
<summary><strong>🚫 1. 不要忽视规范</strong></summary>

- ❌ 不要跳过代码格式化
- ❌ 不要忽略ESLint警告
- ❌ 不要使用不一致的命名
- ❌ 不要提交未经检查的代码

**反例**:
```typescript
// ❌ 避免 - 忽略ESLint警告
const unused_variable = 'test'  // ESLint警告：未使用的变量
console.log('debug info')       // 生产环境不应有console.log
```

</details>

<details>
<summary><strong>🔄 2. 不要过度复杂化</strong></summary>

- ❌ 避免过深的组件嵌套（超过5层）
- ❌ 避免过于复杂的状态管理
- ❌ 避免不必要的抽象
- ❌ 避免过早优化

**反例**:
```vue
<!-- ❌ 避免 - 过深的嵌套 -->
<div>
  <div>
    <div>
      <div>
        <div>
          <div>内容</div>
        </div>
      </div>
    </div>
  </div>
</div>
```

</details>

<details>
<summary><strong>⚠️ 3. 不要忽视性能</strong></summary>

- ❌ 不要忽略bundle大小
- ❌ 不要忽视内存泄漏
- ❌ 不要过度使用响应式数据
- ❌ 不要在模板中使用复杂计算

**反例**:
```vue
<!-- ❌ 避免 - 模板中的复杂计算 -->
<template>
  <div>{{ items.filter(item => item.active).map(item => item.name).join(', ') }}</div>
</template>
```

</details>

## 团队协作

### 新成员入职

1. **环境设置**
   - 安装必要的开发工具
   - 配置编辑器插件
   - 克隆项目并安装依赖

2. **规范学习**
   - 阅读本规范文档
   - 查看代码示例
   - 了解项目结构

3. **实践练习**
   - 完成简单的功能开发
   - 参与代码审查
   - 接受反馈和指导

### 持续改进

1. **定期评估**
   - 收集团队反馈
   - 分析规范执行情况
   - 识别改进机会

2. **规范更新**
   - 跟进技术发展
   - 更新最佳实践
   - 调整工具配置

3. **知识分享**
   - 组织技术分享会
   - 记录经验教训
   - 建立知识库

## 工具和资源

### 推荐工具

- **编辑器**: VS Code
- **浏览器**: Chrome DevTools
- **版本控制**: Git
- **包管理**: npm/pnpm
- **构建工具**: Vite
- **测试框架**: Vitest + Vue Test Utils

### 有用的链接

- [Vue.js 官方文档](https://vuejs.org/)
- [TypeScript 官方文档](https://www.typescriptlang.org/)
- [TailwindCSS 官方文档](https://tailwindcss.com/)
- [Pinia 官方文档](https://pinia.vuejs.org/)
- [Vue Router 官方文档](https://router.vuejs.org/)
- [Vite 官方文档](https://vitejs.dev/)

## 贡献指南

### 如何贡献

1. **发现问题**
   - 报告文档错误
   - 提出改进建议
   - 分享最佳实践

2. **提交更改**
   - Fork 项目
   - 创建功能分支
   - 提交 Pull Request

3. **参与讨论**
   - 参加团队会议
   - 提供反馈意见
   - 分享使用经验

### 更新日志

- **v1.4.0** (2024-03-01): ⭐ **最新更新** - 同步Vue官方最新模板
  - 🚀 **性能优化**: 集成oxlint高性能代码检查
  - 📦 **依赖更新**: 同步Vue官方模板最新依赖版本
  - 🔧 **脚本优化**: 采用npm-run-all2进行脚本管理
  - ⚡ **开发体验**: oxlint + ESLint组合使用策略
- **v1.3.0** (2024-03-01): ESLint升级到v9
  - 🔄 迁移到ESLint 9扁平配置格式
  - 📦 更新所有相关插件版本
  - 📖 添加详细的迁移指南
- **v1.2.0** (2024-02-01): 完善测试规范
- **v1.1.0** (2024-01-15): 添加TailwindCSS规范
- **v1.0.0** (2024-01-01): 初始版本发布

---

<div align="center">

## 📞 联系与支持

| 类型 | 联系方式 | 描述 |
|------|----------|------|
| 🐛 **问题反馈** | [GitHub Issues](https://github.com/your-org/vue-standards/issues) | 报告文档错误或提出改进建议 |
| 💬 **讨论交流** | [GitHub Discussions](https://github.com/your-org/vue-standards/discussions) | 技术讨论和经验分享 |
| 📧 **技术支持** | frontend-team@company.com | 规范使用咨询和技术支持 |
| 📚 **培训咨询** | 联系团队负责人 | 团队培训和规范推广 |

---

### 📈 文档状态

![文档版本](https://img.shields.io/badge/版本-v1.2.0-blue)
![最后更新](https://img.shields.io/badge/更新-2024--03--01-green)
![维护状态](https://img.shields.io/badge/维护-活跃-brightgreen)

### 🤝 贡献者

感谢所有为这份规范文档做出贡献的团队成员！

### 📄 许可证

本文档采用 [MIT License](LICENSE) 开源协议

---

**🎯 让我们一起构建更好的Vue应用！**

</div>