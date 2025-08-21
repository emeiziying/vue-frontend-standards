# Vue单元测试规范

## 概述

本文档定义了Vue项目中单元测试的编写标准和最佳实践，确保测试代码的质量、可维护性和有效性。

## 测试框架配置

### 推荐技术栈

```json
{
  "vitest": "^1.0.0",
  "@vue/test-utils": "^2.4.0",
  "jsdom": "^23.0.0",
  "@testing-library/vue": "^8.0.0",
  "@testing-library/jest-dom": "^6.0.0"
}
```

### Vitest配置

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'
import vue from '@vitejs/plugin-vue'
import { resolve } from 'path'

export default defineConfig({
  plugins: [vue()],
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: ['./src/test/setup.ts'],
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      exclude: [
        'node_modules/',
        'src/test/',
        '**/*.d.ts',
        '**/*.config.*'
      ],
      thresholds: {
        global: {
          branches: 80,
          functions: 80,
          lines: 80,
          statements: 80
        }
      }
    }
  },
  resolve: {
    alias: {
      '@': resolve(__dirname, './src')
    }
  }
})
```

### 测试环境设置

```typescript
// src/test/setup.ts
import { expect, afterEach } from 'vitest'
import { cleanup } from '@testing-library/vue'
import matchers from '@testing-library/jest-dom/matchers'

// 扩展expect匹配器
expect.extend(matchers)

// 每个测试后清理DOM
afterEach(() => {
  cleanup()
})
```

## Vue组件测试标准

### 组件测试结构

```typescript
// UserProfile.test.ts
import { describe, it, expect, beforeEach, vi } from 'vitest'
import { mount, VueWrapper } from '@vue/test-utils'
import { render, screen } from '@testing-library/vue'
import UserProfile from '@/components/UserProfile.vue'
import type { User } from '@/types/user'

describe('UserProfile', () => {
  let wrapper: VueWrapper
  const mockUser: User = {
    id: 1,
    name: 'John Doe',
    email: 'john@example.com'
  }

  beforeEach(() => {
    wrapper = mount(UserProfile, {
      props: {
        user: mockUser
      }
    })
  })

  describe('渲染测试', () => {
    it('应该正确渲染用户信息', () => {
      expect(wrapper.find('[data-testid="user-name"]').text()).toBe(mockUser.name)
      expect(wrapper.find('[data-testid="user-email"]').text()).toBe(mockUser.email)
    })
  })

  describe('交互测试', () => {
    it('点击编辑按钮应该触发edit事件', async () => {
      await wrapper.find('[data-testid="edit-button"]').trigger('click')
      expect(wrapper.emitted('edit')).toBeTruthy()
      expect(wrapper.emitted('edit')?.[0]).toEqual([mockUser.id])
    })
  })

  describe('条件渲染测试', () => {
    it('当用户未登录时应该显示登录提示', () => {
      const wrapper = mount(UserProfile, {
        props: {
          user: null
        }
      })
      expect(wrapper.find('[data-testid="login-prompt"]').exists()).toBe(true)
    })
  })
})
```

### 组件测试最佳实践

#### 1. 使用data-testid属性

```vue
<!-- 推荐 -->
<template>
  <div class="user-profile">
    <h2 data-testid="user-name">{{ user.name }}</h2>
    <p data-testid="user-email">{{ user.email }}</p>
    <button data-testid="edit-button" @click="handleEdit">编辑</button>
  </div>
</template>
```

#### 2. 测试Props验证

```typescript
describe('Props测试', () => {
  it('应该接收必需的props', () => {
    const wrapper = mount(UserProfile, {
      props: {
        user: mockUser,
        editable: true
      }
    })
    expect(wrapper.props('user')).toEqual(mockUser)
    expect(wrapper.props('editable')).toBe(true)
  })

  it('应该有正确的默认props', () => {
    const wrapper = mount(UserProfile, {
      props: {
        user: mockUser
      }
    })
    expect(wrapper.props('editable')).toBe(false)
  })
})
```

#### 3. 测试事件发射

```typescript
describe('事件测试', () => {
  it('应该发射正确的事件和参数', async () => {
    await wrapper.find('[data-testid="delete-button"]').trigger('click')
    
    const emittedEvents = wrapper.emitted('delete')
    expect(emittedEvents).toBeTruthy()
    expect(emittedEvents?.[0]).toEqual([mockUser.id])
  })
})
```

#### 4. 测试插槽

```typescript
describe('插槽测试', () => {
  it('应该正确渲染默认插槽内容', () => {
    const wrapper = mount(UserProfile, {
      props: { user: mockUser },
      slots: {
        default: '<p data-testid="slot-content">自定义内容</p>'
      }
    })
    expect(wrapper.find('[data-testid="slot-content"]').text()).toBe('自定义内容')
  })
})
```

### Composition API测试

```typescript
// useCounter.test.ts
import { describe, it, expect } from 'vitest'
import { useCounter } from '@/composables/useCounter'

describe('useCounter', () => {
  it('应该初始化计数器', () => {
    const { count, increment, decrement, reset } = useCounter(0)
    expect(count.value).toBe(0)
  })

  it('应该正确增加计数', () => {
    const { count, increment } = useCounter(0)
    increment()
    expect(count.value).toBe(1)
  })

  it('应该正确减少计数', () => {
    const { count, decrement } = useCounter(5)
    decrement()
    expect(count.value).toBe(4)
  })

  it('应该重置计数器', () => {
    const { count, increment, reset } = useCounter(0)
    increment()
    increment()
    reset()
    expect(count.value).toBe(0)
  })
})
```

## 工具函数测试指南

### 纯函数测试

```typescript
// utils/format.test.ts
import { describe, it, expect } from 'vitest'
import { formatCurrency, formatDate, validateEmail } from '@/utils/format'

describe('formatCurrency', () => {
  it('应该正确格式化货币', () => {
    expect(formatCurrency(1234.56)).toBe('¥1,234.56')
    expect(formatCurrency(0)).toBe('¥0.00')
    expect(formatCurrency(-100)).toBe('-¥100.00')
  })

  it('应该处理边界情况', () => {
    expect(formatCurrency(null)).toBe('¥0.00')
    expect(formatCurrency(undefined)).toBe('¥0.00')
    expect(formatCurrency(NaN)).toBe('¥0.00')
  })
})

describe('validateEmail', () => {
  it('应该验证有效邮箱', () => {
    expect(validateEmail('test@example.com')).toBe(true)
    expect(validateEmail('user.name+tag@domain.co.uk')).toBe(true)
  })

  it('应该拒绝无效邮箱', () => {
    expect(validateEmail('invalid-email')).toBe(false)
    expect(validateEmail('@example.com')).toBe(false)
    expect(validateEmail('test@')).toBe(false)
    expect(validateEmail('')).toBe(false)
  })
})
```

### 异步函数测试

```typescript
// api/user.test.ts
import { describe, it, expect, vi, beforeEach } from 'vitest'
import { fetchUser, createUser } from '@/api/user'
import type { User, CreateUserRequest } from '@/types/user'

// Mock fetch
global.fetch = vi.fn()

describe('用户API', () => {
  beforeEach(() => {
    vi.clearAllMocks()
  })

  describe('fetchUser', () => {
    it('应该成功获取用户信息', async () => {
      const mockUser: User = {
        id: 1,
        name: 'John Doe',
        email: 'john@example.com'
      }

      ;(fetch as any).mockResolvedValueOnce({
        ok: true,
        json: async () => ({ data: mockUser })
      })

      const result = await fetchUser(1)
      expect(result).toEqual(mockUser)
      expect(fetch).toHaveBeenCalledWith('/api/users/1')
    })

    it('应该处理API错误', async () => {
      ;(fetch as any).mockResolvedValueOnce({
        ok: false,
        status: 404,
        statusText: 'Not Found'
      })

      await expect(fetchUser(999)).rejects.toThrow('用户不存在')
    })
  })
})
```

### Mock和Spy使用

```typescript
// services/notification.test.ts
import { describe, it, expect, vi } from 'vitest'
import { NotificationService } from '@/services/notification'

describe('NotificationService', () => {
  it('应该调用正确的通知方法', () => {
    const mockShow = vi.fn()
    const service = new NotificationService()
    service.show = mockShow

    service.success('操作成功')
    
    expect(mockShow).toHaveBeenCalledWith({
      type: 'success',
      message: '操作成功',
      duration: 3000
    })
  })
})
```

## 测试覆盖率要求

### 覆盖率标准

| 代码类型 | 最低覆盖率 | 推荐覆盖率 |
|---------|-----------|-----------|
| 核心业务逻辑 | 90% | 95% |
| 工具函数 | 95% | 100% |
| Vue组件 | 80% | 90% |
| API服务 | 85% | 95% |
| 状态管理 | 90% | 95% |

### 覆盖率检查配置

```json
{
  "scripts": {
    "test": "vitest",
    "test:coverage": "vitest --coverage",
    "test:ui": "vitest --ui"
  }
}
```

### 覆盖率报告

```bash
# 生成覆盖率报告
npm run test:coverage

# 查看HTML报告
open coverage/index.html
```

## 测试命名规范

### 测试文件命名

```
src/
├── components/
│   ├── UserProfile.vue
│   └── UserProfile.test.ts        # 组件测试
├── utils/
│   ├── format.ts
│   └── format.test.ts             # 工具函数测试
├── composables/
│   ├── useCounter.ts
│   └── useCounter.test.ts         # Composable测试
└── stores/
    ├── user.ts
    └── user.test.ts               # Store测试
```

### 测试用例命名

```typescript
describe('组件/功能名称', () => {
  describe('功能分组', () => {
    it('应该 + 期望行为', () => {
      // 测试实现
    })
  })
})
```

## 测试数据管理

### 测试数据工厂

```typescript
// test/factories/user.ts
import type { User } from '@/types/user'

export const createMockUser = (overrides: Partial<User> = {}): User => ({
  id: 1,
  name: 'Test User',
  email: 'test@example.com',
  avatar: 'https://example.com/avatar.jpg',
  createdAt: new Date('2024-01-01'),
  ...overrides
})

export const createMockUsers = (count: number): User[] => {
  return Array.from({ length: count }, (_, index) =>
    createMockUser({
      id: index + 1,
      name: `User ${index + 1}`,
      email: `user${index + 1}@example.com`
    })
  )
}
```

### Fixture数据

```typescript
// test/fixtures/api-responses.ts
export const userApiResponses = {
  success: {
    code: 200,
    message: 'success',
    data: {
      id: 1,
      name: 'John Doe',
      email: 'john@example.com'
    }
  },
  error: {
    code: 404,
    message: 'User not found',
    data: null
  }
}
```

## 常见测试模式

### 1. 测试驱动开发(TDD)

```typescript
// 1. 先写测试
describe('calculateTotal', () => {
  it('应该计算商品总价', () => {
    const items = [
      { price: 10, quantity: 2 },
      { price: 5, quantity: 3 }
    ]
    expect(calculateTotal(items)).toBe(35)
  })
})

// 2. 实现功能
export const calculateTotal = (items: CartItem[]): number => {
  return items.reduce((total, item) => total + item.price * item.quantity, 0)
}
```

### 2. 快照测试

```typescript
describe('UserCard快照测试', () => {
  it('应该匹配快照', () => {
    const wrapper = mount(UserCard, {
      props: {
        user: mockUser
      }
    })
    expect(wrapper.html()).toMatchSnapshot()
  })
})
```

### 3. 参数化测试

```typescript
describe.each([
  ['valid@email.com', true],
  ['invalid-email', false],
  ['@domain.com', false],
  ['user@', false]
])('validateEmail(%s)', (email, expected) => {
  it(`应该返回 ${expected}`, () => {
    expect(validateEmail(email)).toBe(expected)
  })
})
```

## 性能测试

### 组件渲染性能

```typescript
describe('性能测试', () => {
  it('大量数据渲染性能', () => {
    const largeDataset = createMockUsers(1000)
    const startTime = performance.now()
    
    const wrapper = mount(UserList, {
      props: {
        users: largeDataset
      }
    })
    
    const endTime = performance.now()
    const renderTime = endTime - startTime
    
    expect(renderTime).toBeLessThan(100) // 渲染时间应小于100ms
    expect(wrapper.findAll('[data-testid="user-item"]')).toHaveLength(1000)
  })
})
```

## 调试测试

### 调试配置

```json
{
  "type": "node",
  "request": "launch",
  "name": "Debug Tests",
  "program": "${workspaceFolder}/node_modules/vitest/vitest.mjs",
  "args": ["--run", "${relativeFile}"],
  "console": "integratedTerminal",
  "internalConsoleOptions": "neverOpen"
}
```

### 调试技巧

```typescript
describe('调试示例', () => {
  it('调试组件状态', () => {
    const wrapper = mount(Counter)
    
    // 输出组件HTML结构
    console.log(wrapper.html())
    
    // 输出组件数据
    console.log(wrapper.vm.$data)
    
    // 设置断点
    debugger
    
    expect(wrapper.find('[data-testid="count"]').text()).toBe('0')
  })
})
```