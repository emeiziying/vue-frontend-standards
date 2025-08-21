# 代码审查规范

## 概述

本文档定义了Vue前端项目的代码审查标准和流程，旨在通过系统化的代码审查提高代码质量、知识共享和团队协作效率。代码审查是确保代码质量和维护团队编码标准的重要环节。

## Pull Request 模板

### 基础PR模板

```markdown
## 📋 变更描述

### 🎯 变更类型
- [ ] 🚀 新功能 (feature)
- [ ] 🐛 Bug修复 (bugfix)
- [ ] 📚 文档更新 (docs)
- [ ] 🎨 代码格式化 (style)
- [ ] ♻️ 代码重构 (refactor)
- [ ] ⚡ 性能优化 (perf)
- [ ] ✅ 测试相关 (test)
- [ ] 🔧 构建配置 (build)
- [ ] 🔄 CI/CD (ci)
- [ ] 🧹 其他维护 (chore)

### 📝 变更内容
<!-- 详细描述本次变更的内容和目的 -->

### 🔗 关联Issue
<!-- 关联的Issue编号，如：Closes #123, Fixes #456 -->

### 🧪 测试说明
<!-- 描述如何测试这些变更 -->
- [ ] 单元测试已通过
- [ ] 集成测试已通过
- [ ] 手动测试已完成
- [ ] 浏览器兼容性测试

### 📸 截图/录屏
<!-- 如果是UI变更，请提供截图或录屏 -->

### ⚠️ 注意事项
<!-- 需要特别注意的事项，如破坏性变更、依赖更新等 -->

### ✅ 检查清单
- [ ] 代码符合团队编码规范
- [ ] 已添加必要的测试
- [ ] 文档已更新
- [ ] 无控制台错误或警告
- [ ] 性能影响已评估
- [ ] 安全性已考虑
```

### 功能开发PR模板

```markdown
## 🚀 新功能：[功能名称]

### 📋 功能描述
<!-- 详细描述新功能的目的和价值 -->

### 🎯 用户故事
作为 [用户角色]，我希望 [功能描述]，以便 [预期收益]

### 💡 实现方案
<!-- 描述技术实现方案和关键决策 -->

### 🔧 技术栈
- Vue 3 Composition API
- TypeScript
- Pinia (状态管理)
- Vue Router (路由)
- 其他相关技术

### 🧪 测试覆盖
- [ ] 组件单元测试
- [ ] 状态管理测试
- [ ] 路由测试
- [ ] E2E测试
- [ ] 边界情况测试

### 📱 响应式设计
- [ ] 桌面端 (1920x1080)
- [ ] 平板端 (768x1024)
- [ ] 移动端 (375x667)

### 🔍 代码审查要点
- [ ] 组件设计是否合理
- [ ] 状态管理是否恰当
- [ ] 性能是否优化
- [ ] 错误处理是否完善
- [ ] 类型定义是否准确

### 📚 相关文档
- [设计稿链接]
- [API文档链接]
- [技术方案文档]
```

### Bug修复PR模板

```markdown
## 🐛 Bug修复：[Bug描述]

### 🔍 问题描述
<!-- 详细描述Bug的现象和影响 -->

### 🎯 复现步骤
1. 步骤一
2. 步骤二
3. 步骤三

### 💥 预期行为 vs 实际行为
**预期行为：**
<!-- 描述期望的正确行为 -->

**实际行为：**
<!-- 描述当前的错误行为 -->

### 🔧 根本原因
<!-- 分析Bug的根本原因 -->

### 💡 解决方案
<!-- 描述修复方案和实现细节 -->

### 🧪 修复验证
- [ ] 原问题已解决
- [ ] 回归测试通过
- [ ] 相关功能未受影响
- [ ] 边界情况已测试

### 📊 影响范围
- [ ] 仅影响特定功能
- [ ] 影响多个功能模块
- [ ] 影响核心流程
- [ ] 影响性能表现

### 🔒 安全考虑
- [ ] 无安全风险
- [ ] 已评估安全影响
- [ ] 需要安全审查
```

## 代码审查检查清单

### 通用检查项

#### 🏗️ 代码结构
- [ ] **文件组织**：文件放置在正确的目录中
- [ ] **命名规范**：文件、变量、函数命名符合规范
- [ ] **模块化**：代码合理拆分，职责单一
- [ ] **依赖管理**：导入/导出语句清晰合理
- [ ] **代码复用**：避免重复代码，合理抽象

#### 📝 代码质量
- [ ] **可读性**：代码逻辑清晰，易于理解
- [ ] **注释**：复杂逻辑有适当注释
- [ ] **格式化**：代码格式符合Prettier规范
- [ ] **ESLint**：通过所有ESLint检查
- [ ] **TypeScript**：类型定义准确完整

#### 🔒 安全性
- [ ] **输入验证**：用户输入得到适当验证
- [ ] **XSS防护**：避免跨站脚本攻击
- [ ] **敏感信息**：无硬编码的敏感信息
- [ ] **权限控制**：适当的权限检查
- [ ] **依赖安全**：无已知安全漏洞的依赖

#### ⚡ 性能
- [ ] **渲染优化**：避免不必要的重渲染
- [ ] **内存管理**：正确清理事件监听器和定时器
- [ ] **懒加载**：大型组件和资源使用懒加载
- [ ] **缓存策略**：合理使用缓存机制
- [ ] **包大小**：避免引入不必要的依赖

### Vue特定检查项

#### 🎨 组件设计
- [ ] **单一职责**：组件功能职责单一明确
- [ ] **Props设计**：Props类型定义完整，有默认值
- [ ] **事件设计**：事件命名规范，参数合理
- [ ] **插槽使用**：合理使用具名插槽和作用域插槽
- [ ] **组合式API**：正确使用Composition API

#### 📊 状态管理
- [ ] **状态设计**：状态结构合理，避免冗余
- [ ] **Pinia使用**：正确使用Store模式
- [ ] **响应式**：正确使用ref、reactive、computed
- [ ] **副作用**：watchEffect和watch使用恰当
- [ ] **状态持久化**：需要时正确实现状态持久化

#### 🛣️ 路由设计
- [ ] **路由结构**：路由层级合理，命名规范
- [ ] **导航守卫**：权限控制逻辑正确
- [ ] **参数传递**：路由参数和查询参数使用恰当
- [ ] **懒加载**：路由组件正确实现懒加载
- [ ] **元信息**：路由元信息配置完整

#### 🎭 模板和样式
- [ ] **模板语法**：正确使用Vue模板语法
- [ ] **条件渲染**：v-if和v-show使用恰当
- [ ] **列表渲染**：v-for正确使用key
- [ ] **样式隔离**：使用scoped样式或CSS Modules
- [ ] **响应式设计**：支持多种屏幕尺寸

### 测试检查项

#### 🧪 测试覆盖
- [ ] **单元测试**：核心逻辑有单元测试覆盖
- [ ] **组件测试**：组件行为有测试验证
- [ ] **集成测试**：关键流程有集成测试
- [ ] **边界测试**：边界情况和异常情况有测试
- [ ] **回归测试**：Bug修复有对应的回归测试

#### 📋 测试质量
- [ ] **测试命名**：测试用例命名清晰描述测试内容
- [ ] **测试独立**：测试用例之间相互独立
- [ ] **测试数据**：使用合适的测试数据和Mock
- [ ] **断言明确**：断言逻辑清晰准确
- [ ] **测试维护**：测试代码易于维护和更新

## 审查流程和标准

### 审查流程

#### 1. 提交前自检
```bash
# 开发者自检清单
npm run lint          # ESLint检查
npm run type-check     # TypeScript类型检查
npm run test          # 运行测试
npm run build         # 构建检查
```

#### 2. 创建Pull Request
- 使用合适的PR模板
- 填写完整的变更描述
- 添加相关标签和里程碑
- 指定合适的审查者

#### 3. 自动化检查
- CI/CD流水线自动运行
- 代码质量检查
- 测试覆盖率检查
- 安全扫描

#### 4. 人工审查
- 至少一名团队成员审查
- 核心功能需要高级开发者审查
- 安全相关变更需要安全专家审查

#### 5. 反馈和修改
- 审查者提供具体的反馈意见
- 开发者根据反馈进行修改
- 重新提交审查

#### 6. 批准和合并
- 所有检查通过
- 获得必要的审批
- 合并到目标分支

### 审查标准

#### 审查者职责
1. **及时响应**：在24小时内开始审查
2. **全面检查**：按照检查清单进行系统性审查
3. **建设性反馈**：提供具体、可操作的改进建议
4. **知识分享**：分享最佳实践和经验
5. **保持客观**：基于代码质量而非个人偏好

#### 反馈分类

##### 🚨 必须修复 (Must Fix)
- 功能性错误
- 安全漏洞
- 性能问题
- 违反编码规范

##### ⚠️ 建议修改 (Should Fix)
- 代码可读性问题
- 潜在的维护性问题
- 不够优雅的实现
- 缺少测试覆盖

##### 💡 可选优化 (Could Fix)
- 代码风格偏好
- 性能微优化
- 更好的实现方式
- 学习和改进建议

#### 反馈示例

##### ✅ 好的反馈
```markdown
🚨 **必须修复**：第45行的用户输入没有进行XSS过滤，存在安全风险。
建议使用 `DOMPurify.sanitize()` 或 Vue 的 `v-text` 指令。

⚠️ **建议修改**：`getUserData` 函数缺少错误处理，建议添加 try-catch 
或使用 Promise.catch() 处理异常情况。

💡 **可选优化**：可以考虑使用 `computed` 替代 `watch` 来计算 `fullName`，
这样更符合响应式编程的理念。
```

##### ❌ 不好的反馈
```markdown
这个代码不好。
这里有问题。
建议重写。
```

### 特殊情况处理

#### 紧急修复流程
```markdown
## 🚨 紧急修复流程

### 适用场景
- 生产环境严重Bug
- 安全漏洞修复
- 服务中断恢复

### 简化流程
1. 创建hotfix分支
2. 快速修复问题
3. 简化审查（至少1人审查）
4. 直接合并到main分支
5. 事后补充完整测试和文档

### 审查要点
- [ ] 修复是否解决了问题
- [ ] 是否引入新的问题
- [ ] 是否影响其他功能
- [ ] 是否需要回滚计划
```

#### 大型重构审查
```markdown
## 🔄 大型重构审查策略

### 分阶段审查
1. **架构设计审查**：审查整体设计方案
2. **核心模块审查**：优先审查核心功能模块
3. **接口变更审查**：重点关注API和接口变更
4. **测试策略审查**：确保测试覆盖充分
5. **迁移计划审查**：评估迁移风险和计划

### 审查重点
- [ ] 架构设计是否合理
- [ ] 向后兼容性考虑
- [ ] 性能影响评估
- [ ] 测试策略完整性
- [ ] 文档更新计划
```

## 自动化工具集成

### GitHub Actions 配置

```yaml
# .github/workflows/code-review.yml
name: Code Review Automation

on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  automated-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run ESLint
        run: npm run lint -- --format=json --output-file=eslint-report.json
        continue-on-error: true
      
      - name: Run TypeScript check
        run: npm run type-check
      
      - name: Run tests with coverage
        run: npm run test:coverage
      
      - name: Upload coverage to Codecov
        uses: codecov/codecov-action@v3
      
      - name: Comment PR with results
        uses: actions/github-script@v6
        with:
          script: |
            const fs = require('fs');
            const eslintReport = JSON.parse(fs.readFileSync('eslint-report.json', 'utf8'));
            
            let comment = '## 🤖 自动化审查结果\n\n';
            
            if (eslintReport.length > 0) {
              comment += '### ⚠️ ESLint 问题\n';
              eslintReport.forEach(file => {
                if (file.messages.length > 0) {
                  comment += `**${file.filePath}**\n`;
                  file.messages.forEach(msg => {
                    comment += `- Line ${msg.line}: ${msg.message}\n`;
                  });
                }
              });
            } else {
              comment += '### ✅ ESLint 检查通过\n';
            }
            
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: comment
            });
```

### 代码质量检查

```javascript
// scripts/review-checklist.js
const fs = require('fs');
const path = require('path');

class CodeReviewChecker {
  constructor(changedFiles) {
    this.changedFiles = changedFiles;
    this.issues = [];
  }

  checkVueComponents() {
    const vueFiles = this.changedFiles.filter(file => file.endsWith('.vue'));
    
    vueFiles.forEach(file => {
      const content = fs.readFileSync(file, 'utf8');
      
      // 检查是否使用了 script setup
      if (!content.includes('<script setup')) {
        this.issues.push({
          file,
          type: 'suggestion',
          message: '建议使用 <script setup> 语法'
        });
      }
      
      // 检查是否有 TypeScript
      if (!content.includes('lang="ts"')) {
        this.issues.push({
          file,
          type: 'warning',
          message: '建议使用 TypeScript'
        });
      }
      
      // 检查是否有 scoped 样式
      if (content.includes('<style>') && !content.includes('<style scoped>')) {
        this.issues.push({
          file,
          type: 'warning',
          message: '建议使用 scoped 样式'
        });
      }
    });
  }

  checkTestCoverage() {
    const sourceFiles = this.changedFiles.filter(file => 
      file.endsWith('.vue') || file.endsWith('.ts') || file.endsWith('.js')
    );
    
    sourceFiles.forEach(file => {
      const testFile = this.getTestFilePath(file);
      if (!fs.existsSync(testFile)) {
        this.issues.push({
          file,
          type: 'suggestion',
          message: `缺少测试文件: ${testFile}`
        });
      }
    });
  }

  getTestFilePath(sourceFile) {
    const ext = path.extname(sourceFile);
    const base = sourceFile.replace(ext, '');
    return `${base}.test${ext}`;
  }

  generateReport() {
    let report = '## 🔍 代码审查检查报告\n\n';
    
    if (this.issues.length === 0) {
      report += '✅ 所有检查项都通过了！\n';
      return report;
    }
    
    const groupedIssues = this.groupIssuesByType();
    
    Object.entries(groupedIssues).forEach(([type, issues]) => {
      const icon = type === 'error' ? '🚨' : type === 'warning' ? '⚠️' : '💡';
      report += `### ${icon} ${type.toUpperCase()}\n\n`;
      
      issues.forEach(issue => {
        report += `**${issue.file}**\n`;
        report += `- ${issue.message}\n\n`;
      });
    });
    
    return report;
  }

  groupIssuesByType() {
    return this.issues.reduce((groups, issue) => {
      const type = issue.type;
      if (!groups[type]) {
        groups[type] = [];
      }
      groups[type].push(issue);
      return groups;
    }, {});
  }
}

module.exports = CodeReviewChecker;
```

### 审查指标统计

```javascript
// scripts/review-metrics.js
const { Octokit } = require('@octokit/rest');

class ReviewMetrics {
  constructor(token) {
    this.octokit = new Octokit({ auth: token });
  }

  async getReviewMetrics(owner, repo, days = 30) {
    const since = new Date(Date.now() - days * 24 * 60 * 60 * 1000);
    
    const { data: pulls } = await this.octokit.pulls.list({
      owner,
      repo,
      state: 'closed',
      since: since.toISOString()
    });

    const metrics = {
      totalPRs: pulls.length,
      averageReviewTime: 0,
      reviewParticipation: {},
      approvalRate: 0
    };

    let totalReviewTime = 0;
    let approvedPRs = 0;

    for (const pr of pulls) {
      // 计算审查时间
      const reviewTime = this.calculateReviewTime(pr);
      totalReviewTime += reviewTime;

      // 统计审查参与度
      const { data: reviews } = await this.octokit.pulls.listReviews({
        owner,
        repo,
        pull_number: pr.number
      });

      reviews.forEach(review => {
        const reviewer = review.user.login;
        if (!metrics.reviewParticipation[reviewer]) {
          metrics.reviewParticipation[reviewer] = 0;
        }
        metrics.reviewParticipation[reviewer]++;
      });

      // 统计批准率
      if (reviews.some(review => review.state === 'APPROVED')) {
        approvedPRs++;
      }
    }

    metrics.averageReviewTime = totalReviewTime / pulls.length;
    metrics.approvalRate = (approvedPRs / pulls.length) * 100;

    return metrics;
  }

  calculateReviewTime(pr) {
    const created = new Date(pr.created_at);
    const merged = new Date(pr.merged_at || pr.closed_at);
    return (merged - created) / (1000 * 60 * 60); // 小时
  }

  generateMetricsReport(metrics) {
    return `
## 📊 代码审查指标报告

### 总体统计
- **PR总数**: ${metrics.totalPRs}
- **平均审查时间**: ${metrics.averageReviewTime.toFixed(1)} 小时
- **批准率**: ${metrics.approvalRate.toFixed(1)}%

### 审查参与度
${Object.entries(metrics.reviewParticipation)
  .sort(([,a], [,b]) => b - a)
  .map(([reviewer, count]) => `- **${reviewer}**: ${count} 次审查`)
  .join('\n')}
    `;
  }
}

module.exports = ReviewMetrics;
```

## 最佳实践总结

### 审查者最佳实践

#### Do's ✅
1. **及时响应**：在合理时间内开始审查
2. **全面检查**：按照检查清单系统性审查
3. **具体反馈**：提供明确、可操作的建议
4. **保持尊重**：以建设性的方式提供反馈
5. **知识分享**：解释为什么某种做法更好
6. **关注重点**：优先关注功能性和安全性问题

#### Don'ts ❌
1. **避免模糊反馈**：如"这里不好"、"需要改进"
2. **不要过度挑剔**：避免在风格偏好上过分纠结
3. **避免延迟审查**：不要让PR长时间等待
4. **不要忽视测试**：确保变更有适当的测试覆盖
5. **避免情绪化**：保持专业和客观的态度

### 开发者最佳实践

#### Do's ✅
1. **自我审查**：提交前进行彻底的自我检查
2. **清晰描述**：在PR中详细描述变更内容和原因
3. **小步提交**：保持PR大小合理，便于审查
4. **积极响应**：及时回应审查反馈
5. **学习改进**：从审查反馈中学习和成长

#### Don'ts ❌
1. **避免巨大PR**：不要提交包含过多变更的PR
2. **不要忽视反馈**：认真对待每一条审查意见
3. **避免防御心态**：以开放的心态接受建设性批评
4. **不要跳过测试**：确保所有变更都有适当的测试
5. **避免匆忙合并**：给审查者充分的时间

通过遵循这些代码审查规范，团队可以建立高效的代码质量保障机制，促进知识共享，提升整体开发水平。