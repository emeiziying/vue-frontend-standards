# Git 分支管理规范

## 概述

本文档定义了Vue前端项目的Git分支管理策略，包括分支命名规范、合并策略和发布流程。通过统一的分支管理，确保代码质量、发布稳定性和团队协作效率。

## 分支模型

### Git Flow 分支模型

我们采用基于Git Flow的分支管理模型，包含以下主要分支类型：

```
main (生产分支)
├── develop (开发分支)
│   ├── feature/* (功能分支)
│   ├── bugfix/* (Bug修复分支)
│   └── hotfix/* (热修复分支)
└── release/* (发布分支)
```

### 分支类型说明

| 分支类型 | 用途 | 生命周期 | 合并目标 |
|----------|------|----------|----------|
| `main` | 生产环境代码 | 永久 | - |
| `develop` | 开发环境代码 | 永久 | main |
| `feature/*` | 新功能开发 | 临时 | develop |
| `bugfix/*` | Bug修复 | 临时 | develop |
| `hotfix/*` | 紧急修复 | 临时 | main + develop |
| `release/*` | 发布准备 | 临时 | main + develop |

## 分支命名规范

### 命名格式

```
<type>/<scope>-<description>
```

### 命名规则

#### Feature 分支
```bash
feature/auth-login-page          # 认证登录页面
feature/user-profile-edit        # 用户资料编辑
feature/product-search-filter    # 产品搜索过滤
feature/order-payment-flow       # 订单支付流程
```

#### Bugfix 分支
```bash
bugfix/auth-token-refresh        # 修复认证令牌刷新
bugfix/ui-mobile-responsive      # 修复移动端响应式
bugfix/api-data-validation       # 修复API数据验证
bugfix/performance-list-render   # 修复列表渲染性能
```

#### Hotfix 分支
```bash
hotfix/security-xss-vulnerability    # 修复XSS安全漏洞
hotfix/payment-gateway-timeout       # 修复支付网关超时
hotfix/critical-data-loss           # 修复关键数据丢失
```

#### Release 分支
```bash
release/v1.2.0                   # 版本1.2.0发布
release/v2.0.0-beta             # 版本2.0.0测试版
release/sprint-2024-01          # 2024年第1个冲刺发布
```

### 命名约定

#### Type（类型）
- `feature` - 新功能开发
- `bugfix` - Bug修复
- `hotfix` - 紧急修复
- `release` - 发布准备
- `experiment` - 实验性功能
- `refactor` - 代码重构

#### Scope（范围）
- `auth` - 认证相关
- `user` - 用户管理
- `product` - 产品功能
- `order` - 订单处理
- `ui` - 用户界面
- `api` - 接口相关
- `config` - 配置相关
- `test` - 测试相关

#### Description（描述）
- 使用小写字母和连字符
- 简洁明了，描述主要功能
- 避免使用缩写和特殊字符
- 长度控制在30个字符以内

### 命名示例

#### ✅ 好的命名
```bash
feature/auth-social-login
feature/user-avatar-upload
bugfix/ui-table-pagination
hotfix/security-csrf-protection
release/v1.5.0
```

#### ❌ 不好的命名
```bash
feature/new-stuff              # 太模糊
bugfix/fix                     # 缺少具体描述
feature/AUTH_LOGIN_PAGE        # 使用大写和下划线
hotfix/fix-bug-123            # 只有issue号码
feature/implement-user-management-system  # 太长
```

## 分支合并策略

### 合并方式选择

#### Merge Commit（推荐）
```bash
git checkout develop
git merge --no-ff feature/auth-login-page
```

**优点：**
- 保留完整的分支历史
- 清晰的功能边界
- 便于回滚整个功能

**使用场景：**
- Feature分支合并到develop
- Release分支合并到main
- 重要功能的合并

#### Squash and Merge
```bash
git checkout develop
git merge --squash feature/auth-login-page
git commit -m "feat(auth): implement login page functionality"
```

**优点：**
- 简化提交历史
- 单一提交表示完整功能
- 便于代码审查

**使用场景：**
- 小功能或Bug修复
- 提交历史较乱的分支
- 实验性功能合并

#### Rebase and Merge
```bash
git checkout feature/auth-login-page
git rebase develop
git checkout develop
git merge --ff-only feature/auth-login-page
```

**优点：**
- 线性的提交历史
- 没有合并提交
- 清晰的时间线

**使用场景：**
- 个人开发分支
- 简单的Bug修复
- 保持线性历史的需求

### 合并策略配置

#### 分支保护规则

```yaml
# .github/branch-protection.yml
main:
  required_status_checks:
    strict: true
    contexts:
      - "ci/tests"
      - "ci/lint"
      - "ci/build"
  enforce_admins: true
  required_pull_request_reviews:
    required_approving_review_count: 2
    dismiss_stale_reviews: true
    require_code_owner_reviews: true
  restrictions:
    users: []
    teams: ["frontend-team"]

develop:
  required_status_checks:
    strict: true
    contexts:
      - "ci/tests"
      - "ci/lint"
  required_pull_request_reviews:
    required_approving_review_count: 1
    dismiss_stale_reviews: true
```

#### 自动合并配置

```javascript
// .github/workflows/auto-merge.yml
name: Auto Merge
on:
  pull_request:
    types: [labeled]

jobs:
  auto-merge:
    if: contains(github.event.label.name, 'auto-merge')
    runs-on: ubuntu-latest
    steps:
      - name: Auto merge
        uses: pascalgn/merge-action@v0.15.6
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          merge_method: squash
```

## 发布分支管理流程

### 版本号规范

采用语义化版本控制（Semantic Versioning）：

```
MAJOR.MINOR.PATCH[-PRERELEASE][+BUILD]
```

#### 版本号说明
- **MAJOR**：不兼容的API变更
- **MINOR**：向后兼容的功能新增
- **PATCH**：向后兼容的Bug修复
- **PRERELEASE**：预发布版本标识（alpha, beta, rc）
- **BUILD**：构建元数据

#### 版本示例
```
1.0.0          # 正式版本
1.1.0          # 新增功能
1.1.1          # Bug修复
2.0.0          # 重大更新
1.2.0-beta.1   # 测试版本
1.2.0-rc.1     # 候选版本
```

### 发布流程

#### 1. 创建Release分支

```bash
# 从develop分支创建release分支
git checkout develop
git pull origin develop
git checkout -b release/v1.2.0
git push -u origin release/v1.2.0
```

#### 2. 发布准备工作

```bash
# 更新版本号
npm version 1.2.0 --no-git-tag-version

# 更新CHANGELOG
echo "## [1.2.0] - $(date +%Y-%m-%d)" >> CHANGELOG.md

# 运行完整测试
npm run test:full
npm run build
npm run e2e

# 提交版本更新
git add .
git commit -m "chore(release): prepare v1.2.0"
git push origin release/v1.2.0
```

#### 3. 合并到主分支

```bash
# 创建PR到main分支
gh pr create --title "Release v1.2.0" --body "Release version 1.2.0"

# 合并后创建标签
git checkout main
git pull origin main
git tag -a v1.2.0 -m "Release version 1.2.0"
git push origin v1.2.0
```

#### 4. 回合并到develop

```bash
# 将release分支合并回develop
git checkout develop
git merge --no-ff release/v1.2.0
git push origin develop

# 删除release分支
git branch -d release/v1.2.0
git push origin --delete release/v1.2.0
```

### 热修复流程

#### 1. 创建Hotfix分支

```bash
# 从main分支创建hotfix分支
git checkout main
git pull origin main
git checkout -b hotfix/security-fix-v1.2.1
```

#### 2. 修复问题

```bash
# 进行必要的修复
# ... 修复代码 ...

# 更新版本号
npm version patch --no-git-tag-version

# 提交修复
git add .
git commit -m "fix(security): resolve XSS vulnerability"
git push -u origin hotfix/security-fix-v1.2.1
```

#### 3. 合并和发布

```bash
# 合并到main
git checkout main
git merge --no-ff hotfix/security-fix-v1.2.1
git tag -a v1.2.1 -m "Hotfix version 1.2.1"
git push origin main --tags

# 合并到develop
git checkout develop
git merge --no-ff hotfix/security-fix-v1.2.1
git push origin develop

# 删除hotfix分支
git branch -d hotfix/security-fix-v1.2.1
git push origin --delete hotfix/security-fix-v1.2.1
```

## 分支管理最佳实践

### 分支生命周期管理

#### Feature分支
```bash
# 1. 创建分支
git checkout develop
git pull origin develop
git checkout -b feature/user-profile-edit

# 2. 开发过程
git add .
git commit -m "feat(user): add profile edit form"
git push -u origin feature/user-profile-edit

# 3. 定期同步
git checkout develop
git pull origin develop
git checkout feature/user-profile-edit
git rebase develop

# 4. 完成开发
git push origin feature/user-profile-edit
# 创建Pull Request

# 5. 合并后清理
git checkout develop
git pull origin develop
git branch -d feature/user-profile-edit
```

#### 分支清理策略

```bash
# 查看已合并的分支
git branch --merged develop

# 批量删除已合并的feature分支
git branch --merged develop | grep "feature/" | xargs -n 1 git branch -d

# 清理远程跟踪分支
git remote prune origin

# 查看远程分支状态
git for-each-ref --format='%(refname:short) %(upstream:track)' refs/heads
```

### 冲突解决策略

#### 预防冲突
1. **频繁同步**：定期从主分支拉取最新代码
2. **小步提交**：保持提交粒度小，减少冲突范围
3. **沟通协调**：团队成员间及时沟通修改计划
4. **代码分离**：避免多人同时修改同一文件

#### 解决冲突
```bash
# 1. 更新本地分支
git checkout develop
git pull origin develop

# 2. 切换到功能分支并rebase
git checkout feature/my-feature
git rebase develop

# 3. 解决冲突
# 编辑冲突文件，解决冲突标记
git add <resolved-files>
git rebase --continue

# 4. 强制推送（谨慎使用）
git push --force-with-lease origin feature/my-feature
```

### 分支权限管理

#### 权限配置
```yaml
# 分支权限矩阵
branches:
  main:
    - 只允许通过PR合并
    - 需要2个审批
    - 需要通过所有检查
    - 管理员可以强制合并
  
  develop:
    - 只允许通过PR合并
    - 需要1个审批
    - 需要通过基本检查
    - 开发者可以直接推送hotfix
  
  feature/*:
    - 创建者可以直接推送
    - 其他人需要PR
    - 自动删除已合并分支
```

#### 团队角色定义
- **管理员**：可以操作所有分支，设置保护规则
- **维护者**：可以合并PR，管理release分支
- **开发者**：可以创建feature分支，提交PR
- **访客**：只读权限，可以查看代码和提交历史

## 工具和自动化

### Git Flow 工具

```bash
# 安装git-flow
brew install git-flow-avh  # macOS
apt-get install git-flow   # Ubuntu

# 初始化git-flow
git flow init

# 使用git-flow命令
git flow feature start user-profile-edit
git flow feature finish user-profile-edit
git flow release start 1.2.0
git flow release finish 1.2.0
```

### 自动化脚本

#### 分支清理脚本
```bash
#!/bin/bash
# cleanup-branches.sh

echo "Cleaning up merged branches..."

# 切换到develop分支
git checkout develop
git pull origin develop

# 删除已合并的feature分支
merged_branches=$(git branch --merged develop | grep "feature/" | grep -v "develop")
if [ -n "$merged_branches" ]; then
    echo "Deleting merged feature branches:"
    echo "$merged_branches"
    echo "$merged_branches" | xargs -n 1 git branch -d
else
    echo "No merged feature branches to delete"
fi

# 清理远程跟踪分支
git remote prune origin

echo "Branch cleanup completed"
```

#### 发布脚本
```bash
#!/bin/bash
# release.sh

VERSION=$1
if [ -z "$VERSION" ]; then
    echo "Usage: ./release.sh <version>"
    exit 1
fi

echo "Starting release process for version $VERSION"

# 创建release分支
git checkout develop
git pull origin develop
git checkout -b "release/v$VERSION"

# 更新版本号
npm version "$VERSION" --no-git-tag-version

# 运行测试
npm run test:full
if [ $? -ne 0 ]; then
    echo "Tests failed, aborting release"
    exit 1
fi

# 构建项目
npm run build
if [ $? -ne 0 ]; then
    echo "Build failed, aborting release"
    exit 1
fi

# 提交版本更新
git add .
git commit -m "chore(release): prepare v$VERSION"
git push -u origin "release/v$VERSION"

echo "Release branch created: release/v$VERSION"
echo "Please create a PR to merge into main branch"
```

## 监控和度量

### 分支健康度指标

#### 关键指标
1. **分支数量**：活跃分支的数量
2. **分支年龄**：分支存在的时间
3. **合并频率**：分支合并到主分支的频率
4. **冲突率**：合并时发生冲突的比例
5. **代码审查时间**：从PR创建到合并的时间

#### 监控脚本
```bash
#!/bin/bash
# branch-metrics.sh

echo "=== Branch Health Metrics ==="

# 统计分支数量
echo "Active branches:"
git branch -r | grep -v "HEAD\|main\|develop" | wc -l

# 查找长期存在的分支
echo "Branches older than 30 days:"
git for-each-ref --format='%(refname:short) %(committerdate)' refs/remotes/origin | \
    awk '$2 < "'$(date -d '30 days ago' '+%Y-%m-%d')'"' | \
    grep -v "main\|develop"

# 统计最近的合并活动
echo "Recent merges (last 7 days):"
git log --oneline --merges --since="7 days ago" | wc -l
```

### 自动化报告

```javascript
// branch-report.js
const { execSync } = require('child_process');

function getBranchMetrics() {
    const metrics = {
        totalBranches: 0,
        featureBranches: 0,
        staleBranches: 0,
        recentMerges: 0
    };

    // 获取所有远程分支
    const branches = execSync('git branch -r').toString().split('\n');
    metrics.totalBranches = branches.filter(b => 
        b.trim() && !b.includes('HEAD') && !b.includes('main') && !b.includes('develop')
    ).length;

    // 统计feature分支
    metrics.featureBranches = branches.filter(b => b.includes('feature/')).length;

    // 统计最近合并
    const recentMerges = execSync('git log --oneline --merges --since="7 days ago"').toString();
    metrics.recentMerges = recentMerges.split('\n').filter(line => line.trim()).length;

    return metrics;
}

// 生成报告
const metrics = getBranchMetrics();
console.log('Branch Health Report:');
console.log(`Total active branches: ${metrics.totalBranches}`);
console.log(`Feature branches: ${metrics.featureBranches}`);
console.log(`Recent merges (7 days): ${metrics.recentMerges}`);
```

通过遵循这些分支管理规范，团队可以维护清晰的代码历史，提高协作效率，确保发布质量和稳定性。