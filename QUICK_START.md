# Vue前端开发规范 - 快速开始指南

<div align="center">

![快速开始](https://img.shields.io/badge/快速开始-5分钟设置-blue)
![适用场景](https://img.shields.io/badge/适用场景-新项目&现有项目-green)
![难度等级](https://img.shields.io/badge/难度-初级-brightgreen)

**⚡ 5分钟快速设置Vue项目规范**

[🏠 返回首页](./README.md) • [📋 完整目录](./TABLE_OF_CONTENTS.md) • [💡 代码示例](./EXAMPLES.md)

</div>

---

## 🚀 5分钟快速设置

> 💡 **目标**: 快速建立符合规范的Vue项目开发环境

### 新项目快速启动

#### 1. 创建Vue项目 (1分钟)

```bash
# 使用Vue CLI创建项目
npm create vue@latest my-vue-project

# 选择以下配置
✅ TypeScript
✅ Router
✅ Pinia
✅ Vitest
✅ ESLint
✅ Prettier

cd my-vue-project
npm install

# 安装ESLint 9和Vue官方配置 (最新版本)
npm install --save-dev eslint@^9.21.0 eslint-plugin-vue@~10.0.0 @vue/eslint-config-typescript@^14.5.0 @vue/eslint-config-prettier@^10.2.0

# 可选：安装性能优化工具
npm install --save-dev oxlint@^0.15.13 eslint-plugin-oxlint@^0.15.13 npm-run-all2@^7.0.2
```

#### 2. 复制配置文件 (2分钟)

**ESLint配置 (Vue官方推荐)** - 创建 `eslint.config.js`:
```javascript
import pluginVue from 'eslint-plugin-vue'
import { defineConfigWithVueTs, vueTsConfigs } from '@vue/eslint-config-typescript'
import skipFormatting from '@vue/eslint-config-prettier/skip-formatting'

export default defineConfigWithVueTs(
  {
    files: ['**/*.{ts,mts,tsx,vue}'],
  },
  {
    ignores: ['**/dist/**', '**/coverage/**'],
  },
  pluginVue.configs['flat/essential'],
  vueTsConfigs.recommended,
  {
    rules: {
      'vue/multi-word-component-names': 'error',
      'vue/component-definition-name-casing': ['error', 'PascalCase'],
      '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
      'no-console': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
    }
  },
  skipFormatting,
)
```

**Prettier配置** - 创建 `.prettierrc`:
```json
{
  "semi": false,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 100,
  "endOfLine": "lf"
}
```

**TailwindCSS配置** (可选) - 如果使用TailwindCSS:
```bash
npm install tailwindcss @tailwindcss/vite
```

在 `vite.config.js` 中配置TailwindCSS插件:
```javascript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [
    vue(),
    tailwindcss(),
  ],
})
```

在CSS文件中导入TailwindCSS并进行配置:
```css
@import "tailwindcss";

/* TailwindCSS 4.x 使用CSS-first配置，无需tailwind.config.js */
@theme {
  --color-primary: #3b82f6;
  --color-secondary: #6b7280;
  --font-family-sans: Inter, system-ui, sans-serif;
  --spacing-18: 4.5rem;
}
```

#### 3. 设置项目结构 (2分钟)

创建标准目录结构:
```bash
mkdir -p src/{components/{base,common,layout},composables,utils/{helpers,validators,constants},services/{api,http},types}
```

更新 `src` 目录结构:
```
src/
├── components/
│   ├── base/          # 基础UI组件
│   ├── common/        # 通用业务组件
│   └── layout/        # 布局组件
├── views/             # 页面组件
├── stores/            # Pinia状态管理
├── router/            # 路由配置
├── composables/       # 组合式函数
├── utils/             # 工具函数
├── services/          # API服务
├── types/             # TypeScript类型
└── assets/            # 静态资源
```

---

## 📝 立即可用的代码模板

### Vue组件模板

创建 `src/components/base/BaseButton.vue`:
```vue
<template>
  <button 
    :class="buttonClasses"
    :disabled="disabled"
    @click="handleClick"
  >
    <slot />
  </button>
</template>

<script setup lang="ts">
import { computed } from 'vue'

interface Props {
  variant?: 'primary' | 'secondary' | 'danger'
  size?: 'sm' | 'md' | 'lg'
  disabled?: boolean
}

interface Emits {
  click: [event: MouseEvent]
}

const props = withDefaults(defineProps<Props>(), {
  variant: 'primary',
  size: 'md',
  disabled: false
})

const emit = defineEmits<Emits>()

const buttonClasses = computed(() => {
  const baseClasses = 'px-4 py-2 rounded font-medium transition-colors'
  const variantClasses = {
    primary: 'bg-blue-500 text-white hover:bg-blue-600',
    secondary: 'bg-gray-200 text-gray-800 hover:bg-gray-300',
    danger: 'bg-red-500 text-white hover:bg-red-600'
  }
  const sizeClasses = {
    sm: 'text-sm px-3 py-1',
    md: 'text-base px-4 py-2',
    lg: 'text-lg px-6 py-3'
  }
  
  return [
    baseClasses,
    variantClasses[props.variant],
    sizeClasses[props.size],
    props.disabled && 'opacity-50 cursor-not-allowed'
  ].filter(Boolean).join(' ')
})

const handleClick = (event: MouseEvent) => {
  if (!props.disabled) {
    emit('click', event)
  }
}
</script>
```

### Pinia Store模板

创建 `src/stores/modules/user.ts`:
```typescript
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

interface User {
  id: string
  name: string
  email: string
}

interface LoginCredentials {
  email: string
  password: string
}

export const useUserStore = defineStore('user', () => {
  // State
  const user = ref<User | null>(null)
  const isLoading = ref(false)
  const error = ref<string | null>(null)

  // Getters
  const isLoggedIn = computed(() => !!user.value)
  const userName = computed(() => user.value?.name ?? '未登录')

  // Actions
  const login = async (credentials: LoginCredentials) => {
    try {
      isLoading.value = true
      error.value = null
      
      // 模拟API调用
      const response = await fetch('/api/login', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(credentials)
      })
      
      if (!response.ok) throw new Error('登录失败')
      
      user.value = await response.json()
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
  }

  return {
    user, isLoading, error,
    isLoggedIn, userName,
    login, logout
  }
})
```

### API服务模板

创建 `src/services/api/base.ts`:
```typescript
interface ApiResponse<T> {
  code: number
  message: string
  data: T
}

class ApiClient {
  private baseURL: string

  constructor(baseURL: string = '/api') {
    this.baseURL = baseURL
  }

  private async request<T>(
    endpoint: string,
    options: RequestInit = {}
  ): Promise<ApiResponse<T>> {
    const url = `${this.baseURL}${endpoint}`
    
    const config: RequestInit = {
      headers: {
        'Content-Type': 'application/json',
        ...options.headers,
      },
      ...options,
    }

    try {
      const response = await fetch(url, config)
      
      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`)
      }
      
      return await response.json()
    } catch (error) {
      console.error('API request failed:', error)
      throw error
    }
  }

  async get<T>(endpoint: string): Promise<ApiResponse<T>> {
    return this.request<T>(endpoint, { method: 'GET' })
  }

  async post<T>(endpoint: string, data: any): Promise<ApiResponse<T>> {
    return this.request<T>(endpoint, {
      method: 'POST',
      body: JSON.stringify(data),
    })
  }

  async put<T>(endpoint: string, data: any): Promise<ApiResponse<T>> {
    return this.request<T>(endpoint, {
      method: 'PUT',
      body: JSON.stringify(data),
    })
  }

  async delete<T>(endpoint: string): Promise<ApiResponse<T>> {
    return this.request<T>(endpoint, { method: 'DELETE' })
  }
}

export const apiClient = new ApiClient()
```

---

## ⚙️ VS Code配置

### 必装插件

在 `.vscode/extensions.json` 中添加:
```json
{
  "recommendations": [
    "vue.volar",
    "vue.typescript-vue-plugin",
    "esbenp.prettier-vscode",
    "dbaeumer.vscode-eslint",
    "bradlc.vscode-tailwindcss"
  ]
}
```

### 工作区设置

创建 `.vscode/settings.json`:
```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "eslint.validate": [
    "javascript",
    "javascriptreact", 
    "typescript",
    "typescriptreact",
    "vue"
  ],
  "typescript.preferences.importModuleSpecifier": "relative",
  "vue.complete.casing.tags": "pascal",
  "vue.complete.casing.props": "camel"
}
```

---

## 🎯 第一个组件示例

创建 `src/views/HomePage.vue`:
```vue
<template>
  <div class="home-page">
    <header class="home-page__header">
      <h1 class="home-page__title">欢迎使用Vue前端规范</h1>
      <p class="home-page__subtitle">{{ welcomeMessage }}</p>
    </header>
    
    <main class="home-page__content">
      <BaseButton 
        variant="primary" 
        @click="handleGetStarted"
      >
        开始使用
      </BaseButton>
      
      <div v-if="user" class="home-page__user-info">
        <p>当前用户: {{ user.name }}</p>
      </div>
    </main>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import { useRouter } from 'vue-router'
import { useUserStore } from '@/stores/modules/user'
import BaseButton from '@/components/base/BaseButton.vue'

const router = useRouter()
const userStore = useUserStore()

const welcomeMessage = computed(() => {
  const hour = new Date().getHours()
  if (hour < 12) return '早上好！'
  if (hour < 18) return '下午好！'
  return '晚上好！'
})

const user = computed(() => userStore.user)

const handleGetStarted = () => {
  router.push('/dashboard')
}
</script>

<style scoped>
.home-page {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  padding: 2rem;
}

.home-page__header {
  text-align: center;
  margin-bottom: 3rem;
}

.home-page__title {
  font-size: 2.5rem;
  font-weight: 700;
  color: #1f2937;
  margin-bottom: 1rem;
}

.home-page__subtitle {
  font-size: 1.25rem;
  color: #6b7280;
}

.home-page__content {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2rem;
}

.home-page__user-info {
  padding: 1rem;
  background-color: #f3f4f6;
  border-radius: 0.5rem;
}
</style>
```

---

## 📋 开发检查清单

### 每次提交前检查

```bash
# 1. 快速代码检查和修复 (推荐先运行)
npm run lint:oxlint

# 2. 详细代码检查和修复
npm run lint:eslint

# 3. 或者运行所有lint工具
npm run lint

# 4. 代码格式化
npm run format

# 5. 类型检查
npm run type-check

# 6. 运行测试
npm run test:unit

# 7. 构建检查
npm run build
```

### ⚡ 性能提示

- **oxlint**: 极快的基础错误检查，建议在开发过程中频繁使用
- **ESLint**: 详细的代码风格检查，适合提交前运行
- **组合使用**: `npm run lint` 会按顺序运行所有检查工具

### 组件开发检查

- [ ] 组件名使用PascalCase
- [ ] Props定义了TypeScript类型
- [ ] 事件使用kebab-case命名
- [ ] 样式使用scoped
- [ ] 遵循BEM命名规范

### 提交信息检查

```bash
# 正确的提交格式
git commit -m "feat: 添加用户登录组件"
git commit -m "fix: 修复路由守卫权限判断"
git commit -m "docs: 更新组件使用文档"
```

---

## 🔗 下一步学习

### 基础必读
1. [项目结构规范](./01-project-structure.md) - 了解完整的项目组织方式
2. [Vue组件结构](./02-vue-component-structure.md) - 深入学习组件开发规范
3. [ESLint配置](./10-eslint-configuration.md) - 配置代码质量检查

### 进阶学习
1. [Pinia状态管理](./13-pinia-store-structure.md) - 学习状态管理最佳实践
2. [Vue Router配置](./15-vue-router-configuration.md) - 掌握路由配置规范
3. [性能优化](./20-vue-performance-optimization.md) - 提升应用性能

### 团队协作
1. [Git工作流](./17-git-commit-standards.md) - 建立规范的版本控制流程
2. [代码审查](./19-code-review-standards.md) - 提高代码质量
3. [测试规范](./22-unit-testing-standards.md) - 保证代码可靠性

---

## 🆘 常见问题

### Q: ESLint报错怎么办？
A: 运行 `npm run lint:fix` 自动修复大部分问题，手动修复剩余问题。

### Q: 组件应该放在哪个目录？
A: 
- 基础UI组件 → `src/components/base/`
- 业务组件 → `src/components/common/`
- 页面组件 → `src/views/`

### Q: 如何处理API调用？
A: 使用统一的API客户端，在 `src/services/api/` 目录下组织API服务。

### Q: 状态管理何时使用？
A: 当数据需要在多个组件间共享时使用Pinia，简单的父子组件通信使用Props/Events。

---

---

<div align="center">

## 🎉 设置完成！

**恭喜！你已经完成了Vue前端规范的快速设置**

现在可以开始高效的Vue开发了！

### 📚 下一步学习

| 学习路径 | 推荐文档 | 预计时间 |
|----------|----------|----------|
| 🎯 **深入组件开发** | [Vue组件结构规范](./02-vue-component-structure.md) | 15分钟 |
| 🗄️ **掌握状态管理** | [Pinia Store结构](./13-pinia-store-structure.md) | 20分钟 |
| 🔧 **完善工具配置** | [ESLint配置详解](./10-eslint-configuration.md) | 10分钟 |
| 🔄 **建立工作流程** | [Git提交规范](./17-git-commit-standards.md) | 10分钟 |

### 🔗 快速链接

[📋 完整文档目录](./TABLE_OF_CONTENTS.md) • [💡 代码示例集合](./EXAMPLES.md) • [📄 配置模板](./TEMPLATES.md) • [🛡️ 质量检查清单](./25-code-quality-checklist.md)

---

**💬 需要帮助？** 查看 [常见问题](#常见问题) 或联系团队获取支持

</div>