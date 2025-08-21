# 异步编程规范

## 概述

本文档定义了Vue项目中异步编程的最佳实践，包括Promise和async/await的使用标准、错误处理模式、并发控制策略等，确保异步代码的可读性、可维护性和错误处理的一致性。

## Promise和async/await使用标准

### async/await优先原则
- 优先使用 async/await 而非 Promise.then()
- 保持代码的线性和可读性
- 合理处理异步操作的错误

```typescript
// ✅ 推荐 - 使用 async/await
const fetchUserProfile = async (userId: string): Promise<UserProfile> => {
  try {
    const user = await userApi.getUser(userId)
    const profile = await profileApi.getProfile(userId)
    
    return {
      ...user,
      profile
    }
  } catch (error) {
    console.error('Failed to fetch user profile:', error)
    throw new Error('Unable to load user profile')
  }
}

// ❌ 不推荐 - 使用 Promise.then()
const fetchUserProfile = (userId: string): Promise<UserProfile> => {
  return userApi.getUser(userId)
    .then(user => {
      return profileApi.getProfile(userId)
        .then(profile => ({
          ...user,
          profile
        }))
    })
    .catch(error => {
      console.error('Failed to fetch user profile:', error)
      throw new Error('Unable to load user profile')
    })
}
```

### Promise创建和使用
- 使用Promise构造函数创建自定义异步操作
- 正确处理Promise的resolve和reject

```typescript
// ✅ 推荐 - 创建Promise
const delay = (ms: number): Promise<void> => {
  return new Promise((resolve) => {
    setTimeout(resolve, ms)
  })
}

const timeout = <T>(promise: Promise<T>, ms: number): Promise<T> => {
  return new Promise((resolve, reject) => {
    const timer = setTimeout(() => {
      reject(new Error(`Operation timed out after ${ms}ms`))
    }, ms)

    promise
      .then(resolve)
      .catch(reject)
      .finally(() => clearTimeout(timer))
  })
}

// ✅ 推荐 - 使用示例
const fetchWithTimeout = async (url: string): Promise<Response> => {
  const fetchPromise = fetch(url)
  return timeout(fetchPromise, 5000)
}
```

### 并行异步操作
- 使用 Promise.all() 处理并行操作
- 使用 Promise.allSettled() 处理部分失败场景

```typescript
// ✅ 推荐 - 并行操作（全部成功）
const loadDashboardData = async (userId: string) => {
  try {
    const [user, notifications, settings] = await Promise.all([
      userApi.getUser(userId),
      notificationApi.getNotifications(userId),
      settingsApi.getUserSettings(userId)
    ])

    return {
      user,
      notifications,
      settings
    }
  } catch (error) {
    console.error('Failed to load dashboard data:', error)
    throw error
  }
}

// ✅ 推荐 - 并行操作（允许部分失败）
const loadOptionalData = async (userId: string) => {
  const results = await Promise.allSettled([
    userApi.getUser(userId),
    notificationApi.getNotifications(userId),
    settingsApi.getUserSettings(userId)
  ])

  const [userResult, notificationsResult, settingsResult] = results

  return {
    user: userResult.status === 'fulfilled' ? userResult.value : null,
    notifications: notificationsResult.status === 'fulfilled' ? notificationsResult.value : [],
    settings: settingsResult.status === 'fulfilled' ? settingsResult.value : null,
    errors: results
      .filter(result => result.status === 'rejected')
      .map(result => (result as PromiseRejectedResult).reason)
  }
}

// ❌ 不推荐 - 串行操作（性能差）
const loadDashboardDataSerial = async (userId: string) => {
  const user = await userApi.getUser(userId)
  const notifications = await notificationApi.getNotifications(userId)
  const settings = await settingsApi.getUserSettings(userId)

  return { user, notifications, settings }
}
```

### 条件异步操作
- 使用 Promise.race() 处理竞态条件
- 合理处理条件异步逻辑

```typescript
// ✅ 推荐 - 竞态操作
const fetchFromMultipleSources = async (query: string): Promise<SearchResult[]> => {
  const sources = [
    searchApi.searchInDatabase(query),
    searchApi.searchInCache(query),
    searchApi.searchInIndex(query)
  ]

  try {
    // 返回最快的结果
    const result = await Promise.race(sources)
    return result
  } catch (error) {
    // 如果最快的失败了，尝试其他源
    const results = await Promise.allSettled(sources)
    const successful = results.find(r => r.status === 'fulfilled')
    
    if (successful && successful.status === 'fulfilled') {
      return successful.value
    }
    
    throw new Error('All search sources failed')
  }
}

// ✅ 推荐 - 条件异步操作
const getUserData = async (userId: string, includeProfile = false) => {
  const user = await userApi.getUser(userId)
  
  if (includeProfile && user.hasProfile) {
    const profile = await profileApi.getProfile(userId)
    return { ...user, profile }
  }
  
  return user
}
```

## 错误处理模式指南

### 统一错误处理策略
- 创建统一的错误类型和处理机制
- 区分不同类型的错误

```typescript
// ✅ 推荐 - 错误类型定义
class ApiError extends Error {
  constructor(
    message: string,
    public statusCode: number,
    public code?: string
  ) {
    super(message)
    this.name = 'ApiError'
  }
}

class ValidationError extends Error {
  constructor(
    message: string,
    public field: string
  ) {
    super(message)
    this.name = 'ValidationError'
  }
}

class NetworkError extends Error {
  constructor(message: string) {
    super(message)
    this.name = 'NetworkError'
  }
}

// ✅ 推荐 - 错误处理工具函数
const handleApiError = (error: unknown): never => {
  if (error instanceof ApiError) {
    console.error(`API Error [${error.statusCode}]:`, error.message)
  } else if (error instanceof NetworkError) {
    console.error('Network Error:', error.message)
  } else if (error instanceof ValidationError) {
    console.error(`Validation Error [${error.field}]:`, error.message)
  } else {
    console.error('Unknown Error:', error)
  }
  
  throw error
}
```

### try-catch最佳实践
- 在适当的层级处理错误
- 提供有意义的错误信息

```typescript
// ✅ 推荐 - 分层错误处理
// 服务层 - 处理API错误
class UserService {
  async getUser(id: string): Promise<User> {
    try {
      const response = await fetch(`/api/users/${id}`)
      
      if (!response.ok) {
        throw new ApiError(
          `Failed to fetch user: ${response.statusText}`,
          response.status
        )
      }
      
      return await response.json()
    } catch (error) {
      if (error instanceof ApiError) {
        throw error
      }
      
      throw new NetworkError('Unable to connect to user service')
    }
  }
}

// 组合层 - 处理业务逻辑错误
class UserController {
  constructor(private userService: UserService) {}

  async getUserProfile(id: string): Promise<UserProfile | null> {
    try {
      const user = await this.userService.getUser(id)
      
      if (!user.isActive) {
        throw new ValidationError('User account is inactive', 'status')
      }
      
      return {
        id: user.id,
        name: user.name,
        email: user.email,
        lastLoginAt: user.lastLoginAt
      }
    } catch (error) {
      console.error(`Failed to get user profile for ${id}:`, error)
      
      if (error instanceof ValidationError) {
        return null
      }
      
      throw error
    }
  }
}

// UI层 - 处理用户界面错误
const loadUserProfile = async (userId: string) => {
  try {
    const profile = await userController.getUserProfile(userId)
    
    if (!profile) {
      showNotification('User profile is not available', 'warning')
      return
    }
    
    displayUserProfile(profile)
  } catch (error) {
    if (error instanceof ApiError && error.statusCode === 404) {
      showNotification('User not found', 'error')
    } else if (error instanceof NetworkError) {
      showNotification('Connection failed. Please try again.', 'error')
    } else {
      showNotification('An unexpected error occurred', 'error')
    }
  }
}
```

### 错误恢复策略
- 实现重试机制
- 提供降级方案

```typescript
// ✅ 推荐 - 重试机制
const withRetry = async <T>(
  operation: () => Promise<T>,
  maxRetries = 3,
  delayMs = 1000
): Promise<T> => {
  let lastError: Error

  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      return await operation()
    } catch (error) {
      lastError = error as Error
      
      if (attempt === maxRetries) {
        break
      }
      
      console.warn(`Attempt ${attempt} failed, retrying in ${delayMs}ms...`)
      await delay(delayMs)
      
      // 指数退避
      delayMs *= 2
    }
  }
  
  throw lastError!
}

// ✅ 推荐 - 使用重试机制
const fetchUserWithRetry = async (userId: string): Promise<User> => {
  return withRetry(
    () => userApi.getUser(userId),
    3,
    1000
  )
}

// ✅ 推荐 - 降级方案
const getUserDisplayName = async (userId: string): Promise<string> => {
  try {
    const user = await userApi.getUser(userId)
    return user.displayName || user.name
  } catch (error) {
    console.warn('Failed to fetch user, using fallback:', error)
    
    // 降级方案：从缓存获取
    try {
      const cachedUser = await cacheService.getUser(userId)
      return cachedUser?.name || 'Unknown User'
    } catch {
      return 'Unknown User'
    }
  }
}
```

## 并发控制最佳实践

### 限制并发数量
- 控制同时进行的异步操作数量
- 避免资源过度消耗

```typescript
// ✅ 推荐 - 并发限制工具
class ConcurrencyLimiter {
  private running = 0
  private queue: Array<() => void> = []

  constructor(private limit: number) {}

  async execute<T>(operation: () => Promise<T>): Promise<T> {
    return new Promise((resolve, reject) => {
      const task = async () => {
        try {
          this.running++
          const result = await operation()
          resolve(result)
        } catch (error) {
          reject(error)
        } finally {
          this.running--
          this.processQueue()
        }
      }

      if (this.running < this.limit) {
        task()
      } else {
        this.queue.push(task)
      }
    })
  }

  private processQueue() {
    if (this.queue.length > 0 && this.running < this.limit) {
      const task = this.queue.shift()!
      task()
    }
  }
}

// ✅ 推荐 - 使用并发限制
const limiter = new ConcurrencyLimiter(3)

const processUsers = async (userIds: string[]) => {
  const results = await Promise.all(
    userIds.map(id => 
      limiter.execute(() => userApi.getUser(id))
    )
  )
  
  return results
}
```

### 批量操作优化
- 合并多个小请求为批量请求
- 使用防抖和节流优化频繁操作

```typescript
// ✅ 推荐 - 批量请求
class BatchProcessor<T, R> {
  private batch: T[] = []
  private timer: NodeJS.Timeout | null = null

  constructor(
    private processor: (items: T[]) => Promise<R[]>,
    private batchSize = 10,
    private delayMs = 100
  ) {}

  async add(item: T): Promise<R> {
    return new Promise((resolve, reject) => {
      this.batch.push(item)
      
      const index = this.batch.length - 1
      
      if (this.batch.length >= this.batchSize) {
        this.processBatch()
      } else if (!this.timer) {
        this.timer = setTimeout(() => this.processBatch(), this.delayMs)
      }
      
      // 简化示例，实际实现需要更复杂的Promise管理
      this.processor([item]).then(results => resolve(results[0])).catch(reject)
    })
  }

  private async processBatch() {
    if (this.timer) {
      clearTimeout(this.timer)
      this.timer = null
    }

    if (this.batch.length === 0) return

    const currentBatch = this.batch.splice(0, this.batchSize)
    
    try {
      await this.processor(currentBatch)
    } catch (error) {
      console.error('Batch processing failed:', error)
    }
  }
}

// ✅ 推荐 - 防抖异步操作
const debounce = <T extends any[]>(
  func: (...args: T) => Promise<any>,
  delayMs: number
) => {
  let timeoutId: NodeJS.Timeout | null = null
  let latestResolve: ((value: any) => void) | null = null
  let latestReject: ((reason: any) => void) | null = null

  return (...args: T): Promise<any> => {
    return new Promise((resolve, reject) => {
      if (timeoutId) {
        clearTimeout(timeoutId)
      }

      latestResolve = resolve
      latestReject = reject

      timeoutId = setTimeout(async () => {
        try {
          const result = await func(...args)
          latestResolve?.(result)
        } catch (error) {
          latestReject?.(error)
        }
      }, delayMs)
    })
  }
}

// 使用示例
const debouncedSearch = debounce(
  async (query: string) => searchApi.search(query),
  300
)
```

### 取消异步操作
- 使用 AbortController 取消请求
- 实现可取消的异步操作

```typescript
// ✅ 推荐 - 可取消的异步操作
class CancellableOperation<T> {
  private abortController = new AbortController()
  private promise: Promise<T>

  constructor(operation: (signal: AbortSignal) => Promise<T>) {
    this.promise = operation(this.abortController.signal)
  }

  cancel(reason?: string) {
    this.abortController.abort(reason)
  }

  async execute(): Promise<T> {
    return this.promise
  }
}

// ✅ 推荐 - 支持取消的API调用
const fetchWithCancel = (url: string, signal?: AbortSignal): Promise<Response> => {
  return fetch(url, { signal })
}

// ✅ 推荐 - 在Vue组件中使用
import { ref, onUnmounted } from 'vue'

export const useAsyncOperation = () => {
  const loading = ref(false)
  const error = ref<Error | null>(null)
  const abortController = ref<AbortController | null>(null)

  const execute = async <T>(
    operation: (signal: AbortSignal) => Promise<T>
  ): Promise<T | null> => {
    try {
      loading.value = true
      error.value = null
      
      abortController.value = new AbortController()
      const result = await operation(abortController.value.signal)
      
      return result
    } catch (err) {
      if (err instanceof Error && err.name === 'AbortError') {
        console.log('Operation was cancelled')
        return null
      }
      
      error.value = err as Error
      throw err
    } finally {
      loading.value = false
      abortController.value = null
    }
  }

  const cancel = () => {
    abortController.value?.abort()
  }

  onUnmounted(() => {
    cancel()
  })

  return {
    loading: readonly(loading),
    error: readonly(error),
    execute,
    cancel
  }
}
```

## Vue组合式API中的异步处理

### 异步状态管理
- 在组合式API中正确处理异步状态

```typescript
// ✅ 推荐 - 异步状态组合函数
export const useAsyncData = <T>(
  fetcher: () => Promise<T>,
  options: {
    immediate?: boolean
    onError?: (error: Error) => void
  } = {}
) => {
  const data = ref<T | null>(null)
  const loading = ref(false)
  const error = ref<Error | null>(null)

  const execute = async () => {
    try {
      loading.value = true
      error.value = null
      
      const result = await fetcher()
      data.value = result
      
      return result
    } catch (err) {
      const errorObj = err as Error
      error.value = errorObj
      options.onError?.(errorObj)
      throw err
    } finally {
      loading.value = false
    }
  }

  const refresh = () => execute()

  if (options.immediate !== false) {
    execute()
  }

  return {
    data: readonly(data),
    loading: readonly(loading),
    error: readonly(error),
    execute,
    refresh
  }
}

// ✅ 推荐 - 在组件中使用
export default defineComponent({
  setup() {
    const { data: users, loading, error, refresh } = useAsyncData(
      () => userApi.getUsers(),
      {
        onError: (error) => {
          console.error('Failed to load users:', error)
          // 显示错误通知
        }
      }
    )

    return {
      users,
      loading,
      error,
      refresh
    }
  }
})
```

## 最佳实践总结

1. **async/await优先**：使用async/await而非Promise.then()提高代码可读性
2. **错误分层处理**：在不同层级适当处理错误，提供有意义的错误信息
3. **并发控制**：合理控制并发数量，避免资源过度消耗
4. **取消机制**：为长时间运行的异步操作提供取消功能
5. **重试策略**：实现合理的重试和降级机制
6. **状态管理**：在Vue组件中正确管理异步状态
7. **类型安全**：为异步操作提供完整的TypeScript类型支持

遵循这些异步编程规范将帮助团队编写更加健壮、可维护的异步代码，提高应用的用户体验和稳定性。