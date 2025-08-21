# TypeScript 类型定义规范

## 概述

本文档定义了Vue项目中TypeScript类型系统的使用规范，包括接口定义、类型别名、泛型使用、类型断言和类型守卫等高级特性的最佳实践。

## 接口和类型别名使用指南

### 接口定义规范

#### 基础接口定义
- 使用 PascalCase 命名接口
- 接口名称应具有描述性
- 优先使用接口而非类型别名定义对象结构

```typescript
// ✅ 推荐 - 基础接口
interface User {
  readonly id: string
  name: string
  email: string
  age?: number
  createdAt: Date
  updatedAt: Date
}

// ✅ 推荐 - 嵌套接口
interface UserProfile {
  user: User
  avatar?: string
  bio?: string
  preferences: {
    theme: 'light' | 'dark'
    language: string
    notifications: boolean
  }
}
```

#### 接口继承和扩展
- 使用 `extends` 关键字进行接口继承
- 合理使用接口组合

```typescript
// ✅ 推荐 - 基础接口
interface BaseEntity {
  readonly id: string
  createdAt: Date
  updatedAt: Date
}

// ✅ 推荐 - 接口继承
interface User extends BaseEntity {
  name: string
  email: string
  role: UserRole
}

interface AdminUser extends User {
  permissions: Permission[]
  lastLoginAt?: Date
}

// ✅ 推荐 - 多重继承
interface AuditableEntity extends BaseEntity {
  createdBy: string
  updatedBy: string
}

interface Product extends BaseEntity, AuditableEntity {
  name: string
  price: number
  category: string
}
```

#### 可选属性和只读属性
- 合理使用可选属性 `?`
- 使用 `readonly` 标记不可变属性

```typescript
// ✅ 推荐 - 可选和只读属性
interface CreateUserRequest {
  name: string
  email: string
  age?: number
  role?: UserRole
}

interface UserResponse {
  readonly id: string
  readonly createdAt: Date
  name: string
  email: string
  age?: number
}

// ✅ 推荐 - 索引签名
interface UserPreferences {
  [key: string]: string | number | boolean
  theme: 'light' | 'dark'
  language: string
}
```

### 类型别名使用规范

#### 基础类型别名
- 使用 PascalCase 命名类型别名
- 用于联合类型、函数类型和复杂类型组合

```typescript
// ✅ 推荐 - 联合类型
type UserRole = 'admin' | 'user' | 'guest'
type Theme = 'light' | 'dark' | 'auto'
type Status = 'pending' | 'approved' | 'rejected'

// ✅ 推荐 - 函数类型
type EventHandler<T = any> = (event: T) => void
type AsyncOperation<T> = () => Promise<T>
type Validator<T> = (value: T) => boolean | string

// ✅ 推荐 - 复杂类型组合
type ApiResponse<T> = {
  success: boolean
  data: T
  message?: string
  errors?: string[]
}

type PaginatedResponse<T> = ApiResponse<{
  items: T[]
  total: number
  page: number
  pageSize: number
}>
```

#### 工具类型使用
- 充分利用TypeScript内置工具类型
- 创建自定义工具类型

```typescript
// ✅ 推荐 - 内置工具类型
type PartialUser = Partial<User>
type RequiredUser = Required<User>
type UserEmail = Pick<User, 'email'>
type UserWithoutId = Omit<User, 'id'>

// ✅ 推荐 - 自定义工具类型
type Optional<T, K extends keyof T> = Omit<T, K> & Partial<Pick<T, K>>
type WithTimestamp<T> = T & {
  createdAt: Date
  updatedAt: Date
}

// 使用示例
type CreateUser = Optional<User, 'id' | 'createdAt' | 'updatedAt'>
type UserWithTimestamp = WithTimestamp<Omit<User, 'createdAt' | 'updatedAt'>>
```

### 接口 vs 类型别名选择指南

```typescript
// ✅ 推荐 - 使用接口的场景
// 1. 定义对象结构
interface UserConfig {
  apiUrl: string
  timeout: number
}

// 2. 需要继承扩展
interface ExtendedConfig extends UserConfig {
  retryCount: number
}

// 3. 声明合并
interface Window {
  customProperty: string
}

// ✅ 推荐 - 使用类型别名的场景
// 1. 联合类型
type InputType = 'text' | 'email' | 'password'

// 2. 函数类型
type RequestHandler = (req: Request, res: Response) => void

// 3. 条件类型和映射类型
type NonNullable<T> = T extends null | undefined ? never : T
```

## 泛型使用最佳实践

### 基础泛型定义
- 使用有意义的泛型参数名
- 提供合理的默认类型和约束

```typescript
// ✅ 推荐 - 描述性泛型参数名
interface Repository<TEntity, TKey = string> {
  findById(id: TKey): Promise<TEntity | null>
  create(entity: Omit<TEntity, 'id'>): Promise<TEntity>
  update(id: TKey, updates: Partial<TEntity>): Promise<TEntity>
  delete(id: TKey): Promise<void>
}

// ✅ 推荐 - 泛型约束
interface Identifiable {
  id: string
}

interface EntityService<T extends Identifiable> {
  entities: T[]
  findById(id: string): T | undefined
  add(entity: T): void
}

// ❌ 不推荐 - 无意义的泛型参数名
interface Repository<T, U> {
  findById(id: U): Promise<T | null>
}
```

### 函数泛型
- 在函数级别使用泛型提高复用性
- 合理使用泛型约束

```typescript
// ✅ 推荐 - 函数泛型
const mapArray = <TInput, TOutput>(
  array: TInput[],
  mapper: (item: TInput) => TOutput
): TOutput[] => {
  return array.map(mapper)
}

// ✅ 推荐 - 带约束的泛型函数
const sortByProperty = <T extends Record<string, any>>(
  array: T[],
  property: keyof T
): T[] => {
  return array.sort((a, b) => {
    if (a[property] < b[property]) return -1
    if (a[property] > b[property]) return 1
    return 0
  })
}

// ✅ 推荐 - 条件类型泛型
type ApiMethod<T> = T extends 'GET' 
  ? (url: string) => Promise<any>
  : T extends 'POST'
  ? (url: string, data: any) => Promise<any>
  : never

const createApiMethod = <T extends 'GET' | 'POST'>(
  method: T
): ApiMethod<T> => {
  // 实现逻辑
  return null as any
}
```

### 类泛型
- 在类定义中使用泛型增强复用性

```typescript
// ✅ 推荐 - 泛型类
class DataStore<T extends { id: string }> {
  private items: Map<string, T> = new Map()

  add(item: T): void {
    this.items.set(item.id, item)
  }

  get(id: string): T | undefined {
    return this.items.get(id)
  }

  getAll(): T[] {
    return Array.from(this.items.values())
  }

  update(id: string, updates: Partial<T>): T | null {
    const item = this.items.get(id)
    if (!item) return null

    const updated = { ...item, ...updates }
    this.items.set(id, updated)
    return updated
  }
}

// 使用示例
const userStore = new DataStore<User>()
const productStore = new DataStore<Product>()
```

## 类型断言和类型守卫规范

### 类型断言使用
- 谨慎使用类型断言
- 优先使用类型守卫而非断言
- 使用 `as` 语法而非尖括号语法

```typescript
// ✅ 推荐 - 合理的类型断言场景
const userInput = document.getElementById('user-input') as HTMLInputElement
const apiResponse = response.data as ApiResponse<User>

// ✅ 推荐 - 使用 as const 断言
const themes = ['light', 'dark', 'auto'] as const
type Theme = typeof themes[number] // 'light' | 'dark' | 'auto'

const config = {
  apiUrl: 'https://api.example.com',
  timeout: 5000
} as const

// ❌ 不推荐 - 危险的类型断言
const user = data as User // 没有验证 data 的结构
const id = user.id as number // 强制类型转换
```

### 类型守卫定义
- 创建自定义类型守卫函数
- 使用 `is` 关键字明确类型谓词

```typescript
// ✅ 推荐 - 基础类型守卫
const isString = (value: unknown): value is string => {
  return typeof value === 'string'
}

const isNumber = (value: unknown): value is number => {
  return typeof value === 'number' && !isNaN(value)
}

// ✅ 推荐 - 对象类型守卫
const isUser = (obj: unknown): obj is User => {
  return (
    typeof obj === 'object' &&
    obj !== null &&
    'id' in obj &&
    'name' in obj &&
    'email' in obj &&
    typeof (obj as any).id === 'string' &&
    typeof (obj as any).name === 'string' &&
    typeof (obj as any).email === 'string'
  )
}

// ✅ 推荐 - 联合类型守卫
type Shape = Circle | Rectangle | Triangle

const isCircle = (shape: Shape): shape is Circle => {
  return shape.type === 'circle'
}

const isRectangle = (shape: Shape): shape is Rectangle => {
  return shape.type === 'rectangle'
}

// 使用示例
const processShape = (shape: Shape) => {
  if (isCircle(shape)) {
    // shape 被推断为 Circle 类型
    console.log(`Circle radius: ${shape.radius}`)
  } else if (isRectangle(shape)) {
    // shape 被推断为 Rectangle 类型
    console.log(`Rectangle: ${shape.width} x ${shape.height}`)
  }
}
```

### 高级类型守卫
- 使用泛型类型守卫
- 创建复合类型守卫

```typescript
// ✅ 推荐 - 泛型类型守卫
const hasProperty = <T extends object, K extends string>(
  obj: T,
  prop: K
): obj is T & Record<K, unknown> => {
  return prop in obj
}

// ✅ 推荐 - 数组类型守卫
const isArrayOf = <T>(
  arr: unknown,
  guard: (item: unknown) => item is T
): arr is T[] => {
  return Array.isArray(arr) && arr.every(guard)
}

// 使用示例
const isUserArray = (data: unknown): data is User[] => {
  return isArrayOf(data, isUser)
}

// ✅ 推荐 - 复合类型守卫
const isApiResponse = <T>(
  obj: unknown,
  dataGuard: (data: unknown) => data is T
): obj is ApiResponse<T> => {
  return (
    typeof obj === 'object' &&
    obj !== null &&
    'success' in obj &&
    'data' in obj &&
    typeof (obj as any).success === 'boolean' &&
    dataGuard((obj as any).data)
  )
}
```

## 高级类型模式

### 映射类型
- 使用映射类型创建衍生类型

```typescript
// ✅ 推荐 - 基础映射类型
type Readonly<T> = {
  readonly [P in keyof T]: T[P]
}

type Optional<T> = {
  [P in keyof T]?: T[P]
}

// ✅ 推荐 - 条件映射类型
type NonFunctionPropertyNames<T> = {
  [K in keyof T]: T[K] extends Function ? never : K
}[keyof T]

type NonFunctionProperties<T> = Pick<T, NonFunctionPropertyNames<T>>

// ✅ 推荐 - 模板字面量类型
type EventName<T extends string> = `on${Capitalize<T>}`
type UserEvents = EventName<'login' | 'logout' | 'register'>
// 结果: 'onLogin' | 'onLogout' | 'onRegister'
```

### 条件类型
- 使用条件类型实现类型逻辑

```typescript
// ✅ 推荐 - 基础条件类型
type NonNullable<T> = T extends null | undefined ? never : T

type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never

// ✅ 推荐 - 递归条件类型
type DeepReadonly<T> = {
  readonly [P in keyof T]: T[P] extends object ? DeepReadonly<T[P]> : T[P]
}

// ✅ 推荐 - 分布式条件类型
type ToArray<T> = T extends any ? T[] : never
type StringOrNumberArray = ToArray<string | number>
// 结果: string[] | number[]
```

## 类型声明文件规范

### 全局类型声明
- 在 `types` 目录中组织类型声明
- 使用模块声明扩展第三方库

```typescript
// types/global.d.ts
declare global {
  interface Window {
    __APP_CONFIG__: AppConfig
  }

  namespace NodeJS {
    interface ProcessEnv {
      NODE_ENV: 'development' | 'production' | 'test'
      API_BASE_URL: string
    }
  }
}

export {}

// types/vue-router.d.ts
import 'vue-router'

declare module 'vue-router' {
  interface RouteMeta {
    requiresAuth?: boolean
    roles?: string[]
    title?: string
  }
}
```

### 模块类型声明
- 为第三方库创建类型声明

```typescript
// types/external-lib.d.ts
declare module 'some-library' {
  export interface LibraryConfig {
    apiKey: string
    timeout?: number
  }

  export class LibraryClient {
    constructor(config: LibraryConfig)
    request<T>(endpoint: string): Promise<T>
  }

  export default LibraryClient
}
```

## 最佳实践总结

1. **类型优先**：优先使用接口定义对象结构，类型别名用于联合类型和函数类型
2. **泛型约束**：合理使用泛型约束提高类型安全性
3. **类型守卫**：优先使用类型守卫而非类型断言
4. **工具类型**：充分利用TypeScript内置工具类型
5. **类型组织**：将类型定义组织在专门的文件中
6. **渐进增强**：从宽松类型开始，逐步收紧类型约束

遵循这些TypeScript类型定义规范将帮助团队构建更加类型安全、可维护的Vue应用程序。