# Prettier 代码格式化配置规范

## 概述

Prettier 是一个代码格式化工具，能够自动格式化代码以保持一致的代码风格。本文档定义了 Vue 项目中 Prettier 的配置标准和使用规范。

## .prettierrc 配置文件模板

### 基础配置

在项目根目录创建 `.prettierrc` 文件：

```json
{
  "semi": false,
  "singleQuote": true,
  "quoteProps": "as-needed",
  "trailingComma": "es5",
  "tabWidth": 2,
  "useTabs": false,
  "printWidth": 100,
  "bracketSpacing": true,
  "bracketSameLine": false,
  "arrowParens": "avoid",
  "endOfLine": "lf",
  "embeddedLanguageFormatting": "auto",
  "htmlWhitespaceSensitivity": "css",
  "insertPragma": false,
  "jsxSingleQuote": true,
  "proseWrap": "preserve",
  "requirePragma": false,
  "vueIndentScriptAndStyle": false
}
```

### Vue 特定配置

针对 Vue 文件的特殊配置：

```json
{
  "overrides": [
    {
      "files": "*.vue",
      "options": {
        "parser": "vue",
        "vueIndentScriptAndStyle": false
      }
    },
    {
      "files": ["*.ts", "*.tsx"],
      "options": {
        "parser": "typescript"
      }
    },
    {
      "files": "*.json",
      "options": {
        "parser": "json",
        "trailingComma": "none"
      }
    },
    {
      "files": "*.md",
      "options": {
        "parser": "markdown",
        "proseWrap": "always",
        "printWidth": 80
      }
    }
  ]
}
```

## 代码格式化规则详解

### 1. 基础格式化规则

#### 分号使用 (semi)
```javascript
// ✅ 推荐：不使用分号
const message = 'Hello World'
const numbers = [1, 2, 3]

// ❌ 避免：使用分号
const message = 'Hello World';
const numbers = [1, 2, 3];
```

#### 引号使用 (singleQuote)
```javascript
// ✅ 推荐：使用单引号
const message = 'Hello World'
const template = `Hello ${name}`

// ❌ 避免：使用双引号
const message = "Hello World"
```

#### 尾随逗号 (trailingComma)
```javascript
// ✅ 推荐：ES5 兼容的尾随逗号
const config = {
  name: 'app',
  version: '1.0.0',
  dependencies: ['vue', 'pinia'],
}

// ❌ 避免：所有地方都使用尾随逗号
const fn = (
  param1,
  param2,
) => {}
```

### 2. 缩进和空格规则

#### 缩进宽度 (tabWidth)
```javascript
// ✅ 推荐：2 个空格缩进
function example() {
  if (condition) {
    return true
  }
}

// ❌ 避免：4 个空格或制表符
function example() {
    if (condition) {
        return true
    }
}
```

#### 括号空格 (bracketSpacing)
```javascript
// ✅ 推荐：括号内有空格
const obj = { name: 'John', age: 30 }

// ❌ 避免：括号内无空格
const obj = {name: 'John', age: 30}
```

### 3. 行长度和换行规则

#### 最大行长度 (printWidth)
```javascript
// ✅ 推荐：100 字符内
const longVariableName = someFunction(parameter1, parameter2, parameter3)

// ✅ 自动换行：超过 100 字符
const veryLongVariableName = someVeryLongFunctionName(
  veryLongParameter1,
  veryLongParameter2,
  veryLongParameter3
)
```

#### 箭头函数参数 (arrowParens)
```javascript
// ✅ 推荐：单参数不使用括号
const square = x => x * x
const add = (a, b) => a + b

// ❌ 避免：单参数使用括号
const square = (x) => x * x
```

### 4. Vue 文件格式化规则

#### Vue 组件结构
```vue
<template>
  <div class="component">
    <h1>{{ title }}</h1>
    <button @click="handleClick">
      Click me
    </button>
  </div>
</template>

<script setup lang="ts">
interface Props {
  title: string
}

const props = defineProps<Props>()

const handleClick = () => {
  console.log('Button clicked')
}
</script>

<style scoped>
.component {
  padding: 1rem;
  background-color: #f5f5f5;
}
</style>
```

## 与 ESLint 集成配置指南

### 1. 安装依赖

```bash
# 安装 Prettier 和 ESLint 集成插件
npm install --save-dev prettier eslint-config-prettier eslint-plugin-prettier

# 或使用 yarn
yarn add --dev prettier eslint-config-prettier eslint-plugin-prettier
```

### 2. ESLint 配置更新

在 `.eslintrc.js` 中添加 Prettier 配置：

```javascript
module.exports = {
  extends: [
    '@vue/eslint-config-typescript',
    '@vue/eslint-config-prettier', // 必须放在最后
  ],
  plugins: ['prettier'],
  rules: {
    'prettier/prettier': 'error',
    // 禁用与 Prettier 冲突的 ESLint 规则
    'indent': 'off',
    'linebreak-style': 'off',
    'quotes': 'off',
    'semi': 'off',
    'comma-dangle': 'off',
    'max-len': 'off',
  },
}
```

### 3. 解决冲突规则

创建 `.eslintrc.override.js` 处理特殊情况：

```javascript
module.exports = {
  overrides: [
    {
      files: ['*.vue'],
      rules: {
        // Vue 文件特殊规则
        'vue/max-attributes-per-line': 'off',
        'vue/singleline-html-element-content-newline': 'off',
      },
    },
    {
      files: ['*.ts', '*.tsx'],
      rules: {
        // TypeScript 文件特殊规则
        '@typescript-eslint/indent': 'off',
      },
    },
  ],
}
```

### 4. package.json 脚本配置

```json
{
  "scripts": {
    "format": "prettier --write .",
    "format:check": "prettier --check .",
    "lint": "eslint . --ext .vue,.js,.jsx,.cjs,.mjs,.ts,.tsx,.cts,.mts",
    "lint:fix": "eslint . --ext .vue,.js,.jsx,.cjs,.mjs,.ts,.tsx,.cts,.mts --fix",
    "code:check": "npm run lint && npm run format:check",
    "code:fix": "npm run lint:fix && npm run format"
  }
}
```

## 编辑器集成设置说明

### 1. VS Code 配置

#### 安装插件
- Prettier - Code formatter
- ESLint
- Vetur 或 Volar (Vue 支持)

#### 工作区设置 (.vscode/settings.json)
```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true,
    "source.formatDocument": true
  },
  "[vue]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[json]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "prettier.requireConfig": true,
  "prettier.useEditorConfig": false,
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact",
    "vue"
  ]
}
```

#### 用户设置建议
```json
{
  "editor.formatOnPaste": true,
  "editor.formatOnType": false,
  "prettier.printWidth": 100,
  "prettier.tabWidth": 2,
  "prettier.useTabs": false,
  "prettier.semi": false,
  "prettier.singleQuote": true
}
```

### 2. WebStorm/IntelliJ IDEA 配置

#### Prettier 设置
1. 打开 `Settings` → `Languages & Frameworks` → `JavaScript` → `Prettier`
2. 配置 Prettier package 路径
3. 启用 `On code reformat` 和 `On save`
4. 设置 Run for files 为 `{**/*,*}.{js,ts,jsx,tsx,vue,json,css,scss,md}`

#### ESLint 设置
1. 打开 `Settings` → `Languages & Frameworks` → `JavaScript` → `Code Quality Tools` → `ESLint`
2. 选择 `Automatic ESLint configuration`
3. 启用 `Run eslint --fix on save`

### 3. Vim/Neovim 配置

#### 使用 coc.nvim
```json
{
  "prettier.requireConfig": true,
  "prettier.onlyUseLocalVersion": true,
  "eslint.autoFixOnSave": true,
  "coc.preferences.formatOnSaveFiletypes": [
    "javascript",
    "typescript",
    "vue",
    "json",
    "css",
    "scss",
    "markdown"
  ]
}
```

#### 使用 ALE
```vim
let g:ale_fixers = {
\   'javascript': ['prettier', 'eslint'],
\   'typescript': ['prettier', 'eslint'],
\   'vue': ['prettier', 'eslint'],
\   'css': ['prettier'],
\   'scss': ['prettier'],
\   'json': ['prettier'],
\   'markdown': ['prettier'],
\}
let g:ale_fix_on_save = 1
```

## 忽略文件配置

### .prettierignore 文件

创建 `.prettierignore` 文件排除不需要格式化的文件：

```
# 依赖目录
node_modules/
dist/
build/

# 生成文件
*.min.js
*.min.css
coverage/

# 配置文件
.env*
*.config.js
vite.config.ts

# 文档
CHANGELOG.md
LICENSE

# 其他
public/
.nuxt/
.output/
```

## Git Hooks 集成

### 使用 husky 和 lint-staged

#### 安装依赖
```bash
npm install --save-dev husky lint-staged
```

#### package.json 配置
```json
{
  "lint-staged": {
    "*.{js,jsx,ts,tsx,vue}": [
      "eslint --fix",
      "prettier --write"
    ],
    "*.{css,scss,json,md}": [
      "prettier --write"
    ]
  }
}
```

#### 设置 Git hooks
```bash
npx husky install
npx husky add .husky/pre-commit "npx lint-staged"
```

## 最佳实践

### 1. 团队协作
- 确保所有团队成员使用相同的 Prettier 配置
- 在 CI/CD 中集成格式检查
- 定期更新 Prettier 版本

### 2. 性能优化
- 使用 `.prettierignore` 排除大文件
- 在编辑器中启用增量格式化
- 合理配置 Git hooks

### 3. 配置管理
- 将配置文件纳入版本控制
- 文档化特殊配置的原因
- 定期评估和更新配置

### 4. 故障排除
- 检查配置文件语法
- 验证插件版本兼容性
- 查看编辑器错误日志

## 常见问题解决

### 1. Prettier 与 ESLint 冲突
```bash
# 检查冲突规则
npx eslint-config-prettier path/to/main.js

# 修复方法：确保 eslint-config-prettier 在 extends 数组最后
```

### 2. Vue 文件格式化问题
- 确保安装了 `@prettier/plugin-vue`
- 检查 Vue 文件的 parser 配置
- 验证编辑器 Vue 插件版本

### 3. 格式化不生效
- 检查 `.prettierrc` 文件位置和语法
- 验证编辑器插件配置
- 确认文件类型在格式化范围内

这个配置规范确保了代码格式的一致性，提高了团队协作效率，并与现有的开发工具链完美集成。