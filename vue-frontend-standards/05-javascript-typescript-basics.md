# JavaScript/TypeScript 基础语法规范

## 概述

本文档定义了Vue项目中JavaScript和TypeScript的基础语法规范，包括变量命名、函数定义、对象和数组操作等核心编程规范。

## 变量命名和声明规范

### 命名约定

#### 变量和函数命名
- 使用 camelCase 命名法
- 名称应具有描述性和语义化
- 避免使用缩写，除非是广泛认知的缩写

```typescript
// ✅ 推荐
const userName = 'john_doe'
const isUserLoggedIn = true
const getUserProfile = () => { /* ... */ }

// ❌ 不推荐
const un = 'john_doe'
const flag = true
const getUsrProf = () => { /* ... */ }
```

#### 常量命名
- 使用 SCREAMING_SNAKE_CASE 命名法
- 在文件顶部或专门的常量文件中定义

```typescript
// ✅ 推荐
const API_BASE_URL = 'https://api.example.com'
const MAX_RETRY_COUNT = 3
const DEFAULT_PAGE_SIZE = 20

// ❌ 不推荐
const apiBaseUrl = 'https://api.example.com'
const maxRetryCount = 3
```

#### 类和接口命名
- 使用 PascalCase 命名法
- 接口名称以 `I` 开头或使用描述性名称

```typescript
// ✅ 推荐
class UserService { }
interface UserProfile { }
interface IApiResponse { }

// ❌ 不推荐
class userService { }
interface userProfile { }
```

### 变量声明

#### 优先级顺序
1. `const` - 用于不会重新赋值的变量
2. `let` - 用于需要重新赋值的变量
3. 避免使用 `var`

```typescript
// ✅ 推荐
const config = { timeout: 5000 }
let currentUser = null

// ❌ 不推荐
var config = { timeout: 5000 }
var currentUser = null
```

#### 声明位置
- 在使用前就近声明
- 避免在作用域顶部声明所有变量

```typescript
// ✅ 推荐
function processUser(userId: string) {
  const user = getUserById(userId)
  
  if (user) {
    const profile = user.profile
    return formatProfile(profile)
  }
  
  return null
}

// ❌ 不推荐
function processUser(userId: string) {
  let user, profile, result
  
  user = getUserById(userId)
  if (user) {
    profile = user.profile
    result = formatProfile(profile)
    return result
  }
  
  return null
}
```

## 函数定义和使用标准

### 函数声明方式

#### 函数声明 vs 函数表达式
- 优先使用箭头函数表达式
- 对于需要提升的函数使用函数声明

```typescript
// ✅ 推荐 - 箭头函数
const calculateTotal = (items: Item[]) => {
  return items.reduce((sum, item) => sum + item.price, 0)
}

// ✅ 推荐 - 函数声明（需要提升时）
function initializeApp() {
  // 初始化逻辑
}

// ❌ 不推荐 - 传统函数表达式
const calculateTotal = function(items: Item[]) {
  return items.reduce((sum, item) => sum + item.price, 0)
}
```

### 函数参数

#### 参数命名和类型
- 参数名称应具有描述性
- 必须提供TypeScript类型注解
- 使用解构赋值简化参数传递

```typescript
// ✅ 推荐
const createUser = (userData: {
  name: string
  email: string
  age?: number
}) => {
  // 实现逻辑
}

// 或使用接口
interface CreateUserParams {
  name: string
  email: string
  age?: number
}

const createUser = (userData: CreateUserParams) => {
  // 实现逻辑
}

// ✅ 推荐 - 解构参数
const updateUserProfile = ({
  userId,
  name,
  email
}: {
  userId: string
  name: string
  email: string
}) => {
  // 实现逻辑
}
```

#### 默认参数
- 使用ES6默认参数语法
- 将有默认值的参数放在参数列表末尾

```typescript
// ✅ 推荐
const fetchUsers = (
  page: number = 1,
  pageSize: number = 20,
  sortBy: string = 'createdAt'
) => {
  // 实现逻辑
}

// ❌ 不推荐
const fetchUsers = (page?: number, pageSize?: number, sortBy?: string) => {
  const actualPage = page || 1
  const actualPageSize = pageSize || 20
  const actualSortBy = sortBy || 'createdAt'
  // 实现逻辑
}
```

### 函数返回值

#### 明确返回类型
- 复杂函数必须声明返回类型
- 简单函数可以依赖类型推断

```typescript
// ✅ 推荐 - 明确返回类型
const getUserById = (id: string): Promise<User | null> => {
  return api.get(`/users/${id}`)
}

// ✅ 推荐 - 简单函数可省略
const add = (a: number, b: number) => a + b

// ✅ 推荐 - 联合类型返回
const parseUserInput = (input: string): number | string => {
  const num = Number(input)
  return isNaN(num) ? input : num
}
```

## 对象和数组操作规范

### 对象操作

#### 对象创建和属性访问
- 优先使用字面量语法创建对象
- 使用点号访问已知属性，方括号访问动态属性

```typescript
// ✅ 推荐 - 对象字面量
const user = {
  id: '123',
  name: 'John Doe',
  email: 'john@example.com'
}

// ✅ 推荐 - 属性访问
const userName = user.name
const dynamicProp = user[propertyName]

// ❌ 不推荐
const user = new Object()
user.id = '123'
user.name = 'John Doe'
```

#### 对象解构
- 使用解构赋值提取对象属性
- 为解构的属性提供默认值

```typescript
// ✅ 推荐 - 基础解构
const { name, email, age } = user

// ✅ 推荐 - 重命名和默认值
const { 
  name: userName, 
  age = 0,
  isActive = false 
} = user

// ✅ 推荐 - 嵌套解构
const { 
  profile: { 
    avatar, 
    bio = '暂无简介' 
  } 
} = user
```

#### 对象合并和复制
- 使用扩展运算符进行对象操作
- 避免直接修改原对象

```typescript
// ✅ 推荐 - 对象合并
const updatedUser = {
  ...user,
  name: 'New Name',
  updatedAt: new Date()
}

// ✅ 推荐 - 深度合并（使用工具函数）
const mergedConfig = {
  ...defaultConfig,
  ...userConfig,
  api: {
    ...defaultConfig.api,
    ...userConfig.api
  }
}

// ❌ 不推荐 - 直接修改
user.name = 'New Name'
Object.assign(user, { updatedAt: new Date() })
```

### 数组操作

#### 数组创建和基础操作
- 使用字面量语法创建数组
- 优先使用数组方法而非循环

```typescript
// ✅ 推荐 - 数组字面量
const numbers = [1, 2, 3, 4, 5]
const users: User[] = []

// ✅ 推荐 - 数组方法
const activeUsers = users.filter(user => user.isActive)
const userNames = users.map(user => user.name)
const totalAge = users.reduce((sum, user) => sum + user.age, 0)

// ❌ 不推荐 - 传统循环
const activeUsers = []
for (let i = 0; i < users.length; i++) {
  if (users[i].isActive) {
    activeUsers.push(users[i])
  }
}
```

#### 数组解构
- 使用解构赋值提取数组元素
- 使用剩余运算符处理不定长数组

```typescript
// ✅ 推荐 - 基础解构
const [first, second, third] = numbers

// ✅ 推荐 - 跳过元素
const [first, , third] = numbers

// ✅ 推荐 - 剩余元素
const [head, ...tail] = numbers
const [first, second, ...rest] = numbers
```

#### 数组不可变操作
- 使用扩展运算符进行数组操作
- 避免直接修改原数组

```typescript
// ✅ 推荐 - 添加元素
const newUsers = [...users, newUser]
const prependedUsers = [newUser, ...users]

// ✅ 推荐 - 删除元素
const filteredUsers = users.filter(user => user.id !== targetId)

// ✅ 推荐 - 更新元素
const updatedUsers = users.map(user => 
  user.id === targetId 
    ? { ...user, ...updates }
    : user
)

// ❌ 不推荐 - 直接修改
users.push(newUser)
users.splice(index, 1)
users[index] = updatedUser
```

### 条件判断和逻辑操作

#### 布尔值判断
- 使用严格相等比较
- 利用JavaScript的真值/假值特性

```typescript
// ✅ 推荐 - 严格比较
if (user.age === 0) { }
if (user.name !== '') { }

// ✅ 推荐 - 真值判断
if (user.name) { }
if (users.length) { }

// ✅ 推荐 - 空值合并
const userName = user.name ?? 'Anonymous'
const userAge = user.age ?? 0

// ❌ 不推荐 - 宽松比较
if (user.age == 0) { }
if (user.name != '') { }
```

#### 条件链和逻辑运算
- 使用可选链操作符处理嵌套属性
- 合理使用逻辑运算符

```typescript
// ✅ 推荐 - 可选链
const avatarUrl = user?.profile?.avatar?.url
const hasPermission = user?.permissions?.includes('admin')

// ✅ 推荐 - 逻辑运算符
const displayName = user.nickname || user.name || 'Anonymous'
const shouldShowButton = isLoggedIn && hasPermission

// ❌ 不推荐 - 嵌套判断
let avatarUrl
if (user && user.profile && user.profile.avatar) {
  avatarUrl = user.profile.avatar.url
}
```

## 代码组织和模块化

### 导入导出规范
- 使用ES6模块语法
- 明确的导入导出声明

```typescript
// ✅ 推荐 - 命名导出
export const formatDate = (date: Date) => { }
export const parseDate = (dateString: string) => { }

// ✅ 推荐 - 默认导出
export default class UserService { }

// ✅ 推荐 - 导入
import { formatDate, parseDate } from './dateUtils'
import UserService from './UserService'

// ✅ 推荐 - 类型导入
import type { User, UserProfile } from './types'
```

### 文件组织
- 每个文件专注单一职责
- 相关功能组织在同一模块中

```typescript
// userUtils.ts - 用户相关工具函数
export const formatUserName = (user: User) => { }
export const validateEmail = (email: string) => { }
export const generateAvatar = (name: string) => { }

// constants.ts - 常量定义
export const API_ENDPOINTS = {
  USERS: '/api/users',
  PROFILES: '/api/profiles'
} as const

export const USER_ROLES = {
  ADMIN: 'admin',
  USER: 'user',
  GUEST: 'guest'
} as const
```

## 最佳实践总结

1. **命名规范**：使用有意义的变量名，遵循约定的命名规则
2. **类型安全**：充分利用TypeScript的类型系统
3. **不可变性**：优先使用不可变操作，避免直接修改数据
4. **函数式编程**：使用数组方法和纯函数
5. **现代语法**：使用ES6+的新特性提高代码质量
6. **代码组织**：保持模块化和单一职责原则

遵循这些规范将帮助团队编写更加一致、可维护和高质量的JavaScript/TypeScript代码。