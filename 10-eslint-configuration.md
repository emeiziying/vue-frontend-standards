# ESLint 配置规范

## 概述

ESLint 是 JavaScript 和 TypeScript 代码质量检查工具，本规范定义了 Vue 项目中 ESLint 的配置标准、规则集和最佳实践。

## ESLint 9 新特性

ESLint 9 引入了重要的变化，主要包括：

### 主要变化

1. **扁平配置格式**: 使用 `eslint.config.js` 替代 `.eslintrc.js`
2. **简化的 CLI**: 不再需要指定文件扩展名
3. **更好的性能**: 配置解析和规则执行优化
4. **Node.js 18+**: 最低要求 Node.js 18.18.0 或 20.9.0

### 迁移要点

- 配置文件从 `.eslintrc.js` 迁移到 `eslint.config.js`
- 使用 ES 模块导入/导出语法
- 扁平化的配置结构，更直观的规则覆盖
- 内置支持 TypeScript 和 Vue 文件类型检测

## 性能优化：Oxlint 集成

Vue 官方模板现在集成了 oxlint，这是一个用 Rust 编写的高性能 linter：

### Oxlint 优势

- **极快的速度**: 比 ESLint 快 10-100 倍
- **内存效率**: 更低的内存占用
- **渐进式采用**: 可以与 ESLint 并行使用
- **专注正确性**: 主要检查代码正确性问题

### 使用策略

1. **oxlint**: 快速检查基础错误和正确性问题
2. **ESLint**: 详细的代码风格和最佳实践检查
3. **组合使用**: 先运行 oxlint 快速修复，再运行 ESLint 进行详细检查

## 基础配置

### 安装依赖

```bash
# 核心ESLint包 (v9.x)
npm install --save-dev eslint@^9.21.0

# Vue官方ESLint配置 (最新版本)
npm install --save-dev eslint-plugin-vue@~10.0.0
npm install --save-dev @vue/eslint-config-typescript@^14.5.0
npm install --save-dev @vue/eslint-config-prettier@^10.2.0

# 测试相关ESLint插件 (推荐)
npm install --save-dev @vitest/eslint-plugin@^1.1.36
npm install --save-dev eslint-plugin-playwright@^2.2.0

# 性能优化插件 (推荐)
npm install --save-dev eslint-plugin-oxlint@^0.15.13
npm install --save-dev oxlint@^0.15.13

# 构建工具
npm install --save-dev npm-run-all2@^7.0.2
```

### eslint.config.js 配置模板 (Vue 官方推荐)

#### 基础配置 (推荐用于新项目)

```javascript
import pluginVue from "eslint-plugin-vue";
import {
  defineConfigWithVueTs,
  vueTsConfigs,
} from "@vue/eslint-config-typescript";
import pluginVitest from "@vitest/eslint-plugin";
import pluginPlaywright from "eslint-plugin-playwright";
import oxlint from "eslint-plugin-oxlint";
import skipFormatting from "@vue/eslint-config-prettier/skip-formatting";

// 如需支持Vue文件中的其他语言，取消注释以下行：
// import { configureVueProject } from '@vue/eslint-config-typescript'
// configureVueProject({ scriptLangs: ['ts', 'tsx'] })

export default defineConfigWithVueTs(
  // 指定要检查的文件
  {
    name: "app/files-to-lint",
    files: ["**/*.{ts,mts,tsx,vue}"],
  },

  // 指定要忽略的文件
  {
    name: "app/files-to-ignore",
    ignores: ["**/dist/**", "**/dist-ssr/**", "**/coverage/**"],
  },

  // Vue基础配置
  pluginVue.configs["flat/essential"],

  // TypeScript推荐配置
  vueTsConfigs.recommended,

  // Vitest测试配置
  {
    ...pluginVitest.configs.recommended,
    files: ["src/**/__tests__/*"],
  },

  // Playwright E2E测试配置
  {
    ...pluginPlaywright.configs["flat/recommended"],
    files: ["e2e/**/*.{test,spec}.{js,ts,jsx,tsx}"],
  },

  // Oxlint性能优化配置
  ...oxlint.configs["flat/recommended"],

  // Prettier格式化跳过配置 (必须放在最后)
  skipFormatting
);
```

#### 自定义规则配置

```javascript
import pluginVue from "eslint-plugin-vue";
import {
  defineConfigWithVueTs,
  vueTsConfigs,
} from "@vue/eslint-config-typescript";
import skipFormatting from "@vue/eslint-config-prettier/skip-formatting";

export default defineConfigWithVueTs(
  {
    name: "app/files-to-lint",
    files: ["**/*.{ts,mts,tsx,vue}"],
  },
  {
    name: "app/files-to-ignore",
    ignores: ["**/dist/**", "**/dist-ssr/**", "**/coverage/**"],
  },

  // Vue推荐配置 (可选择 essential, recommended, strongly-recommended)
  pluginVue.configs["flat/recommended"],

  // TypeScript配置
  vueTsConfigs.recommended,

  // 自定义规则配置
  {
    name: "app/custom-rules",
    rules: {
      // Vue规则配置
      "vue/multi-word-component-names": "error",
      "vue/component-definition-name-casing": ["error", "PascalCase"],
      "vue/component-name-in-template-casing": ["error", "PascalCase"],
      "vue/prop-name-casing": ["error", "camelCase"],
      "vue/attribute-hyphenation": ["error", "always"],
      "vue/v-on-event-hyphenation": ["error", "always"],
      "vue/component-tags-order": [
        "error",
        {
          order: ["template", "script", "style"],
        },
      ],
      "vue/block-tag-newline": [
        "error",
        {
          singleline: "always",
          multiline: "always",
        },
      ],

      // TypeScript规则配置
      "@typescript-eslint/no-unused-vars": [
        "error",
        {
          argsIgnorePattern: "^_",
          varsIgnorePattern: "^_",
        },
      ],
      "@typescript-eslint/explicit-function-return-type": "off",
      "@typescript-eslint/explicit-module-boundary-types": "off",
      "@typescript-eslint/no-explicit-any": "warn",
      "@typescript-eslint/prefer-const": "error",
      "@typescript-eslint/no-inferrable-types": "error",
      "@typescript-eslint/prefer-nullish-coalescing": "error",
      "@typescript-eslint/prefer-optional-chain": "error",

      // 通用JavaScript规则
      "no-console": process.env.NODE_ENV === "production" ? "warn" : "off",
      "no-debugger": process.env.NODE_ENV === "production" ? "warn" : "off",
      "prefer-const": "error",
      "no-var": "error",
      "object-shorthand": "error",
      "prefer-template": "error",
    },
  },

  // 测试文件特殊配置
  {
    name: "app/test-files",
    files: ["**/__tests__/**/*", "**/*.{test,spec}.*"],
    rules: {
      "@typescript-eslint/no-explicit-any": "off",
      "no-console": "off",
      "vue/multi-word-component-names": "off",
    },
  },

  // Prettier集成
  skipFormatting
);
```

#### 传统扁平配置 (兼容其他工具)

```javascript
import js from "@eslint/js";
import pluginVue from "eslint-plugin-vue";
import * as parserVue from "vue-eslint-parser";
import configPrettier from "@vue/eslint-config-prettier/skip-formatting";
import pluginTs from "@typescript-eslint/eslint-plugin";
import * as parserTs from "@typescript-eslint/parser";

export default [
  {
    name: "app/files-to-ignore",
    ignores: ["**/dist/**", "**/dist-ssr/**", "**/coverage/**"],
  },

  // JavaScript基础配置
  {
    ...js.configs.recommended,
    name: "app/js",
  },

  // Vue文件配置
  {
    ...pluginVue.configs["flat/recommended"],
    files: ["**/*.vue"],
    languageOptions: {
      parser: parserVue,
      parserOptions: {
        parser: parserTs,
        ecmaVersion: "latest",
        sourceType: "module",
      },
    },
    name: "app/vue",
  },

  // TypeScript文件配置
  {
    files: ["**/*.{ts,tsx,mts,cts}"],
    languageOptions: {
      parser: parserTs,
      parserOptions: {
        ecmaVersion: "latest",
        sourceType: "module",
      },
    },
    plugins: {
      "@typescript-eslint": pluginTs,
    },
    rules: {
      ...pluginTs.configs.recommended.rules,
    },
    name: "app/ts",
  },

  // Prettier配置
  configPrettier,
];
```

````

## Vue项目专用规则集

### 组件规则

```javascript
// Vue组件相关规则
'vue/component-definition-name-casing': ['error', 'PascalCase'],
'vue/component-name-in-template-casing': ['error', 'PascalCase'],
'vue/multi-word-component-names': 'error',
'vue/no-reserved-component-names': 'error',
'vue/require-default-prop': 'error',
'vue/require-prop-types': 'error',
'vue/prop-name-casing': ['error', 'camelCase'],
````

### 模板规则

```javascript
// Vue模板相关规则
'vue/html-indent': ['error', 2],
'vue/html-closing-bracket-newline': ['error', {
  'singleline': 'never',
  'multiline': 'always'
}],
'vue/html-self-closing': ['error', {
  'html': {
    'void': 'always',
    'normal': 'always',
    'component': 'always'
  }
}],
'vue/max-attributes-per-line': ['error', {
  'singleline': 3,
  'multiline': 1
}],
```

### 脚本规则

```javascript
// Vue脚本相关规则
'vue/component-tags-order': ['error', {
  'order': ['template', 'script', 'style']
}],
'vue/define-macros-order': ['error', {
  'order': ['defineProps', 'defineEmits', 'defineExpose']
}],
'vue/no-setup-props-destructure': 'error',
```

## TypeScript 集成配置

### 基础 TypeScript 规则

```javascript
// TypeScript核心规则
'@typescript-eslint/no-unused-vars': ['error', {
  argsIgnorePattern: '^_',
  varsIgnorePattern: '^_'
}],
'@typescript-eslint/no-explicit-any': 'warn',
'@typescript-eslint/prefer-const': 'error',
'@typescript-eslint/no-inferrable-types': 'error',
'@typescript-eslint/prefer-nullish-coalescing': 'error',
'@typescript-eslint/prefer-optional-chain': 'error',
```

### 类型检查规则

```javascript
// 需要类型信息的规则
'@typescript-eslint/await-thenable': 'error',
'@typescript-eslint/no-floating-promises': 'error',
'@typescript-eslint/no-misused-promises': 'error',
'@typescript-eslint/prefer-readonly': 'error',
'@typescript-eslint/require-await': 'error',
```

### 命名约定规则

```javascript
'@typescript-eslint/naming-convention': [
  'error',
  {
    'selector': 'variableLike',
    'format': ['camelCase', 'PascalCase', 'UPPER_CASE']
  },
  {
    'selector': 'typeLike',
    'format': ['PascalCase']
  },
  {
    'selector': 'interface',
    'format': ['PascalCase'],
    'prefix': ['I']
  },
  {
    'selector': 'enum',
    'format': ['PascalCase']
  }
],
```

## 配置文件示例

### package.json 脚本配置

```json
{
  "scripts": {
    "lint:oxlint": "oxlint . --fix -D correctness --ignore-path .gitignore",
    "lint:eslint": "eslint . --fix",
    "lint": "run-s lint:*",
    "format": "prettier --write src/",
    "type-check": "vue-tsc --build",
    "build": "run-p type-check \"build-only {@}\" --",
    "build-only": "vite build"
  }
}
```

#### 脚本说明

- **`lint:oxlint`**: 使用 oxlint 进行快速代码检查和修复
- **`lint:eslint`**: 使用 ESLint 进行详细代码检查和修复
- **`lint`**: 按顺序运行所有 lint 工具 (先 oxlint 后 eslint)
- **`run-s`**: 串行运行脚本 (来自 npm-run-all2)
- **`run-p`**: 并行运行脚本 (来自 npm-run-all2)

```

### .eslintignore 文件

```

# 构建输出

dist/
build/
coverage/

# 依赖

node_modules/

# 配置文件

_.config.js
_.config.ts

# 生成文件

_.min.js
_.bundle.js

# 临时文件

.tmp/
.cache/

# IDE 文件

.vscode/
.idea/

````

## VS Code 集成配置

### .vscode/settings.json

```json
{
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact",
    "vue"
  ],
  "eslint.useFlatConfig": true,
  "eslint.workingDirectories": ["."],
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit"
  },
  "eslint.format.enable": true,
  "eslint.lintTask.enable": true,
  "eslint.experimental.useFlatConfig": true
}
````

## ESLint 8 到 9 迁移指南

### 1. 升级依赖

```bash
# 卸载旧版本 (如果存在)
npm uninstall eslint @vue/eslint-config-vue @vue/eslint-config-typescript @vue/eslint-config-prettier

# 安装ESLint 9和Vue官方配置
npm install --save-dev eslint@^9.0.0
npm install --save-dev eslint-plugin-vue@^9.20.0
npm install --save-dev @vue/eslint-config-typescript@^13.0.0
npm install --save-dev @vue/eslint-config-prettier@^9.0.0

# 可选：测试和性能优化插件
npm install --save-dev @vitest/eslint-plugin@^1.0.0
npm install --save-dev eslint-plugin-playwright@^1.0.0
npm install --save-dev eslint-plugin-oxlint@^0.2.0
```

### 2. 配置文件迁移

**旧配置 (.eslintrc.js)**:

```javascript
module.exports = {
  root: true,
  extends: ["eslint:recommended", "@vue/typescript/recommended"],
  // ...
};
```

**新配置 (eslint.config.js) - Vue 官方推荐**:

```javascript
import pluginVue from "eslint-plugin-vue";
import {
  defineConfigWithVueTs,
  vueTsConfigs,
} from "@vue/eslint-config-typescript";
import skipFormatting from "@vue/eslint-config-prettier/skip-formatting";

export default defineConfigWithVueTs(
  {
    files: ["**/*.{ts,mts,tsx,vue}"],
  },
  pluginVue.configs["flat/essential"],
  vueTsConfigs.recommended,
  skipFormatting
);
```

### 3. 常见迁移问题

#### 问题 1: 配置不生效

**解决方案**: 确保使用 `eslint.config.js` 文件名，并删除旧的 `.eslintrc.*` 文件

#### 问题 2: 插件导入错误

**解决方案**: 使用 ES 模块导入语法

```javascript
// ❌ 错误
const vue = require("eslint-plugin-vue");

// ✅ 正确
import vue from "eslint-plugin-vue";
```

#### 问题 3: 规则覆盖不工作

**解决方案**: 使用数组配置，后面的配置会覆盖前面的

```javascript
export default [
  // 基础配置
  js.configs.recommended,

  // 特定文件配置
  {
    files: ["**/*.vue"],
    rules: {
      // Vue特定规则
    },
  },
];
```

### 4. 验证迁移

```bash
# 检查配置是否正确
npx eslint --print-config src/App.vue

# 运行lint检查
npm run lint

# 检查是否使用扁平配置
npx eslint --help | grep "flat"
```

## 最佳实践

### 1. 渐进式采用

```javascript
// 开始时使用较宽松的配置
rules: {
  '@typescript-eslint/no-explicit-any': 'warn', // 而不是 'error'
  'vue/multi-word-component-names': 'warn',     // 而不是 'error'
}

// 逐步提升到严格模式
rules: {
  '@typescript-eslint/no-explicit-any': 'error',
  'vue/multi-word-component-names': 'error',
}
```

### 2. 团队协作

- 确保所有团队成员使用相同的 ESLint 配置
- 在 CI/CD 中集成 ESLint 检查
- 定期更新和维护规则集

### 3. 性能优化

```javascript
// 使用缓存提升性能
module.exports = {
  cache: true,
  cacheLocation: "node_modules/.cache/eslint/",
  // 其他配置...
};
```

### 4. 规则禁用指南

```javascript
// 文件级别禁用
/* eslint-disable @typescript-eslint/no-explicit-any */

// 行级别禁用
const data: any = response.data; // eslint-disable-line @typescript-eslint/no-explicit-any

// 下一行禁用
// eslint-disable-next-line @typescript-eslint/no-explicit-any
const data: any = response.data;
```

##

自定义 ESLint 规则

### 团队特定规则配置

#### 1. 命名规范规则

```javascript
rules: {
  // 强制使用特定的命名模式
  '@typescript-eslint/naming-convention': [
    'error',
    // 变量和函数使用camelCase
    {
      'selector': ['variable', 'function'],
      'format': ['camelCase'],
      'leadingUnderscore': 'allow'
    },
    // 类和接口使用PascalCase
    {
      'selector': ['class', 'interface', 'typeAlias'],
      'format': ['PascalCase']
    },
    // 接口必须以I开头
    {
      'selector': 'interface',
      'format': ['PascalCase'],
      'prefix': ['I']
    },
    // 枚举使用PascalCase
    {
      'selector': 'enum',
      'format': ['PascalCase']
    },
    // 枚举成员使用UPPER_CASE
    {
      'selector': 'enumMember',
      'format': ['UPPER_CASE']
    },
    // 常量使用UPPER_CASE
    {
      'selector': 'variable',
      'modifiers': ['const', 'global'],
      'format': ['UPPER_CASE', 'camelCase']
    }
  ],

  // Vue组件命名必须使用多个单词
  'vue/multi-word-component-names': ['error', {
    'ignores': ['App', 'Home', 'Login', 'Dashboard']
  }],

  // 强制组件文件名与组件名一致
  'vue/match-component-file-name': ['error', {
    'extensions': ['vue'],
    'shouldMatchCase': true
  }]
}
```

#### 2. 代码结构规则

```javascript
rules: {
  // 限制函数复杂度
  'complexity': ['error', { 'max': 10 }],

  // 限制函数参数数量
  'max-params': ['error', { 'max': 4 }],

  // 限制文件行数
  'max-lines': ['warn', {
    'max': 300,
    'skipBlankLines': true,
    'skipComments': true
  }],

  // 强制使用一致的换行符
  'linebreak-style': ['error', 'unix'],

  // Vue组件中的属性顺序
  'vue/attributes-order': ['error', {
    'order': [
      'DEFINITION',
      'LIST_RENDERING',
      'CONDITIONALS',
      'RENDER_MODIFIERS',
      'GLOBAL',
      'UNIQUE',
      'TWO_WAY_BINDING',
      'OTHER_DIRECTIVES',
      'OTHER_ATTR',
      'EVENTS',
      'CONTENT'
    ]
  }],

  // 强制组件选项顺序
  'vue/order-in-components': ['error', {
    'order': [
      'el',
      'name',
      'key',
      'parent',
      'functional',
      ['delimiters', 'comments'],
      ['components', 'directives', 'filters'],
      'extends',
      'mixins',
      ['provide', 'inject'],
      'ROUTER_GUARDS',
      'layout',
      'middleware',
      'validate',
      'scrollToTop',
      'transition',
      'loading',
      'inheritAttrs',
      'model',
      ['props', 'propsData'],
      'emits',
      'setup',
      'asyncData',
      'data',
      'fetch',
      'head',
      'computed',
      'watch',
      'watchQuery',
      'LIFECYCLE_HOOKS',
      'methods',
      ['template', 'render'],
      'renderError'
    ]
  }]
}
```

#### 3. 安全性规则

```javascript
rules: {
  // 禁止使用eval
  'no-eval': 'error',

  // 禁止使用with语句
  'no-with': 'error',

  // 禁止使用__proto__
  'no-proto': 'error',

  // 禁止在Vue模板中使用v-html（防XSS）
  'vue/no-v-html': 'warn',

  // 禁止在computed中使用异步操作
  'vue/no-async-in-computed-properties': 'error',

  // 禁止在Vue组件中使用未声明的变量
  'vue/no-undef-components': 'error',

  // TypeScript安全规则
  '@typescript-eslint/no-unsafe-assignment': 'error',
  '@typescript-eslint/no-unsafe-call': 'error',
  '@typescript-eslint/no-unsafe-member-access': 'error',
  '@typescript-eslint/no-unsafe-return': 'error'
}
```

### 规则严重级别配置

#### 错误级别定义

```javascript
// 错误级别说明：
// "off" 或 0 - 关闭规则
// "warn" 或 1 - 警告级别，不会导致程序退出
// "error" 或 2 - 错误级别，会导致程序退出

rules: {
  // 必须修复的错误（阻止构建）
  'no-undef': 'error',
  'no-unused-vars': 'error',
  'vue/no-unused-components': 'error',
  '@typescript-eslint/no-explicit-any': 'error',

  // 建议修复的警告（不阻止构建）
  'no-console': 'warn',
  'no-debugger': 'warn',
  'vue/no-v-html': 'warn',
  '@typescript-eslint/prefer-nullish-coalescing': 'warn',

  // 代码风格相关（可配置为警告或错误）
  'indent': ['warn', 2],
  'quotes': ['warn', 'single'],
  'semi': ['warn', 'never'],
  'comma-dangle': ['warn', 'always-multiline']
}
```

#### 环境特定配置

```javascript
// 开发环境配置
const developmentRules = {
  "no-console": "warn",
  "no-debugger": "warn",
  "@typescript-eslint/no-explicit-any": "warn",
};

// 生产环境配置
const productionRules = {
  "no-console": "error",
  "no-debugger": "error",
  "@typescript-eslint/no-explicit-any": "error",
};

// 测试环境配置
const testRules = {
  "no-console": "off",
  "@typescript-eslint/no-explicit-any": "off",
  "@typescript-eslint/no-unsafe-assignment": "off",
};

module.exports = {
  rules: {
    ...baseRules,
    ...(process.env.NODE_ENV === "production"
      ? productionRules
      : developmentRules),
  },
  overrides: [
    {
      files: ["**/__tests__/**/*", "**/*.{test,spec}.*"],
      rules: testRules,
    },
  ],
};
```

### 规则禁用和例外处理指南

#### 1. 禁用规则的原则

```javascript
// ✅ 好的做法：有明确理由的禁用
// eslint-disable-next-line @typescript-eslint/no-explicit-any -- 第三方库类型定义不完整
const apiResponse: any = await thirdPartyApi.getData();

// ❌ 不好的做法：无理由的禁用
// eslint-disable-next-line @typescript-eslint/no-explicit-any
const data: any = someFunction();
```

#### 2. 文件级别禁用

```javascript
// 整个文件禁用特定规则（谨慎使用）
/* eslint-disable @typescript-eslint/no-explicit-any */

// 文件开头说明禁用原因
/**
 * 此文件禁用了no-explicit-any规则
 * 原因：处理动态API响应，类型结构不固定
 * TODO: 在API类型定义完善后移除此禁用
 */
/* eslint-disable @typescript-eslint/no-explicit-any */
```

#### 3. 行级别禁用

```javascript
// 单行禁用
const config: any = JSON.parse(configString); // eslint-disable-line @typescript-eslint/no-explicit-any

// 下一行禁用
// eslint-disable-next-line @typescript-eslint/no-explicit-any
const config: any = JSON.parse(configString);

// 多行禁用
/* eslint-disable @typescript-eslint/no-explicit-any */
const config: any = JSON.parse(configString);
const settings: any = JSON.parse(settingsString);
/* eslint-enable @typescript-eslint/no-explicit-any */
```

#### 4. 块级别禁用

```javascript
// Vue模板中禁用规则
<template>
  <!-- eslint-disable-next-line vue/no-v-html -->
  <div v-html="trustedContent"></div>
</template>

// 函数级别禁用
function legacyFunction() {
  /* eslint-disable prefer-const, no-var */
  var oldStyleVar = 'legacy code'
  // 保持旧代码风格以兼容性
  /* eslint-enable prefer-const, no-var */
}
```

#### 5. 条件禁用配置

```javascript
// 基于环境的条件禁用
rules: {
  'no-console': process.env.NODE_ENV === 'production' ? 'error' : 'warn',
  'no-debugger': process.env.NODE_ENV === 'production' ? 'error' : 'warn',
}

// 基于文件类型的禁用
overrides: [
  {
    files: ['*.config.js', '*.config.ts'],
    rules: {
      '@typescript-eslint/no-var-requires': 'off',
      'import/no-commonjs': 'off'
    }
  },
  {
    files: ['src/types/**/*.ts'],
    rules: {
      '@typescript-eslint/no-empty-interface': 'off'
    }
  }
]
```

#### 6. 临时禁用管理

```javascript
// 使用TODO注释管理临时禁用
// TODO: 移除此禁用，当API类型定义完成后
// eslint-disable-next-line @typescript-eslint/no-explicit-any
const response: any = await api.call();

// 使用FIXME标记需要修复的代码
// FIXME: 重构此函数以符合复杂度要求
// eslint-disable-next-line complexity
function complexFunction() {
  // 复杂逻辑...
}
```

### 自定义规则实现示例

#### 1. 创建自定义规则插件

```javascript
// eslint-plugin-team-rules/index.js
module.exports = {
  rules: {
    "no-chinese-comments": {
      meta: {
        type: "suggestion",
        docs: {
          description: "禁止在代码中使用中文注释",
          category: "Stylistic Issues",
        },
        schema: [],
      },
      create(context) {
        return {
          Program() {
            const comments = context.getSourceCode().getAllComments();
            comments.forEach((comment) => {
              if (/[\u4e00-\u9fa5]/.test(comment.value)) {
                context.report({
                  node: comment,
                  message: "请使用英文注释",
                });
              }
            });
          },
        };
      },
    },
  },
};
```

#### 2. 使用自定义规则

```javascript
// .eslintrc.js
module.exports = {
  plugins: ["team-rules"],
  rules: {
    "team-rules/no-chinese-comments": "warn",
  },
};
```

### 规则配置模板

#### 严格模式配置

```javascript
// .eslintrc.strict.js - 用于新项目
module.exports = {
  extends: ["./.eslintrc.js"],
  rules: {
    "@typescript-eslint/no-explicit-any": "error",
    "@typescript-eslint/explicit-function-return-type": "error",
    "vue/multi-word-component-names": "error",
    complexity: ["error", { max: 8 }],
    "max-lines": ["error", { max: 200 }],
  },
};
```

#### 宽松模式配置

```javascript
// .eslintrc.loose.js - 用于遗留项目迁移
module.exports = {
  extends: ["./.eslintrc.js"],
  rules: {
    "@typescript-eslint/no-explicit-any": "warn",
    "@typescript-eslint/explicit-function-return-type": "off",
    "vue/multi-word-component-names": "warn",
    complexity: "off",
    "max-lines": "off",
  },
};
```

## 规则维护和更新

### 1. 定期审查

- 每季度审查规则配置的有效性
- 收集团队反馈，调整规则严重级别
- 跟进 ESLint 和插件的更新

### 2. 规则变更流程

1. 提出规则变更建议
2. 团队讨论和评估影响
3. 在测试分支中试运行
4. 收集反馈并调整
5. 正式发布新配置

### 3. 文档维护

- 保持规则说明的及时更新
- 记录规则变更的历史和原因
- 提供规则使用的最佳实践示例
