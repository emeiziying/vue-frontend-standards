# Vue前端开发规范 - 文档索引

## 🔍 快速索引

### 按主题分类

#### 🏗️ 项目基础
- **项目结构**: [01-项目结构规范](./01-project-structure.md)
  - 标准目录结构、文件命名规范、模块组织

#### 🎯 Vue开发
- **组件开发**: [02-Vue组件结构](./02-vue-component-structure.md)
  - 组件模板、Script Setup、样式规范
- **组件通信**: [03-Props和Events](./03-vue-props-events.md)
  - Props定义、事件处理、类型约束
- **生命周期**: [04-Vue生命周期](./04-vue-lifecycle.md)
  - Composition API、副作用处理、清理

#### 💻 JavaScript/TypeScript
- **基础语法**: [05-JavaScript/TypeScript基础](./05-javascript-typescript-basics.md)
  - 变量、函数、对象操作规范
- **类型系统**: [06-TypeScript类型](./06-typescript-types.md)
  - 接口、泛型、类型守卫
- **异步编程**: [07-异步编程](./07-async-programming.md)
  - Promise、async/await、错误处理

#### 🎨 模板与样式
- **HTML模板**: [08-HTML模板标准](./08-html-template-standards.md)
  - Vue指令、条件渲染、插槽
- **CSS样式**: [09-CSS样式标准](./09-css-style-standards.md)
  - CSS规范、TailwindCSS、响应式设计

#### 🔧 工具配置
- **ESLint**: [10-ESLint配置](./10-eslint-configuration.md) ⭐ **ESLint 9 + Vue官方推荐配置**
  - 代码质量检查、Vue官方扁平配置、规则定制
- **Prettier**: [11-Prettier配置](./11-prettier-configuration.md)
  - 代码格式化、编辑器集成
- **构建工具**: [12-构建工具配置](./12-build-tool-configuration.md)
  - Vite配置、环境变量、打包优化

#### 🗄️ 状态管理与路由
- **Pinia结构**: [13-Pinia Store结构](./13-pinia-store-structure.md)
  - Store定义、模块化组织
- **状态模式**: [14-Pinia状态模式](./14-pinia-state-patterns.md)
  - 异步处理、错误管理
- **路由配置**: [15-Vue Router配置](./15-vue-router-configuration.md)
  - 路由定义、嵌套路由
- **路由守卫**: [16-Vue Router守卫](./16-vue-router-guards.md)
  - 导航守卫、权限控制

#### 🔄 Git工作流
- **提交规范**: [17-Git提交规范](./17-git-commit-standards.md)
  - Commit格式、类型定义
- **分支管理**: [18-Git分支管理](./18-git-branch-management.md)
  - 分支策略、合并流程
- **代码审查**: [19-代码审查规范](./19-code-review-standards.md)
  - PR模板、审查清单

#### ⚡ 性能优化
- **Vue性能**: [20-Vue性能优化](./20-vue-performance-optimization.md)
  - 组件优化、懒加载、内存管理
- **构建优化**: [21-构建优化](./21-build-optimization.md)
  - 代码分割、压缩、缓存

#### 🧪 测试规范
- **单元测试**: [22-单元测试规范](./22-unit-testing-standards.md)
  - 组件测试、工具函数测试
- **集成测试**: [23-集成测试规范](./23-integration-testing-standards.md)
  - E2E测试、API测试

#### 🛡️ 质量保证
- **错误处理**: [24-错误处理日志](./24-error-handling-logging.md)
  - 全局错误处理、日志规范
- **质量清单**: [25-代码质量清单](./25-code-quality-checklist.md)
  - 检查项目、评估标准

---

## 🚀 快速导航

### 新手入门路径
1. [README.md](./README.md) - 总体概览
2. [QUICK_START.md](./QUICK_START.md) - 5分钟快速设置
3. [01-项目结构规范](./01-project-structure.md) - 了解项目组织
4. [02-Vue组件结构](./02-vue-component-structure.md) - 学习组件开发
5. [10-ESLint配置](./10-eslint-configuration.md) - 配置代码检查

### 配置文件快速查找
- **ESLint 9配置**: [eslint.config.js模板](./10-eslint-configuration.md#eslintconfigjs-配置模板-vue官方推荐)
- **Prettier配置**: [.prettierrc模板](./11-prettier-configuration.md)
- **Vite配置**: [vite.config.ts模板](./12-build-tool-configuration.md)
- **TailwindCSS配置**: [tailwind.config.js模板](./09-css-style-standards.md)
- **TypeScript配置**: [tsconfig.json模板](./EXAMPLES.md#typescript配置模板)

### 代码示例快速查找
- **Vue组件示例**: [EXAMPLES.md#vue组件示例](./EXAMPLES.md#vue组件示例)
- **Pinia Store示例**: [EXAMPLES.md#pinia-store示例](./EXAMPLES.md#pinia-store示例)
- **工具函数示例**: [EXAMPLES.md#工具函数示例](./EXAMPLES.md#工具函数示例)
- **API服务示例**: [EXAMPLES.md#api服务模板](./EXAMPLES.md#api服务模板)

---

## 🔧 按开发阶段分类

### 项目初始化
- [01-项目结构规范](./01-project-structure.md)
- [10-ESLint配置](./10-eslint-configuration.md) ⭐ **ESLint 9**
- [11-Prettier配置](./11-prettier-configuration.md)
- [12-构建工具配置](./12-build-tool-configuration.md)

### 组件开发
- [02-Vue组件结构](./02-vue-component-structure.md)
- [03-Props和Events](./03-vue-props-events.md)
- [04-Vue生命周期](./04-vue-lifecycle.md)
- [08-HTML模板标准](./08-html-template-standards.md)
- [09-CSS样式标准](./09-css-style-standards.md)

### 状态管理
- [13-Pinia Store结构](./13-pinia-store-structure.md)
- [14-Pinia状态模式](./14-pinia-state-patterns.md)
- [15-Vue Router配置](./15-vue-router-configuration.md)
- [16-Vue Router守卫](./16-vue-router-guards.md)

### 代码提交
- [17-Git提交规范](./17-git-commit-standards.md)
- [18-Git分支管理](./18-git-branch-management.md)
- [19-代码审查规范](./19-code-review-standards.md)

### 性能优化
- [20-Vue性能优化](./20-vue-performance-optimization.md)
- [21-构建优化](./21-build-optimization.md)

### 测试部署
- [22-单元测试规范](./22-unit-testing-standards.md)
- [23-集成测试规范](./23-integration-testing-standards.md)
- [24-错误处理日志](./24-error-handling-logging.md)
- [25-代码质量清单](./25-code-quality-checklist.md)

---

## 🏷️ 按技术栈分类

### Vue.js相关
- [02-Vue组件结构](./02-vue-component-structure.md)
- [03-Props和Events](./03-vue-props-events.md)
- [04-Vue生命周期](./04-vue-lifecycle.md)
- [08-HTML模板标准](./08-html-template-standards.md)
- [20-Vue性能优化](./20-vue-performance-optimization.md)

### TypeScript相关
- [05-JavaScript/TypeScript基础](./05-javascript-typescript-basics.md)
- [06-TypeScript类型](./06-typescript-types.md)
- [07-异步编程](./07-async-programming.md)
- [10-ESLint配置](./10-eslint-configuration.md) ⭐ **ESLint 9支持**

### 样式相关
- [09-CSS样式标准](./09-css-style-standards.md)
- [11-Prettier配置](./11-prettier-configuration.md)

### 构建工具相关
- [12-构建工具配置](./12-build-tool-configuration.md)
- [21-构建优化](./21-build-optimization.md)

### 状态管理相关
- [13-Pinia Store结构](./13-pinia-store-structure.md)
- [14-Pinia状态模式](./14-pinia-state-patterns.md)

### 路由相关
- [15-Vue Router配置](./15-vue-router-configuration.md)
- [16-Vue Router守卫](./16-vue-router-guards.md)

---

## 📋 检查清单

### 新项目设置检查清单
- [ ] 项目结构符合 [01-项目结构规范](./01-project-structure.md)
- [ ] 配置了 [ESLint 9](./10-eslint-configuration.md) ⭐
- [ ] 配置了 [Prettier](./11-prettier-configuration.md)
- [ ] 设置了 [构建工具](./12-build-tool-configuration.md)
- [ ] 建立了 [Git工作流](./17-git-commit-standards.md)

### 组件开发检查清单
- [ ] 组件结构符合 [Vue组件规范](./02-vue-component-structure.md)
- [ ] Props和Events符合 [通信规范](./03-vue-props-events.md)
- [ ] 生命周期使用符合 [生命周期规范](./04-vue-lifecycle.md)
- [ ] 模板语法符合 [HTML模板标准](./08-html-template-standards.md)
- [ ] 样式编写符合 [CSS样式标准](./09-css-style-standards.md)

### 代码提交检查清单
- [ ] 代码通过 [ESLint检查](./10-eslint-configuration.md)
- [ ] 代码格式符合 [Prettier规范](./11-prettier-configuration.md)
- [ ] 提交信息符合 [Git提交规范](./17-git-commit-standards.md)
- [ ] 通过了 [代码审查](./19-code-review-standards.md)
- [ ] 编写了必要的 [单元测试](./22-unit-testing-standards.md)

### 性能优化检查清单
- [ ] 应用了 [Vue性能优化](./20-vue-performance-optimization.md)
- [ ] 配置了 [构建优化](./21-build-optimization.md)
- [ ] 实施了 [错误处理](./24-error-handling-logging.md)
- [ ] 通过了 [质量检查](./25-code-quality-checklist.md)

---

## 🆕 最新更新

### Vue官方最新模板同步 (2024-03-01)
- ⭐ **重要更新**: 完全同步Vue官方最新create-vue模板
- 🚀 **性能革命**: 集成oxlint实现极速代码检查 (比ESLint快10-100倍)
- 🔄 **配置格式**: 使用`defineConfigWithVueTs`和Vue官方扁平配置
- 📦 **依赖最新**: 同步Vue 3.5.13、TypeScript 5.8等最新版本
- ⚡ **开发体验**: oxlint + ESLint组合策略，开发时快速检查，提交前详细检查
- 🔧 **脚本优化**: 使用npm-run-all2进行高效的脚本管理

### 文档完善 (2024-03-01)
- 📚 **完整示例**: 添加了[EXAMPLES.md](./EXAMPLES.md)完整项目示例
- 🚀 **快速开始**: 更新了[QUICK_START.md](./QUICK_START.md)快速设置指南
- 📋 **目录导航**: 完善了[TABLE_OF_CONTENTS.md](./TABLE_OF_CONTENTS.md)文档目录
- 🔍 **索引导航**: 新增了本索引文件，提供多维度导航

---

## 📞 获取帮助

### 文档问题
- 📖 查看 [README.md](./README.md) 获取总体介绍
- 🚀 查看 [QUICK_START.md](./QUICK_START.md) 快速上手
- 📚 查看 [EXAMPLES.md](./EXAMPLES.md) 获取完整示例

### 技术问题
- 🔧 ESLint配置问题: 参考 [10-ESLint配置](./10-eslint-configuration.md)
- 🎯 Vue开发问题: 参考 [02-Vue组件结构](./02-vue-component-structure.md)
- 📘 TypeScript问题: 参考 [06-TypeScript类型](./06-typescript-types.md)

### 团队协作
- 🔄 Git工作流: 参考 [17-Git提交规范](./17-git-commit-standards.md)
- 👥 代码审查: 参考 [19-代码审查规范](./19-code-review-standards.md)
- ✅ 质量保证: 参考 [25-代码质量清单](./25-code-quality-checklist.md)

---

*本索引最后更新时间: 2024年3月1日*  
*ESLint 9升级完成 ⭐*