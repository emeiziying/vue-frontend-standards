# Vue前端开发规范 - 目录索引

<div align="center">

![文档总数](https://img.shields.io/badge/文档总数-26-blue)
![覆盖范围](https://img.shields.io/badge/覆盖范围-完整开发流程-green)
![更新状态](https://img.shields.io/badge/状态-最新-brightgreen)

**📚 完整的Vue前端开发规范文档索引**

[🏠 返回首页](./README.md) • [🚀 快速开始](./QUICK_START.md) • [💡 代码示例](./EXAMPLES.md) • [📋 模板集合](./TEMPLATES.md)

</div>

---

## 📋 文档结构概览

本规范文档按照开发流程和功能模块进行组织，共包含 **26个专题文档**，涵盖Vue前端开发的各个方面。

### 📊 文档分布统计

| 分类 | 文档数量 | 覆盖内容 |
|------|----------|----------|
| 🏗️ 项目基础 | 1篇 | 项目结构、文件组织 |
| 🎯 Vue开发 | 4篇 | 组件、Props、生命周期、Composables |
| 💻 JS/TS规范 | 3篇 | 语法、类型、异步编程 |
| 🎨 模板样式 | 2篇 | HTML模板、CSS样式 |
| 🔧 工具配置 | 3篇 | ESLint、Prettier、构建工具 |
| 🗄️ 状态路由 | 4篇 | Pinia、Vue Router |
| 🔄 Git工作流 | 3篇 | 提交、分支、代码审查 |
| ⚡ 性能测试 | 4篇 | 性能优化、单元测试、集成测试 |
| 🛡️ 质量保证 | 2篇 | 错误处理、质量检查 |

## 📖 详细目录

### 🏗️ 第一部分：项目基础

<table>
<thead>
<tr>
<th width="60">序号</th>
<th width="200">📄 文档标题</th>
<th width="300">📝 主要内容</th>
<th width="120">🎯 适用场景</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>01</strong></td>
<td>

**[项目结构规范](./01-project-structure.md)**

</td>
<td>

• 标准目录结构  
• 文件命名规范  
• 模块组织最佳实践  
• 导入导出规范

</td>
<td>项目初始化</td>
</tr>
</tbody>
</table>

### 🎯 第二部分：Vue开发规范

<table>
<thead>
<tr>
<th width="60">序号</th>
<th width="200">📄 文档标题</th>
<th width="300">📝 主要内容</th>
<th width="120">🎯 适用场景</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>02</strong></td>
<td>

**[Vue组件结构](./02-vue-component-structure.md)**

</td>
<td>

• 组件文件结构  
• Template模板规范  
• Script Setup语法规范  
• 组件样式编写指南

</td>
<td>组件开发</td>
</tr>
<tr>
<td><strong>03</strong></td>
<td>

**[Props和Events](./03-vue-props-events.md)**

</td>
<td>

• Props类型定义标准  
• 事件命名和触发规范  
• 组件通信最佳实践

</td>
<td>组件通信</td>
</tr>
<tr>
<td><strong>04</strong></td>
<td>

**[Vue生命周期](./04-vue-lifecycle.md)**

</td>
<td>

• Composition API生命周期钩子  
• 副作用处理规范  
• 组件清理和内存管理

</td>
<td>生命周期管理</td>
</tr>
<tr>
<td><strong>05</strong></td>
<td>

**[自定义Composables](./05-vue-composables.md)**

</td>
<td>

• 自定义组合函数规范  
• 状态管理和异步处理  
• 业务逻辑封装最佳实践

</td>
<td>逻辑复用</td>
</tr>
</tbody>
</table>

### 💻 第三部分：JavaScript/TypeScript规范

<table>
<thead>
<tr>
<th width="60">序号</th>
<th width="200">📄 文档标题</th>
<th width="300">📝 主要内容</th>
<th width="120">🎯 适用场景</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>06</strong></td>
<td>

**[JS/TS基础](./05-javascript-typescript-basics.md)**

</td>
<td>

• 变量命名和声明规范  
• 函数定义和使用标准  
• 对象和数组操作规范

</td>
<td>基础语法</td>
</tr>
<tr>
<td><strong>07</strong></td>
<td>

**[TypeScript类型](./06-typescript-types.md)**

</td>
<td>

• 接口和类型别名使用指南  
• 泛型使用最佳实践  
• 类型断言和类型守卫规范

</td>
<td>类型系统</td>
</tr>
<tr>
<td><strong>08</strong></td>
<td>

**[异步编程](./07-async-programming.md)**

</td>
<td>

• Promise和async/await使用标准  
• 错误处理模式指南  
• 并发控制最佳实践

</td>
<td>异步处理</td>
</tr>
</tbody>
</table>

---

### 🎨 第四部分：模板与样式规范 (09-10)

#### 09. HTML模板标准
- **[09-HTML模板标准](./08-html-template-standards.md)**
  - Vue模板语法使用标准
  - 指令使用最佳实践
  - 条件渲染和列表渲染规范
  - 插槽使用指南

#### 10. CSS样式规范
- **[10-CSS样式标准](./09-css-style-standards.md)**
  - 基础CSS规范
  - TailwindCSS使用规范
  - 选择器命名规范
  - 响应式设计指南

---

### 🔧 第五部分：工具配置 (11-13)

#### 11. 代码质量检查
- **[11-ESLint配置](./10-eslint-configuration.md)**
  - ESLint基础配置
  - Vue项目专用规则集
  - TypeScript集成配置
  - 自定义ESLint规则

#### 12. 代码格式化
- **[12-Prettier配置](./11-prettier-configuration.md)**
  - Prettier配置文件模板
  - 代码格式化规则
  - 与ESLint集成配置
  - 编辑器集成设置

#### 13. 构建工具
- **[13-构建工具配置](./12-build-tool-configuration.md)**
  - Vite配置最佳实践
  - 环境变量管理规范
  - 打包优化配置标准
  - 开发服务器配置指南

---

### 🗄️ 第六部分：状态管理与路由 (14-17)

#### 14. Pinia状态管理结构
- **[14-Pinia Store结构](./13-pinia-store-structure.md)**
  - Store定义标准模板
  - 状态、getter、action组织规范
  - Store模块化最佳实践

#### 15. 状态管理模式
- **[15-Pinia状态模式](./14-pinia-state-patterns.md)**
  - 异步状态处理规范
  - 错误状态管理标准
  - Store间通信最佳实践

#### 16. 路由配置
- **[16-Vue Router配置](./15-vue-router-configuration.md)**
  - 路由定义标准格式
  - 嵌套路由组织指南
  - 动态路由处理规范

#### 17. 路由守卫与权限
- **[17-Vue Router守卫](./16-vue-router-guards.md)**
  - 导航守卫使用指南
  - 权限验证最佳实践
  - 路由元信息配置标准

---

### 🔄 第七部分：Git工作流 (18-20)

#### 18. 提交规范
- **[18-Git提交规范](./17-git-commit-standards.md)**
  - Commit message格式标准
  - Commit类型和范围规范
  - 提交频率和粒度指南

#### 19. 分支管理
- **[19-Git分支管理](./18-git-branch-management.md)**
  - 分支命名规范
  - 分支合并策略指南
  - 发布分支管理流程

#### 20. 代码审查
- **[20-代码审查规范](./19-code-review-standards.md)**
  - Pull Request模板
  - 代码审查检查清单
  - 审查流程和标准

---

### ⚡ 第八部分：性能优化 (21-22)

#### 21. Vue应用性能优化
- **[21-Vue性能优化](./20-vue-performance-optimization.md)**
  - 组件懒加载最佳实践
  - 虚拟滚动使用规范
  - 内存泄漏预防指南

#### 22. 构建性能优化
- **[22-构建优化](./21-build-optimization.md)**
  - 代码分割策略指南
  - 资源压缩配置标准
  - 缓存策略最佳实践

---

### 🧪 第九部分：测试规范 (23-24)

#### 23. 单元测试
- **[23-单元测试规范](./22-unit-testing-standards.md)**
  - Vue组件测试标准
  - 工具函数测试指南
  - 测试覆盖率要求

#### 24. 集成测试
- **[24-集成测试规范](./23-integration-testing-standards.md)**
  - E2E测试最佳实践
  - API测试规范
  - 测试数据管理指南

---

### 🛡️ 第十部分：质量保证 (25-26)

#### 25. 错误处理与日志
- **[25-错误处理日志](./24-error-handling-logging.md)**
  - 全局错误处理配置
  - 错误边界使用指南
  - 日志记录标准和格式
  - 错误上报机制规范

#### 26. 代码质量检查
- **[26-代码质量清单](./25-code-quality-checklist.md)**
  - 代码审查检查项目
  - 性能评估标准
  - 安全检查指南
  - 可维护性评估标准

---

## 文档使用指南

### 📖 阅读顺序建议

#### 新手开发者
1. 先阅读 [README.md](./README.md) 了解整体概况
2. 学习 [01-项目结构规范](./01-project-structure.md)
3. 掌握 [02-Vue组件结构](./02-vue-component-structure.md)
4. 配置开发工具 [10-ESLint配置](./10-eslint-configuration.md) 和 [11-Prettier配置](./11-prettier-configuration.md)
5. 逐步学习其他专题文档

#### 有经验的开发者
1. 快速浏览 [README.md](./README.md) 中的快速开始部分
2. 重点关注与当前项目相关的规范文档
3. 查看 [26-代码质量清单](./25-code-quality-checklist.md) 进行自检

#### 团队负责人
1. 全面了解所有规范内容
2. 重点关注 [18-20] Git工作流相关文档
3. 制定团队培训计划和实施策略

### 🔍 快速查找

#### 按开发阶段查找
- **项目初始化**: 01, 11, 12, 13
- **组件开发**: 02, 03, 04, 05, 09, 10
- **状态管理**: 14, 15, 16, 17
- **代码提交**: 18, 19, 20
- **性能优化**: 21, 22
- **测试部署**: 23, 24, 25, 26

#### 按技术栈查找
- **Vue相关**: 02, 03, 04, 05, 09, 21
- **TypeScript相关**: 06, 07, 08, 11
- **样式相关**: 10, 12
- **构建相关**: 13, 22
- **状态管理**: 14, 15
- **路由相关**: 16, 17

#### 按问题类型查找
- **代码规范问题**: 06, 07, 11, 12, 26
- **性能问题**: 21, 22
- **测试问题**: 23, 24
- **协作问题**: 18, 19, 20
- **错误处理**: 07, 24

### 📋 检查清单

#### 新项目启动检查
- [ ] 项目结构符合 [01-项目结构规范](./01-project-structure.md)
- [ ] 配置了 [10-ESLint](./10-eslint-configuration.md) 和 [11-Prettier](./11-prettier-configuration.md)
- [ ] 设置了 [12-构建工具](./12-build-tool-configuration.md)
- [ ] 建立了 [17-Git工作流](./17-git-commit-standards.md)

#### 代码提交前检查
- [ ] 代码符合 [02-Vue组件规范](./02-vue-component-structure.md)
- [ ] 通过了 [10-ESLint检查](./10-eslint-configuration.md)
- [ ] 编写了必要的 [22-单元测试](./22-unit-testing-standards.md)
- [ ] 提交信息符合 [17-Git规范](./17-git-commit-standards.md)

#### 代码审查检查
- [ ] 参考 [19-代码审查规范](./19-code-review-standards.md)
- [ ] 使用 [25-代码质量清单](./25-code-quality-checklist.md)
- [ ] 检查性能相关 [20-Vue性能优化](./20-vue-performance-optimization.md)

---

## 文档维护

### 📝 更新记录
- **2024-03-01**: 完成所有25个规范文档
- **2024-02-15**: 添加TailwindCSS相关规范
- **2024-02-01**: 完善测试和性能优化规范
- **2024-01-15**: 初始版本发布

### 🤝 贡献方式
1. 发现文档错误或改进建议，请提交Issue
2. 对规范内容有疑问，可以发起Discussion
3. 提交文档改进，请遵循Pull Request流程

### 📞 联系方式
- **维护团队**: 前端开发团队
- **技术支持**: 通过项目Issue系统
- **培训咨询**: 联系团队负责人

---

## 🔍 快速索引

### 按关键词查找

<details>
<summary><strong>🎯 Vue相关</strong></summary>

| 关键词 | 相关文档 | 页面链接 |
|--------|----------|----------|
| 组件结构 | 02-Vue组件结构 | [查看文档](./02-vue-component-structure.md) |
| Props定义 | 03-Props和Events | [查看文档](./03-vue-props-events.md) |
| 生命周期 | 04-Vue生命周期 | [查看文档](./04-vue-lifecycle.md) |
| 模板语法 | 08-HTML模板标准 | [查看文档](./08-html-template-standards.md) |
| 性能优化 | 20-Vue性能优化 | [查看文档](./20-vue-performance-optimization.md) |

</details>

<details>
<summary><strong>🔧 工具配置</strong></summary>

| 关键词 | 相关文档 | 页面链接 |
|--------|----------|----------|
| ESLint | 10-ESLint配置 | [查看文档](./10-eslint-configuration.md) |
| Prettier | 11-Prettier配置 | [查看文档](./11-prettier-configuration.md) |
| Vite构建 | 12-构建工具配置 | [查看文档](./12-build-tool-configuration.md) |
| TailwindCSS | 09-CSS样式标准 | [查看文档](./09-css-style-standards.md) |

</details>

<details>
<summary><strong>🗄️ 状态管理</strong></summary>

| 关键词 | 相关文档 | 页面链接 |
|--------|----------|----------|
| Pinia Store | 13-Pinia Store结构 | [查看文档](./13-pinia-store-structure.md) |
| 状态模式 | 14-Pinia状态模式 | [查看文档](./14-pinia-state-patterns.md) |
| 路由配置 | 15-Vue Router配置 | [查看文档](./15-vue-router-configuration.md) |
| 路由守卫 | 16-Vue Router守卫 | [查看文档](./16-vue-router-guards.md) |

</details>

<details>
<summary><strong>🔄 Git工作流</strong></summary>

| 关键词 | 相关文档 | 页面链接 |
|--------|----------|----------|
| 提交规范 | 17-Git提交规范 | [查看文档](./17-git-commit-standards.md) |
| 分支管理 | 18-Git分支管理 | [查看文档](./18-git-branch-management.md) |
| 代码审查 | 19-代码审查规范 | [查看文档](./19-code-review-standards.md) |

</details>

<details>
<summary><strong>🧪 测试相关</strong></summary>

| 关键词 | 相关文档 | 页面链接 |
|--------|----------|----------|
| 单元测试 | 22-单元测试规范 | [查看文档](./22-unit-testing-standards.md) |
| 集成测试 | 23-集成测试规范 | [查看文档](./23-integration-testing-standards.md) |
| 错误处理 | 24-错误处理日志 | [查看文档](./24-error-handling-logging.md) |
| 质量检查 | 25-代码质量清单 | [查看文档](./25-code-quality-checklist.md) |

</details>

### 按开发阶段查找

| 开发阶段 | 推荐阅读顺序 | 文档列表 |
|----------|--------------|----------|
| **🚀 项目初始化** | 1→2→3 | [01-项目结构](./01-project-structure.md) → [10-ESLint配置](./10-eslint-configuration.md) → [11-Prettier配置](./11-prettier-configuration.md) |
| **🎯 组件开发** | 1→2→3→4 | [02-Vue组件结构](./02-vue-component-structure.md) → [03-Props和Events](./03-vue-props-events.md) → [04-Vue生命周期](./04-vue-lifecycle.md) → [08-HTML模板标准](./08-html-template-standards.md) |
| **💻 逻辑编写** | 1→2→3 | [05-JS/TS基础](./05-javascript-typescript-basics.md) → [06-TypeScript类型](./06-typescript-types.md) → [07-异步编程](./07-async-programming.md) |
| **🗄️ 状态管理** | 1→2 | [13-Pinia Store结构](./13-pinia-store-structure.md) → [14-Pinia状态模式](./14-pinia-state-patterns.md) |
| **🎨 样式编写** | 1→2 | [09-CSS样式标准](./09-css-style-standards.md) → [20-Vue性能优化](./20-vue-performance-optimization.md) |
| **🔄 代码提交** | 1→2→3 | [17-Git提交规范](./17-git-commit-standards.md) → [18-Git分支管理](./18-git-branch-management.md) → [19-代码审查规范](./19-code-review-standards.md) |
| **🧪 测试部署** | 1→2→3 | [22-单元测试规范](./22-unit-testing-standards.md) → [23-集成测试规范](./23-integration-testing-standards.md) → [21-构建优化](./21-build-optimization.md) |

---

<div align="center">

### 📊 文档完成度

![项目基础](https://img.shields.io/badge/项目基础-100%25-brightgreen)
![Vue开发](https://img.shields.io/badge/Vue开发-100%25-brightgreen)
![JS/TS规范](https://img.shields.io/badge/JS/TS规范-100%25-brightgreen)
![工具配置](https://img.shields.io/badge/工具配置-100%25-brightgreen)
![状态路由](https://img.shields.io/badge/状态路由-100%25-brightgreen)
![Git工作流](https://img.shields.io/badge/Git工作流-100%25-brightgreen)
![性能测试](https://img.shields.io/badge/性能测试-100%25-brightgreen)
![质量保证](https://img.shields.io/badge/质量保证-100%25-brightgreen)

**📚 总体完成度: 100%**

---

*📅 本目录最后更新时间: 2024年3月1日*  
*👥 维护团队: 前端开发团队*  
*📧 反馈邮箱: frontend-team@company.com*

</div>