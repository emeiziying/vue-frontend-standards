# Vue项目结构规范

## 概述

本文档定义了Vue前端项目的标准目录结构、文件命名规范和模块组织最佳实践，旨在确保项目的可维护性、可扩展性和团队协作效率。

## 标准目录结构

### 基础项目结构

```
project-name/
├── public/                     # 静态资源目录
│   ├── favicon.ico
│   └── index.html
├── src/                        # 源代码目录
│   ├── assets/                 # 静态资源
│   │   ├── images/            # 图片资源
│   │   ├── icons/             # 图标资源
│   │   ├── fonts/             # 字体文件
│   │   └── styles/            # 全局样式
│   ├── components/            # 通用组件
│   │   ├── base/              # 基础组件
│   │   ├── common/            # 通用业务组件
│   │   └── layout/            # 布局组件
│   ├── views/                 # 页面组件
│   │   ├── home/              # 首页模块
│   │   ├── user/              # 用户模块
│   │   └── admin/             # 管理模块
│   ├── stores/                # Pinia状态管理
│   │   ├── modules/           # 状态模块
│   │   └── index.ts           # Store入口
│   ├── router/                # 路由配置
│   │   ├── modules/           # 路由模块
│   │   └── index.ts           # 路由入口
│   ├── composables/           # 组合式函数
│   ├── utils/                 # 工具函数
│   │   ├── helpers/           # 辅助函数
│   │   ├── validators/        # 验证函数
│   │   └── constants/         # 常量定义
│   ├── services/              # API服务
│   │   ├── api/               # API接口
│   │   └── http/              # HTTP配置
│   ├── types/                 # TypeScript类型定义
│   │   ├── api/               # API类型
│   │   ├── components/        # 组件类型
│   │   └── global.d.ts        # 全局类型
│   ├── plugins/               # Vue插件
│   ├── directives/            # 自定义指令
│   ├── App.vue                # 根组件
│   └── main.ts                # 应用入口
├── tests/                     # 测试文件
│   ├── unit/                  # 单元测试
│   ├── e2e/                   # 端到端测试
│   └── fixtures/              # 测试数据
├── docs/                      # 项目文档
├── .vscode/                   # VS Code配置
├── .eslintrc.js               # ESLint配置
├── .prettierrc                # Prettier配置
├── tailwind.config.js         # TailwindCSS配置
├── vite.config.ts             # Vite配置
├── tsconfig.json              # TypeScript配置
├── package.json               # 项目依赖
└── README.md                  # 项目说明
```

### 目录说明

#### `/src/assets/` - 静态资源目录
- **images/**: 存放项目中使用的图片资源，按功能模块分类
- **icons/**: 存放图标文件，支持SVG、PNG等格式
- **fonts/**: 存放自定义字体文件
- **styles/**: 存放全局样式文件，包括变量、混入、基础样式等

#### `/src/components/` - 组件目录
- **base/**: 基础UI组件，如Button、Input、Modal等
- **common/**: 通用业务组件，可在多个页面复用
- **layout/**: 布局相关组件，如Header、Sidebar、Footer等

#### `/src/views/` - 页面组件目录
按业务模块组织，每个模块包含该模块的所有页面组件

#### `/src/stores/` - 状态管理目录
- **modules/**: 按功能模块划分的Store文件
- **index.ts**: Store的统一导出和配置

#### `/src/utils/` - 工具函数目录
- **helpers/**: 通用辅助函数
- **validators/**: 数据验证函数
- **constants/**: 常量定义文件

## 文件命名规范

### 组件文件命名

#### Vue组件文件
- **格式**: PascalCase（帕斯卡命名法）
- **扩展名**: `.vue`
- **示例**:
  ```
  UserProfile.vue          ✅ 正确
  LoginForm.vue           ✅ 正确
  DataTable.vue           ✅ 正确
  
  userProfile.vue         ❌ 错误
  login-form.vue          ❌ 错误
  data_table.vue          ❌ 错误
  ```

#### 基础组件命名
基础组件应以`Base`、`App`或`V`作为前缀：
```
BaseButton.vue           ✅ 推荐
BaseInput.vue            ✅ 推荐
AppHeader.vue            ✅ 推荐
VIcon.vue                ✅ 推荐
```

#### 单例组件命名
只应该拥有单个活跃实例的组件应该以`The`前缀命名：
```
TheHeader.vue            ✅ 正确
TheSidebar.vue           ✅ 正确
TheFooter.vue            ✅ 正确
```

### JavaScript/TypeScript文件命名

#### 工具函数和服务文件
- **格式**: camelCase（驼峰命名法）
- **扩展名**: `.js` 或 `.ts`
- **示例**:
  ```
  formatUtils.ts           ✅ 正确
  apiService.ts            ✅ 正确
  dateHelpers.js           ✅ 正确
  
  format-utils.ts          ❌ 错误
  ApiService.ts            ❌ 错误
  date_helpers.js          ❌ 错误
  ```

#### 常量文件命名
- **格式**: SCREAMING_SNAKE_CASE（全大写下划线）
- **示例**:
  ```
  API_CONSTANTS.ts         ✅ 正确
  USER_ROLES.ts            ✅ 正确
  ERROR_MESSAGES.ts        ✅ 正确
  ```

#### 类型定义文件命名
- **格式**: camelCase + `.types.ts` 后缀
- **示例**:
  ```
  user.types.ts            ✅ 正确
  api.types.ts             ✅ 正确
  component.types.ts       ✅ 正确
  ```

### 目录命名规范

#### 通用目录命名
- **格式**: kebab-case（短横线命名法）
- **示例**:
  ```
  user-management/         ✅ 正确
  data-visualization/      ✅ 正确
  api-services/            ✅ 正确
  
  userManagement/          ❌ 错误
  data_visualization/      ❌ 错误
  ApiServices/             ❌ 错误
  ```

#### 特殊目录命名
某些约定俗成的目录保持原有命名：
```
components/              ✅ 保持原样
composables/             ✅ 保持原样
utils/                   ✅ 保持原样
stores/                  ✅ 保持原样
```

## 模块组织最佳实践

### 1. 按功能模块组织

#### 页面模块组织
每个业务模块应包含完整的功能实现：
```
src/views/user/
├── components/          # 模块专用组件
│   ├── UserCard.vue
│   └── UserForm.vue
├── composables/         # 模块专用组合函数
│   └── useUser.ts
├── types/              # 模块类型定义
│   └── user.types.ts
├── UserList.vue        # 用户列表页
├── UserDetail.vue      # 用户详情页
└── index.ts            # 模块导出
```

#### Store模块组织
```
src/stores/modules/
├── user/
│   ├── index.ts         # 用户Store
│   ├── types.ts         # Store类型
│   └── actions.ts       # 复杂异步操作
├── auth/
│   └── index.ts
└── app/
    └── index.ts
```

### 2. 组件分层策略

#### 三层组件架构
1. **基础组件层** (`src/components/base/`)
   - 纯UI组件，无业务逻辑
   - 高度可复用
   - 通过props接收数据

2. **业务组件层** (`src/components/common/`)
   - 包含特定业务逻辑
   - 可在多个页面复用
   - 可能包含状态管理

3. **页面组件层** (`src/views/`)
   - 页面级组件
   - 组合多个业务组件
   - 处理路由和页面状态

### 3. 导入导出规范

#### 统一导出模式
每个模块目录应包含`index.ts`文件作为统一导出：
```typescript
// src/components/base/index.ts
export { default as BaseButton } from './BaseButton.vue'
export { default as BaseInput } from './BaseInput.vue'
export { default as BaseModal } from './BaseModal.vue'

// src/utils/index.ts
export * from './helpers'
export * from './validators'
export * from './constants'
```

#### 导入路径规范
使用绝对路径导入，配置路径别名：
```typescript
// vite.config.ts 配置
resolve: {
  alias: {
    '@': path.resolve(__dirname, 'src'),
    '@/components': path.resolve(__dirname, 'src/components'),
    '@/utils': path.resolve(__dirname, 'src/utils'),
    '@/types': path.resolve(__dirname, 'src/types')
  }
}

// 使用示例
import { BaseButton } from '@/components/base'
import { formatDate } from '@/utils/helpers'
import type { User } from '@/types/user.types'
```

### 4. 文件组织原则

#### 单一职责原则
每个文件应该只负责一个功能：
```
❌ 错误：一个文件包含多个组件
// UserComponents.vue - 包含UserCard、UserForm等多个组件

✅ 正确：每个组件独立文件
// UserCard.vue - 只包含UserCard组件
// UserForm.vue - 只包含UserForm组件
```

#### 就近原则
相关文件应该放在同一目录下：
```
src/views/user/
├── components/          # 用户模块专用组件
├── composables/         # 用户模块专用逻辑
├── UserList.vue         # 用户列表页面
└── UserDetail.vue       # 用户详情页面
```

#### 依赖方向原则
- 上层模块可以依赖下层模块
- 下层模块不应该依赖上层模块
- 同层模块之间避免循环依赖

```
页面组件 (views/)
    ↓ 可以依赖
业务组件 (components/common/)
    ↓ 可以依赖  
基础组件 (components/base/)
    ↓ 可以依赖
工具函数 (utils/)
```

### 5. 配置文件组织

#### 环境配置
```
src/config/
├── index.ts             # 配置入口
├── development.ts       # 开发环境配置
├── production.ts        # 生产环境配置
└── staging.ts           # 测试环境配置
```

#### 路由配置
```
src/router/
├── index.ts             # 路由主配置
├── guards.ts            # 路由守卫
└── modules/             # 路由模块
    ├── user.ts
    ├── admin.ts
    └── public.ts
```

## 最佳实践总结

### ✅ 推荐做法

1. **保持目录结构扁平化**，避免过深的嵌套
2. **使用一致的命名规范**，便于团队协作
3. **按功能模块组织代码**，提高可维护性
4. **配置路径别名**，简化导入路径
5. **每个模块提供统一导出**，便于使用
6. **遵循单一职责原则**，每个文件职责明确

### ❌ 避免做法

1. **不要混合不同的命名风格**
2. **不要创建过深的目录嵌套**（超过4层）
3. **不要在一个文件中定义多个组件**
4. **不要使用相对路径进行跨模块导入**
5. **不要忽略文件和目录的命名规范**
6. **不要创建循环依赖**

通过遵循这些项目结构规范，可以确保Vue项目具有良好的可维护性、可扩展性和团队协作效率。