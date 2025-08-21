# Vue集成测试规范

## 概述

本文档定义了Vue项目中集成测试的标准和最佳实践，包括端到端(E2E)测试、API测试和测试数据管理策略。

## E2E测试最佳实践

### 推荐技术栈

```json
{
  "@playwright/test": "^1.40.0",
  "playwright": "^1.40.0",
  "msw": "^2.0.0",
  "axios-mock-adapter": "^1.22.0"
}
```

### Playwright配置

```typescript
// playwright.config.ts
import { defineConfig, devices } from '@playwright/test'

export default defineConfig({
  testDir: './e2e',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: [
    ['html'],
    ['json', { outputFile: 'test-results/results.json' }],
    ['junit', { outputFile: 'test-results/results.xml' }]
  ],
  use: {
    baseURL: 'http://localhost:5173',
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
    video: 'retain-on-failure'
  },
  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] }
    },
    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] }
    },
    {
      name: 'webkit',
      use: { ...devices['Desktop Safari'] }
    },
    {
      name: 'Mobile Chrome',
      use: { ...devices['Pixel 5'] }
    }
  ],
  webServer: {
    command: 'npm run dev',
    url: 'http://localhost:5173',
    reuseExistingServer: !process.env.CI
  }
})
```

### E2E测试结构

```typescript
// e2e/user-management.spec.ts
import { test, expect, Page } from '@playwright/test'
import { LoginPage } from './pages/LoginPage'
import { UserListPage } from './pages/UserListPage'
import { UserFormPage } from './pages/UserFormPage'

test.describe('用户管理流程', () => {
  let loginPage: LoginPage
  let userListPage: UserListPage
  let userFormPage: UserFormPage

  test.beforeEach(async ({ page }) => {
    loginPage = new LoginPage(page)
    userListPage = new UserListPage(page)
    userFormPage = new UserFormPage(page)
    
    // 登录系统
    await loginPage.goto()
    await loginPage.login('admin@example.com', 'password123')
  })

  test('完整的用户CRUD流程', async ({ page }) => {
    // 1. 导航到用户列表
    await userListPage.goto()
    await expect(page).toHaveTitle(/用户管理/)

    // 2. 创建新用户
    await userListPage.clickCreateUser()
    await userFormPage.fillUserForm({
      name: '张三',
      email: 'zhangsan@example.com',
      role: '普通用户'
    })
    await userFormPage.submit()

    // 3. 验证用户创建成功
    await expect(userListPage.getUserRow('张三')).toBeVisible()
    await expect(page.getByText('用户创建成功')).toBeVisible()

    // 4. 编辑用户信息
    await userListPage.editUser('张三')
    await userFormPage.updateField('name', '张三丰')
    await userFormPage.submit()

    // 5. 验证用户更新成功
    await expect(userListPage.getUserRow('张三丰')).toBeVisible()
    await expect(page.getByText('用户更新成功')).toBeVisible()

    // 6. 删除用户
    await userListPage.deleteUser('张三丰')
    await userListPage.confirmDelete()

    // 7. 验证用户删除成功
    await expect(userListPage.getUserRow('张三丰')).not.toBeVisible()
    await expect(page.getByText('用户删除成功')).toBeVisible()
  })

  test('用户搜索和筛选功能', async ({ page }) => {
    await userListPage.goto()
    
    // 搜索用户
    await userListPage.searchUser('admin')
    await expect(userListPage.getUserRows()).toHaveCount(1)
    
    // 按角色筛选
    await userListPage.filterByRole('管理员')
    await expect(userListPage.getUserRows()).toHaveCount(2)
    
    // 清除筛选
    await userListPage.clearFilters()
    await expect(userListPage.getUserRows().count()).toBeGreaterThan(2)
  })

  test('分页功能测试', async ({ page }) => {
    await userListPage.goto()
    
    // 验证分页信息
    await expect(userListPage.getPaginationInfo()).toContainText('共 25 条记录')
    
    // 切换到下一页
    await userListPage.goToNextPage()
    await expect(page.url()).toContain('page=2')
    
    // 改变每页显示数量
    await userListPage.changePageSize(50)
    await expect(userListPage.getUserRows().count()).toBeLessThanOrEqual(25)
  })
})
```

### Page Object模式

```typescript
// e2e/pages/LoginPage.ts
import { Page, Locator } from '@playwright/test'

export class LoginPage {
  readonly page: Page
  readonly emailInput: Locator
  readonly passwordInput: Locator
  readonly loginButton: Locator
  readonly errorMessage: Locator

  constructor(page: Page) {
    this.page = page
    this.emailInput = page.getByTestId('email-input')
    this.passwordInput = page.getByTestId('password-input')
    this.loginButton = page.getByTestId('login-button')
    this.errorMessage = page.getByTestId('error-message')
  }

  async goto() {
    await this.page.goto('/login')
  }

  async login(email: string, password: string) {
    await this.emailInput.fill(email)
    await this.passwordInput.fill(password)
    await this.loginButton.click()
    await this.page.waitForURL('/dashboard')
  }

  async getErrorMessage() {
    return await this.errorMessage.textContent()
  }
}
```

```typescript
// e2e/pages/UserListPage.ts
import { Page, Locator } from '@playwright/test'

export class UserListPage {
  readonly page: Page
  readonly createUserButton: Locator
  readonly searchInput: Locator
  readonly roleFilter: Locator
  readonly userTable: Locator

  constructor(page: Page) {
    this.page = page
    this.createUserButton = page.getByTestId('create-user-button')
    this.searchInput = page.getByTestId('search-input')
    this.roleFilter = page.getByTestId('role-filter')
    this.userTable = page.getByTestId('user-table')
  }

  async goto() {
    await this.page.goto('/users')
  }

  async clickCreateUser() {
    await this.createUserButton.click()
  }

  async searchUser(query: string) {
    await this.searchInput.fill(query)
    await this.searchInput.press('Enter')
  }

  async filterByRole(role: string) {
    await this.roleFilter.selectOption(role)
  }

  async clearFilters() {
    await this.page.getByTestId('clear-filters').click()
  }

  getUserRow(userName: string) {
    return this.page.getByTestId(`user-row-${userName}`)
  }

  getUserRows() {
    return this.page.getByTestId('user-row')
  }

  async editUser(userName: string) {
    await this.getUserRow(userName).getByTestId('edit-button').click()
  }

  async deleteUser(userName: string) {
    await this.getUserRow(userName).getByTestId('delete-button').click()
  }

  async confirmDelete() {
    await this.page.getByTestId('confirm-delete').click()
  }

  async goToNextPage() {
    await this.page.getByTestId('next-page').click()
  }

  async changePageSize(size: number) {
    await this.page.getByTestId('page-size-select').selectOption(size.toString())
  }

  getPaginationInfo() {
    return this.page.getByTestId('pagination-info')
  }
}
```

### 测试数据管理

```typescript
// e2e/fixtures/test-data.ts
export interface TestUser {
  id?: number
  name: string
  email: string
  role: string
  password?: string
}

export const testUsers: TestUser[] = [
  {
    name: '管理员',
    email: 'admin@example.com',
    role: '管理员',
    password: 'admin123'
  },
  {
    name: '普通用户1',
    email: 'user1@example.com',
    role: '普通用户',
    password: 'user123'
  },
  {
    name: '普通用户2',
    email: 'user2@example.com',
    role: '普通用户',
    password: 'user123'
  }
]

export const createTestUser = (overrides: Partial<TestUser> = {}): TestUser => ({
  name: '测试用户',
  email: 'test@example.com',
  role: '普通用户',
  ...overrides
})
```

### 数据库状态管理

```typescript
// e2e/helpers/database.ts
import { test as base } from '@playwright/test'
import { PrismaClient } from '@prisma/client'

export const test = base.extend<{ db: PrismaClient }>({
  db: async ({}, use) => {
    const db = new PrismaClient()
    await use(db)
    await db.$disconnect()
  }
})

// 使用示例
test('用户创建测试', async ({ page, db }) => {
  // 清理测试数据
  await db.user.deleteMany({
    where: { email: { contains: 'test' } }
  })

  // 执行测试...

  // 验证数据库状态
  const user = await db.user.findUnique({
    where: { email: 'test@example.com' }
  })
  expect(user).toBeTruthy()
})
```

## API测试规范

### API测试框架配置

```typescript
// tests/api/setup.ts
import { beforeAll, afterAll, beforeEach } from 'vitest'
import { setupServer } from 'msw/node'
import { handlers } from './mocks/handlers'

const server = setupServer(...handlers)

beforeAll(() => {
  server.listen({ onUnhandledRequest: 'error' })
})

afterAll(() => {
  server.close()
})

beforeEach(() => {
  server.resetHandlers()
})

export { server }
```

### MSW Mock配置

```typescript
// tests/api/mocks/handlers.ts
import { http, HttpResponse } from 'msw'
import type { User, CreateUserRequest } from '@/types/user'

const users: User[] = [
  {
    id: 1,
    name: 'John Doe',
    email: 'john@example.com',
    role: '管理员',
    createdAt: new Date('2024-01-01')
  }
]

export const handlers = [
  // 获取用户列表
  http.get('/api/users', ({ request }) => {
    const url = new URL(request.url)
    const page = parseInt(url.searchParams.get('page') || '1')
    const limit = parseInt(url.searchParams.get('limit') || '10')
    const search = url.searchParams.get('search')

    let filteredUsers = users
    if (search) {
      filteredUsers = users.filter(user =>
        user.name.includes(search) || user.email.includes(search)
      )
    }

    const start = (page - 1) * limit
    const end = start + limit
    const paginatedUsers = filteredUsers.slice(start, end)

    return HttpResponse.json({
      code: 200,
      data: {
        users: paginatedUsers,
        total: filteredUsers.length,
        page,
        limit
      }
    })
  }),

  // 创建用户
  http.post('/api/users', async ({ request }) => {
    const userData = await request.json() as CreateUserRequest
    
    // 验证邮箱唯一性
    if (users.some(user => user.email === userData.email)) {
      return HttpResponse.json(
        { code: 400, message: '邮箱已存在' },
        { status: 400 }
      )
    }

    const newUser: User = {
      id: users.length + 1,
      ...userData,
      createdAt: new Date()
    }
    users.push(newUser)

    return HttpResponse.json({
      code: 201,
      data: newUser,
      message: '用户创建成功'
    })
  }),

  // 更新用户
  http.put('/api/users/:id', async ({ params, request }) => {
    const userId = parseInt(params.id as string)
    const userData = await request.json() as Partial<User>
    
    const userIndex = users.findIndex(user => user.id === userId)
    if (userIndex === -1) {
      return HttpResponse.json(
        { code: 404, message: '用户不存在' },
        { status: 404 }
      )
    }

    users[userIndex] = { ...users[userIndex], ...userData }
    
    return HttpResponse.json({
      code: 200,
      data: users[userIndex],
      message: '用户更新成功'
    })
  }),

  // 删除用户
  http.delete('/api/users/:id', ({ params }) => {
    const userId = parseInt(params.id as string)
    const userIndex = users.findIndex(user => user.id === userId)
    
    if (userIndex === -1) {
      return HttpResponse.json(
        { code: 404, message: '用户不存在' },
        { status: 404 }
      )
    }

    users.splice(userIndex, 1)
    
    return HttpResponse.json({
      code: 200,
      message: '用户删除成功'
    })
  })
]
```

### API集成测试

```typescript
// tests/api/user.api.test.ts
import { describe, it, expect, beforeEach } from 'vitest'
import { server } from './setup'
import { UserService } from '@/services/UserService'
import type { CreateUserRequest } from '@/types/user'

describe('用户API集成测试', () => {
  let userService: UserService

  beforeEach(() => {
    userService = new UserService()
  })

  describe('获取用户列表', () => {
    it('应该成功获取用户列表', async () => {
      const response = await userService.getUsers({ page: 1, limit: 10 })
      
      expect(response.code).toBe(200)
      expect(response.data.users).toBeInstanceOf(Array)
      expect(response.data.total).toBeGreaterThan(0)
    })

    it('应该支持搜索功能', async () => {
      const response = await userService.getUsers({ 
        page: 1, 
        limit: 10, 
        search: 'John' 
      })
      
      expect(response.code).toBe(200)
      expect(response.data.users.every(user => 
        user.name.includes('John') || user.email.includes('John')
      )).toBe(true)
    })

    it('应该支持分页功能', async () => {
      const page1 = await userService.getUsers({ page: 1, limit: 5 })
      const page2 = await userService.getUsers({ page: 2, limit: 5 })
      
      expect(page1.data.users).toHaveLength(5)
      expect(page2.data.users).toHaveLength(5)
      expect(page1.data.users[0].id).not.toBe(page2.data.users[0].id)
    })
  })

  describe('创建用户', () => {
    it('应该成功创建用户', async () => {
      const userData: CreateUserRequest = {
        name: '新用户',
        email: 'newuser@example.com',
        role: '普通用户'
      }

      const response = await userService.createUser(userData)
      
      expect(response.code).toBe(201)
      expect(response.data.name).toBe(userData.name)
      expect(response.data.email).toBe(userData.email)
      expect(response.data.id).toBeDefined()
    })

    it('应该拒绝重复邮箱', async () => {
      const userData: CreateUserRequest = {
        name: '重复用户',
        email: 'john@example.com', // 已存在的邮箱
        role: '普通用户'
      }

      await expect(userService.createUser(userData)).rejects.toThrow('邮箱已存在')
    })
  })

  describe('更新用户', () => {
    it('应该成功更新用户信息', async () => {
      const updateData = { name: '更新后的名称' }
      const response = await userService.updateUser(1, updateData)
      
      expect(response.code).toBe(200)
      expect(response.data.name).toBe(updateData.name)
    })

    it('应该处理不存在的用户', async () => {
      await expect(userService.updateUser(999, { name: '测试' }))
        .rejects.toThrow('用户不存在')
    })
  })

  describe('删除用户', () => {
    it('应该成功删除用户', async () => {
      const response = await userService.deleteUser(1)
      expect(response.code).toBe(200)
    })

    it('应该处理不存在的用户', async () => {
      await expect(userService.deleteUser(999))
        .rejects.toThrow('用户不存在')
    })
  })
})
```

### 错误处理测试

```typescript
// tests/api/error-handling.test.ts
import { describe, it, expect } from 'vitest'
import { http, HttpResponse } from 'msw'
import { server } from './setup'
import { UserService } from '@/services/UserService'

describe('API错误处理', () => {
  let userService: UserService

  beforeEach(() => {
    userService = new UserService()
  })

  it('应该处理网络错误', async () => {
    server.use(
      http.get('/api/users', () => {
        return HttpResponse.error()
      })
    )

    await expect(userService.getUsers()).rejects.toThrow('网络错误')
  })

  it('应该处理服务器错误', async () => {
    server.use(
      http.get('/api/users', () => {
        return HttpResponse.json(
          { code: 500, message: '服务器内部错误' },
          { status: 500 }
        )
      })
    )

    await expect(userService.getUsers()).rejects.toThrow('服务器内部错误')
  })

  it('应该处理超时错误', async () => {
    server.use(
      http.get('/api/users', async () => {
        await new Promise(resolve => setTimeout(resolve, 6000)) // 超过5秒超时
        return HttpResponse.json({ code: 200, data: [] })
      })
    )

    await expect(userService.getUsers()).rejects.toThrow('请求超时')
  })
})
```

## 测试数据管理指南

### 测试数据隔离

```typescript
// tests/helpers/test-isolation.ts
import { beforeEach, afterEach } from 'vitest'

export const useTestIsolation = () => {
  let originalLocalStorage: Storage
  let originalSessionStorage: Storage

  beforeEach(() => {
    // 保存原始状态
    originalLocalStorage = { ...localStorage }
    originalSessionStorage = { ...sessionStorage }
    
    // 清理存储
    localStorage.clear()
    sessionStorage.clear()
    
    // 重置全局状态
    document.body.innerHTML = ''
  })

  afterEach(() => {
    // 恢复原始状态
    localStorage.clear()
    sessionStorage.clear()
    Object.assign(localStorage, originalLocalStorage)
    Object.assign(sessionStorage, originalSessionStorage)
  })
}
```

### 测试数据工厂

```typescript
// tests/factories/index.ts
import { faker } from '@faker-js/faker'
import type { User, Product, Order } from '@/types'

export class TestDataFactory {
  static createUser(overrides: Partial<User> = {}): User {
    return {
      id: faker.number.int({ min: 1, max: 1000 }),
      name: faker.person.fullName(),
      email: faker.internet.email(),
      avatar: faker.image.avatar(),
      role: faker.helpers.arrayElement(['管理员', '普通用户', '访客']),
      createdAt: faker.date.past(),
      ...overrides
    }
  }

  static createUsers(count: number): User[] {
    return Array.from({ length: count }, () => this.createUser())
  }

  static createProduct(overrides: Partial<Product> = {}): Product {
    return {
      id: faker.number.int({ min: 1, max: 1000 }),
      name: faker.commerce.productName(),
      price: parseFloat(faker.commerce.price()),
      description: faker.commerce.productDescription(),
      category: faker.commerce.department(),
      inStock: faker.datatype.boolean(),
      ...overrides
    }
  }

  static createOrder(overrides: Partial<Order> = {}): Order {
    return {
      id: faker.string.uuid(),
      userId: faker.number.int({ min: 1, max: 100 }),
      products: this.createProducts(faker.number.int({ min: 1, max: 5 })),
      total: parseFloat(faker.commerce.price()),
      status: faker.helpers.arrayElement(['pending', 'processing', 'completed', 'cancelled']),
      createdAt: faker.date.past(),
      ...overrides
    }
  }
}
```

### 数据库种子数据

```typescript
// tests/seeds/database.ts
import { PrismaClient } from '@prisma/client'
import { TestDataFactory } from '../factories'

export class DatabaseSeeder {
  private db: PrismaClient

  constructor(db: PrismaClient) {
    this.db = db
  }

  async seedUsers(count: number = 10) {
    const users = TestDataFactory.createUsers(count)
    
    for (const user of users) {
      await this.db.user.create({
        data: user
      })
    }
    
    return users
  }

  async seedProducts(count: number = 20) {
    const products = Array.from({ length: count }, () => 
      TestDataFactory.createProduct()
    )
    
    for (const product of products) {
      await this.db.product.create({
        data: product
      })
    }
    
    return products
  }

  async cleanDatabase() {
    await this.db.order.deleteMany()
    await this.db.product.deleteMany()
    await this.db.user.deleteMany()
  }

  async seedAll() {
    await this.cleanDatabase()
    
    const users = await this.seedUsers()
    const products = await this.seedProducts()
    
    return { users, products }
  }
}
```

## 性能测试

### 负载测试

```typescript
// tests/performance/load.test.ts
import { test, expect } from '@playwright/test'

test.describe('性能测试', () => {
  test('页面加载性能', async ({ page }) => {
    const startTime = Date.now()
    
    await page.goto('/dashboard')
    await page.waitForLoadState('networkidle')
    
    const loadTime = Date.now() - startTime
    expect(loadTime).toBeLessThan(3000) // 页面加载时间应小于3秒
  })

  test('大数据量渲染性能', async ({ page }) => {
    await page.goto('/users')
    
    // 模拟大量数据
    await page.route('/api/users', route => {
      const largeDataset = Array.from({ length: 1000 }, (_, i) => ({
        id: i + 1,
        name: `User ${i + 1}`,
        email: `user${i + 1}@example.com`
      }))
      
      route.fulfill({
        json: { code: 200, data: { users: largeDataset, total: 1000 } }
      })
    })

    const startTime = Date.now()
    await page.reload()
    await page.waitForSelector('[data-testid="user-table"]')
    
    const renderTime = Date.now() - startTime
    expect(renderTime).toBeLessThan(2000) // 渲染时间应小于2秒
  })
})
```

### 内存泄漏测试

```typescript
// tests/performance/memory.test.ts
import { test, expect } from '@playwright/test'

test('内存泄漏检测', async ({ page }) => {
  await page.goto('/dashboard')
  
  // 获取初始内存使用情况
  const initialMemory = await page.evaluate(() => {
    return (performance as any).memory?.usedJSHeapSize || 0
  })

  // 执行多次操作
  for (let i = 0; i < 10; i++) {
    await page.goto('/users')
    await page.goto('/products')
    await page.goto('/dashboard')
  }

  // 强制垃圾回收
  await page.evaluate(() => {
    if ((window as any).gc) {
      (window as any).gc()
    }
  })

  // 检查内存使用情况
  const finalMemory = await page.evaluate(() => {
    return (performance as any).memory?.usedJSHeapSize || 0
  })

  const memoryIncrease = finalMemory - initialMemory
  const memoryIncreasePercent = (memoryIncrease / initialMemory) * 100

  // 内存增长不应超过50%
  expect(memoryIncreasePercent).toBeLessThan(50)
})
```

## 测试报告和监控

### 测试报告配置

```typescript
// playwright.config.ts (报告部分)
export default defineConfig({
  reporter: [
    ['html', { 
      open: 'never',
      outputFolder: 'test-results/html-report'
    }],
    ['json', { 
      outputFile: 'test-results/results.json' 
    }],
    ['junit', { 
      outputFile: 'test-results/results.xml' 
    }],
    ['allure-playwright', {
      detail: true,
      outputFolder: 'test-results/allure-results',
      suiteTitle: false
    }]
  ]
})
```

### CI/CD集成

```yaml
# .github/workflows/e2e-tests.yml
name: E2E Tests

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  e2e-tests:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Install Playwright
        run: npx playwright install --with-deps
      
      - name: Build application
        run: npm run build
      
      - name: Run E2E tests
        run: npm run test:e2e
      
      - name: Upload test results
        uses: actions/upload-artifact@v4
        if: always()
        with:
          name: playwright-report
          path: test-results/
          retention-days: 30
```

### 测试监控和告警

```typescript
// tests/monitoring/test-monitor.ts
export class TestMonitor {
  static async reportTestResults(results: TestResults) {
    const metrics = {
      totalTests: results.total,
      passedTests: results.passed,
      failedTests: results.failed,
      duration: results.duration,
      timestamp: new Date().toISOString()
    }

    // 发送到监控系统
    await fetch('/api/test-metrics', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(metrics)
    })

    // 如果失败率超过阈值，发送告警
    const failureRate = (results.failed / results.total) * 100
    if (failureRate > 10) {
      await this.sendAlert(`测试失败率过高: ${failureRate.toFixed(2)}%`)
    }
  }

  private static async sendAlert(message: string) {
    // 发送钉钉/企业微信告警
    await fetch(process.env.WEBHOOK_URL!, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        msgtype: 'text',
        text: { content: `🚨 测试告警: ${message}` }
      })
    })
  }
}
```