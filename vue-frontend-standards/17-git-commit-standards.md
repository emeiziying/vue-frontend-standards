# Git Commit 规范

## 概述

本文档定义了Vue前端项目的Git提交规范，旨在保持提交历史的清晰性和一致性，便于代码审查、版本管理和自动化工具处理。

## Commit Message 格式标准

### 基本格式

```
<type>(<scope>): <subject>

<body>

<footer>
```

### 格式说明

- **Header（必需）**：`<type>(<scope>): <subject>`
- **Body（可选）**：详细描述变更内容
- **Footer（可选）**：关联的Issue或Breaking Changes

### Header 规范

#### Type（类型）

| 类型 | 描述 | 示例 |
|------|------|------|
| `feat` | 新功能 | `feat(auth): add user login functionality` |
| `fix` | Bug修复 | `fix(api): resolve data fetching error` |
| `docs` | 文档更新 | `docs(readme): update installation guide` |
| `style` | 代码格式化（不影响功能） | `style(component): fix indentation` |
| `refactor` | 代码重构 | `refactor(utils): optimize data processing` |
| `perf` | 性能优化 | `perf(list): improve rendering performance` |
| `test` | 测试相关 | `test(user): add unit tests for user service` |
| `build` | 构建系统或依赖变更 | `build(deps): upgrade vue to 3.4.0` |
| `ci` | CI配置变更 | `ci(github): add automated testing workflow` |
| `chore` | 其他维护性变更 | `chore(config): update eslint rules` |
| `revert` | 回滚提交 | `revert: feat(auth): add user login` |

#### Scope（范围）

范围应该是受影响的模块或功能区域：

**组件相关**
- `component` - 通用组件
- `layout` - 布局组件
- `page` - 页面组件
- `form` - 表单组件

**功能模块**
- `auth` - 认证相关
- `user` - 用户管理
- `product` - 产品管理
- `order` - 订单管理

**技术层面**
- `api` - API接口
- `store` - 状态管理
- `router` - 路由配置
- `utils` - 工具函数
- `types` - 类型定义

**配置和工具**
- `config` - 配置文件
- `deps` - 依赖管理
- `build` - 构建配置
- `test` - 测试配置

#### Subject（主题）

- 使用动词开头，现在时态
- 首字母小写
- 结尾不加句号
- 限制在50个字符以内
- 简洁明了地描述变更内容

**好的示例：**
```
feat(auth): add password reset functionality
fix(api): handle network timeout errors
docs(component): update props documentation
```

**不好的示例：**
```
Added new feature  // 缺少scope和具体描述
Fix bug.          // 太模糊，有句号
FEAT: New login   // 格式错误
```

### Body 规范

Body部分应该包含：

1. **变更动机**：为什么做这个变更
2. **变更内容**：具体做了什么
3. **影响范围**：可能影响的功能或模块

**示例：**
```
feat(auth): add two-factor authentication

Implement 2FA to enhance account security. Users can now enable
TOTP-based authentication using apps like Google Authenticator.

- Add TOTP generation and validation
- Create 2FA setup wizard component
- Update login flow to handle 2FA verification
- Add backup codes for account recovery

Affects: login process, user settings, security policies
```

### Footer 规范

Footer用于：

1. **关联Issue**：`Closes #123`, `Fixes #456`, `Refs #789`
2. **Breaking Changes**：以`BREAKING CHANGE:`开头
3. **Co-authored-by**：多人协作时标注贡献者

**示例：**
```
feat(api): update user authentication endpoint

BREAKING CHANGE: The authentication endpoint now requires
a different request format. Update client code accordingly.

Closes #123
Co-authored-by: Jane Doe <jane@example.com>
```

## Commit 类型和范围规范

### 功能开发流程

```
feat(scope): 实现新功能
├── test(scope): 添加功能测试
├── docs(scope): 更新功能文档
└── style(scope): 调整代码格式
```

### Bug修复流程

```
fix(scope): 修复具体问题
├── test(scope): 添加回归测试
└── docs(scope): 更新相关文档
```

### 重构流程

```
refactor(scope): 重构代码结构
├── test(scope): 更新相关测试
├── perf(scope): 性能优化（如需要）
└── docs(scope): 更新技术文档
```

## 提交频率和粒度指南

### 提交频率原则

1. **小步快跑**：频繁提交小的、完整的变更
2. **功能完整**：每次提交应该是一个完整的逻辑单元
3. **可回滚**：每个提交都应该是可以安全回滚的
4. **可测试**：提交后代码应该能够正常运行和测试

### 提交粒度标准

#### ✅ 合适的提交粒度

```bash
# 单一功能实现
feat(auth): add login form validation

# 单个Bug修复
fix(api): resolve user data caching issue

# 单个配置更新
build(deps): upgrade vue-router to 4.2.0

# 单个文档更新
docs(component): add usage examples for Button component
```

#### ❌ 不合适的提交粒度

```bash
# 太大：包含多个不相关的变更
feat: add login, fix bugs, update docs

# 太小：不完整的功能
feat(auth): add login form (missing validation)

# 太模糊：无法理解具体变更
fix: various fixes

# 混合类型：一个提交包含多种类型的变更
feat(auth): add login and fix styling issues
```

### 提交时机建议

#### 何时应该提交

- ✅ 完成一个完整的功能点
- ✅ 修复一个具体的Bug
- ✅ 完成代码重构的一个阶段
- ✅ 更新配置或依赖
- ✅ 添加或更新测试
- ✅ 更新文档

#### 何时不应该提交

- ❌ 代码无法编译或运行
- ❌ 测试未通过
- ❌ 功能只完成一半
- ❌ 包含调试代码或临时文件
- ❌ 违反代码规范

### 工作流建议

#### 日常开发流程

1. **开始工作**：从最新的主分支创建功能分支
2. **小步开发**：每完成一个小功能点就提交
3. **定期同步**：定期从主分支拉取最新代码
4. **完成功能**：功能完成后进行最终测试
5. **准备合并**：整理提交历史，准备Pull Request

#### 提交前检查清单

- [ ] 代码能够正常编译和运行
- [ ] 所有测试都通过
- [ ] 代码符合ESLint和Prettier规范
- [ ] 提交信息符合规范格式
- [ ] 不包含敏感信息或调试代码
- [ ] 相关文档已更新

## 工具和自动化

### Commitizen 配置

使用Commitizen来标准化提交流程：

```bash
# 安装
npm install -g commitizen
npm install -g cz-conventional-changelog

# 配置
echo '{ "path": "cz-conventional-changelog" }' > ~/.czrc

# 使用
git cz  # 替代 git commit
```

### Commitlint 配置

使用Commitlint验证提交信息：

```javascript
// commitlint.config.js
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'type-enum': [
      2,
      'always',
      [
        'feat', 'fix', 'docs', 'style', 'refactor',
        'perf', 'test', 'build', 'ci', 'chore', 'revert'
      ]
    ],
    'scope-enum': [
      2,
      'always',
      [
        'auth', 'user', 'product', 'order', 'api',
        'store', 'router', 'component', 'utils', 'config'
      ]
    ],
    'subject-max-length': [2, 'always', 50],
    'body-max-line-length': [2, 'always', 72]
  }
}
```

### Git Hooks 配置

```bash
# 安装 husky
npm install --save-dev husky

# 配置 commit-msg hook
npx husky add .husky/commit-msg 'npx commitlint --edit $1'

# 配置 pre-commit hook
npx husky add .husky/pre-commit 'npm run lint && npm run test'
```

## 示例和模板

### 常见场景示例

#### 新功能开发
```
feat(auth): implement OAuth2 login integration

Add support for Google and GitHub OAuth2 authentication.
Users can now sign in using their existing accounts from
these providers, improving user experience and reducing
registration friction.

- Integrate OAuth2 client libraries
- Create provider selection UI
- Handle OAuth callback and token exchange
- Update user model to support external accounts
- Add comprehensive error handling

Closes #145
```

#### Bug修复
```
fix(api): resolve race condition in data fetching

Fix intermittent data loading failures caused by concurrent
API requests. The issue occurred when users navigated quickly
between pages, causing requests to complete out of order.

- Add request cancellation for pending requests
- Implement proper cleanup in component unmount
- Add loading state management
- Include retry mechanism for failed requests

Fixes #234
```

#### 重构
```
refactor(store): migrate from Vuex to Pinia

Replace Vuex with Pinia for better TypeScript support
and improved developer experience. This change maintains
the same API surface while providing better type safety.

- Convert all Vuex modules to Pinia stores
- Update component imports and usage
- Migrate state persistence logic
- Update tests to work with new store structure

BREAKING CHANGE: Store import paths have changed.
Update imports from '@/store/modules/user' to '@/stores/user'

Refs #189
```

### 提交信息模板

创建Git提交模板：

```bash
# 创建模板文件
cat > ~/.gitmessage << EOF
# <type>(<scope>): <subject>
#
# <body>
#
# <footer>

# Type: feat, fix, docs, style, refactor, perf, test, build, ci, chore, revert
# Scope: auth, user, api, store, router, component, utils, config, etc.
# Subject: 50 chars max, no period, imperative mood
#
# Body: Explain what and why (not how). Wrap at 72 chars.
#
# Footer: Issue references, breaking changes, co-authors
EOF

# 配置Git使用模板
git config --global commit.template ~/.gitmessage
```

## 最佳实践总结

### Do's ✅

1. **保持一致性**：始终遵循既定的格式规范
2. **描述清晰**：让其他开发者能够快速理解变更内容
3. **原子性提交**：每个提交只做一件事
4. **及时提交**：完成一个逻辑单元就提交
5. **使用工具**：利用Commitizen、Commitlint等工具

### Don'ts ❌

1. **避免模糊描述**：如"fix bugs"、"update code"
2. **不要混合变更**：一个提交包含多个不相关的修改
3. **避免巨大提交**：包含过多文件和变更的提交
4. **不要提交半成品**：确保代码能够正常运行
5. **避免情绪化信息**：保持专业和客观的描述

通过遵循这些Git提交规范，团队可以维护清晰的项目历史，提高协作效率，并为自动化工具提供良好的基础。