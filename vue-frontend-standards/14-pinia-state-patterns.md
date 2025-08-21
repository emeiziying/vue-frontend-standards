# Pinia 状态管理模式指南

## 概述

本文档定义了 Pinia 状态管理的高级模式和最佳实践，包括异步状态处理、错误管理和 Store 间通信等核心概念。

## 异步状态处理规范

### 1. 标准异步状态模式

```typescript
// stores/async-base.ts - 异步状态基础模式
export interface AsyncState<T> {
  data: T | null
  loading: boolean
  error: string | null
  lastFetch: number | null
}

export const createAsyncState = <T>(): AsyncState<T> => ({
  data: null,
  loading: false,
  error: null,
  lastFetch: null
})

// 异步操作包装器
export const withAsyncState = <T, P extends any[]>(
  asyncFn: (...args: P) => Promise<T>
) => {
  return async (state: Ref<AsyncState<T>>, ...args: P): Promise<T> => {
    state.value.loading = true
    state.value.error = null
    
    try {
      const result = await asyncFn(...args)
      state.value.data = result
      state.value.lastFetch = Date.now()
      return result
    } catch (error) {
      const errorMessage = error instanceof Error ? error.message : '操作失败'
      state.value.error = errorMessage
      throw error
    } finally {
      state.value.loading = false
    }
  }
}
```

### 2. 实际应用示例

```typescript
// stores/user.ts
export const useUserStore = defineStore('user', () => {
  // 用户信息异步状态
  const userState = ref<AsyncState<User>>(createAsyncState())
  
  // 用户列表异步状态
  const usersState = ref<AsyncState<User[]>>(createAsyncState())
  
  // 获取当前用户信息
  const fetchCurrentUser = withAsyncState(async () => {
    const response = await userApi.getCurrentUser()
    return response.data
  })
  
  // 获取用户列表
  const fetchUsers = withAsyncState(async (params: UserListParams) => {
    const response = await userApi.getUsers(params)
    return response.data
  })
  
  // 更新用户信息
  const updateUser = async (userId: string, userData: Partial<User>) => {
    userState.value.loading = true
    userState.value.error = null
    
    try {
      const response = await userApi.updateUser(userId, userData)
      
      // 更新当前用户状态
      if (userState.value.data?.id === userId) {
        userState.value.data = { ...userState.value.data, ...response.data }
      }
      
      // 更新用户列表中的对应项
      if (usersState.value.data) {
        const index = usersState.value.data.findIndex(u => u.id === userId)
        if (index !== -1) {
          usersState.value.data[index] = { ...usersState.value.data[index], ...response.data }
        }
      }
      
      return response.data
    } catch (error) {
      const errorMessage = error instanceof Error ? error.message : '更新用户失败'
      userState.value.error = errorMessage
      throw error
    } finally {
      userState.value.loading = false
    }
  }
  
  return {
    // 状态
    userState: readonly(userState),
    usersState: readonly(usersState),
    
    // 计算属性
    currentUser: computed(() => userState.value.data),
    users: computed(() => usersState.value.data || []),
    isLoadingUser: computed(() => userState.value.loading),
    isLoadingUsers: computed(() => usersState.value.loading),
    
    // 操作
    fetchCurrentUser: () => fetchCurrentUser(userState),
    fetchUsers: (params: UserListParams) => fetchUsers(usersState, params),
    updateUser
  }
})
```

### 3. 分页数据处理模式

```typescript
// stores/pagination.ts
export interface PaginatedState<T> extends AsyncState<T[]> {
  pagination: {
    page: number
    pageSize: number
    total: number
    hasMore: boolean
  }
}

export const usePaginatedStore = <T>(
  storeName: string,
  fetchFn: (params: PaginationParams) => Promise<PaginatedResponse<T>>
) => {
  return defineStore(storeName, () => {
    const state = ref<PaginatedState<T>>({
      data: [],
      loading: false,
      error: null,
      lastFetch: null,
      pagination: {
        page: 1,
        pageSize: 20,
        total: 0,
        hasMore: true
      }
    })
    
    // 加载数据
    const loadData = async (params: Partial<PaginationParams> = {}) => {
      const requestParams = {
        page: state.value.pagination.page,
        pageSize: state.value.pagination.pageSize,
        ...params
      }
      
      state.value.loading = true
      state.value.error = null
      
      try {
        const response = await fetchFn(requestParams)
        
        if (requestParams.page === 1) {
          // 首页加载，替换数据
          state.value.data = response.data
        } else {
          // 分页加载，追加数据
          state.value.data = [...state.value.data, ...response.data]
        }
        
        state.value.pagination = {
          page: response.pagination.page,
          pageSize: response.pagination.pageSize,
          total: response.pagination.total,
          hasMore: response.pagination.hasMore
        }
        
        state.value.lastFetch = Date.now()
      } catch (error) {
        state.value.error = error instanceof Error ? error.message : '加载失败'
        throw error
      } finally {
        state.value.loading = false
      }
    }
    
    // 加载下一页
    const loadMore = async () => {
      if (!state.value.pagination.hasMore || state.value.loading) return
      
      await loadData({
        page: state.value.pagination.page + 1
      })
    }
    
    // 刷新数据
    const refresh = async () => {
      await loadData({ page: 1 })
    }
    
    return {
      state: readonly(state),
      data: computed(() => state.value.data),
      loading: computed(() => state.value.loading),
      error: computed(() => state.value.error),
      pagination: computed(() => state.value.pagination),
      loadData,
      loadMore,
      refresh
    }
  })
}
```

### 4. 缓存和重新验证模式

```typescript
// stores/cache.ts
export interface CacheOptions {
  ttl?: number // 缓存时间（毫秒）
  staleWhileRevalidate?: boolean // 返回过期数据同时后台更新
}

export const useCachedStore = <T>(
  storeName: string,
  fetchFn: () => Promise<T>,
  options: CacheOptions = {}
) => {
  return defineStore(storeName, () => {
    const { ttl = 5 * 60 * 1000, staleWhileRevalidate = true } = options
    
    const state = ref<AsyncState<T>>(createAsyncState())
    
    const isStale = computed(() => {
      if (!state.value.lastFetch) return true
      return Date.now() - state.value.lastFetch > ttl
    })
    
    const fetchData = async (forceRefresh = false) => {
      // 如果有缓存且未过期，直接返回
      if (!forceRefresh && state.value.data && !isStale.value) {
        return state.value.data
      }
      
      // 如果启用 stale-while-revalidate 且有过期数据
      if (staleWhileRevalidate && state.value.data && isStale.value) {
        // 后台更新，立即返回过期数据
        fetchData(true).catch(console.error)
        return state.value.data
      }
      
      state.value.loading = true
      state.value.error = null
      
      try {
        const result = await fetchFn()
        state.value.data = result
        state.value.lastFetch = Date.now()
        return result
      } catch (error) {
        state.value.error = error instanceof Error ? error.message : '获取数据失败'
        throw error
      } finally {
        state.value.loading = false
      }
    }
    
    return {
      state: readonly(state),
      data: computed(() => state.value.data),
      loading: computed(() => state.value.loading),
      error: computed(() => state.value.error),
      isStale,
      fetchData
    }
  })
}
```

## 错误状态管理标准

### 1. 错误类型定义

```typescript
// types/error.ts
export enum ErrorType {
  NETWORK = 'NETWORK',
  VALIDATION = 'VALIDATION',
  AUTHORIZATION = 'AUTHORIZATION',
  SERVER = 'SERVER',
  UNKNOWN = 'UNKNOWN'
}

export interface AppError {
  type: ErrorType
  message: string
  code?: string | number
  details?: any
  timestamp: number
}

export class NetworkError extends Error implements AppError {
  type = ErrorType.NETWORK
  timestamp = Date.now()
  
  constructor(message: string, public code?: number) {
    super(message)
    this.name = 'NetworkError'
  }
}

export class ValidationError extends Error implements AppError {
  type = ErrorType.VALIDATION
  timestamp = Date.now()
  
  constructor(message: string, public details?: Record<string, string[]>) {
    super(message)
    this.name = 'ValidationError'
  }
}
```

### 2. 全局错误处理 Store

```typescript
// stores/error.ts
export const useErrorStore = defineStore('error', () => {
  const errors = ref<AppError[]>([])
  const globalError = ref<AppError | null>(null)
  
  // 添加错误
  const addError = (error: AppError | Error | string) => {
    const appError: AppError = typeof error === 'string' 
      ? {
          type: ErrorType.UNKNOWN,
          message: error,
          timestamp: Date.now()
        }
      : error instanceof Error
        ? {
            type: ErrorType.UNKNOWN,
            message: error.message,
            timestamp: Date.now()
          }
        : error
    
    errors.value.push(appError)
    
    // 设置全局错误（用于显示通知）
    if (appError.type !== ErrorType.VALIDATION) {
      globalError.value = appError
    }
  }
  
  // 清除错误
  const clearError = (index?: number) => {
    if (typeof index === 'number') {
      errors.value.splice(index, 1)
    } else {
      errors.value = []
    }
  }
  
  // 清除全局错误
  const clearGlobalError = () => {
    globalError.value = null
  }
  
  // 获取特定类型的错误
  const getErrorsByType = (type: ErrorType) => {
    return errors.value.filter(error => error.type === type)
  }
  
  return {
    errors: readonly(errors),
    globalError: readonly(globalError),
    addError,
    clearError,
    clearGlobalError,
    getErrorsByType
  }
})
```

### 3. 错误处理中间件

```typescript
// utils/error-handler.ts
export const createErrorHandler = (errorStore: ReturnType<typeof useErrorStore>) => {
  return {
    // HTTP 错误处理
    handleHttpError: (error: any): AppError => {
      if (error.response) {
        const { status, data } = error.response
        
        switch (status) {
          case 400:
            return new ValidationError(data.message || '请求参数错误', data.errors)
          case 401:
            return {
              type: ErrorType.AUTHORIZATION,
              message: '未授权访问',
              code: status,
              timestamp: Date.now()
            }
          case 403:
            return {
              type: ErrorType.AUTHORIZATION,
              message: '权限不足',
              code: status,
              timestamp: Date.now()
            }
          case 500:
            return {
              type: ErrorType.SERVER,
              message: '服务器内部错误',
              code: status,
              timestamp: Date.now()
            }
          default:
            return {
              type: ErrorType.SERVER,
              message: data.message || '服务器错误',
              code: status,
              timestamp: Date.now()
            }
        }
      } else if (error.request) {
        return new NetworkError('网络连接失败')
      } else {
        return {
          type: ErrorType.UNKNOWN,
          message: error.message || '未知错误',
          timestamp: Date.now()
        }
      }
    },
    
    // 异步操作错误包装
    wrapAsyncAction: <T extends any[], R>(
      action: (...args: T) => Promise<R>
    ) => {
      return async (...args: T): Promise<R> => {
        try {
          return await action(...args)
        } catch (error) {
          const appError = this.handleHttpError(error)
          errorStore.addError(appError)
          throw appError
        }
      }
    }
  }
}
```

### 4. 表单验证错误处理

```typescript
// stores/form.ts
export const useFormStore = defineStore('form', () => {
  const validationErrors = ref<Record<string, string[]>>({})
  const isSubmitting = ref(false)
  
  // 设置字段错误
  const setFieldError = (field: string, errors: string[]) => {
    validationErrors.value[field] = errors
  }
  
  // 清除字段错误
  const clearFieldError = (field: string) => {
    delete validationErrors.value[field]
  }
  
  // 清除所有错误
  const clearAllErrors = () => {
    validationErrors.value = {}
  }
  
  // 设置服务器验证错误
  const setServerErrors = (errors: Record<string, string[]>) => {
    validationErrors.value = { ...validationErrors.value, ...errors }
  }
  
  // 表单提交包装器
  const submitForm = async <T>(
    submitFn: () => Promise<T>,
    onSuccess?: (result: T) => void,
    onError?: (error: AppError) => void
  ) => {
    isSubmitting.value = true
    clearAllErrors()
    
    try {
      const result = await submitFn()
      onSuccess?.(result)
      return result
    } catch (error) {
      if (error instanceof ValidationError && error.details) {
        setServerErrors(error.details)
      }
      onError?.(error as AppError)
      throw error
    } finally {
      isSubmitting.value = false
    }
  }
  
  return {
    validationErrors: readonly(validationErrors),
    isSubmitting: readonly(isSubmitting),
    setFieldError,
    clearFieldError,
    clearAllErrors,
    setServerErrors,
    submitForm
  }
})
```

## Store 间通信最佳实践

### 1. 事件驱动通信

```typescript
// utils/event-bus.ts
export type EventMap = {
  'user:login': { user: User }
  'user:logout': {}
  'cart:item-added': { item: CartItem }
  'order:created': { order: Order }
  'notification:show': { message: string; type: 'success' | 'error' | 'info' }
}

export const eventBus = mitt<EventMap>()

// stores/user.ts
export const useUserStore = defineStore('user', () => {
  const user = ref<User | null>(null)
  
  const login = async (credentials: LoginCredentials) => {
    // 登录逻辑...
    user.value = result
    
    // 发送登录事件
    eventBus.emit('user:login', { user: result })
  }
  
  const logout = () => {
    const currentUser = user.value
    user.value = null
    
    // 发送登出事件
    eventBus.emit('user:logout', {})
  }
  
  return { user, login, logout }
})

// stores/cart.ts
export const useCartStore = defineStore('cart', () => {
  const items = ref<CartItem[]>([])
  
  // 监听用户登出事件，清空购物车
  eventBus.on('user:logout', () => {
    items.value = []
  })
  
  const addItem = (item: CartItem) => {
    items.value.push(item)
    eventBus.emit('cart:item-added', { item })
  }
  
  return { items, addItem }
})
```

### 2. 依赖注入模式

```typescript
// stores/dependencies.ts
export interface StoreDependencies {
  userStore: ReturnType<typeof useUserStore>
  cartStore: ReturnType<typeof useCartStore>
  notificationStore: ReturnType<typeof useNotificationStore>
}

// stores/order.ts
export const useOrderStore = defineStore('order', () => {
  const orders = ref<Order[]>([])
  
  // 注入依赖的 stores
  const deps = {
    userStore: useUserStore(),
    cartStore: useCartStore(),
    notificationStore: useNotificationStore()
  }
  
  const createOrder = async (orderData: CreateOrderData) => {
    // 检查用户登录状态
    if (!deps.userStore.isLoggedIn) {
      throw new Error('请先登录')
    }
    
    try {
      const order = await orderApi.create({
        ...orderData,
        userId: deps.userStore.user!.id,
        items: deps.cartStore.items
      })
      
      orders.value.push(order)
      
      // 清空购物车
      deps.cartStore.clearItems()
      
      // 显示成功通知
      deps.notificationStore.showSuccess('订单创建成功')
      
      return order
    } catch (error) {
      deps.notificationStore.showError('订单创建失败')
      throw error
    }
  }
  
  return { orders, createOrder }
})
```

### 3. 组合式 Store 模式

```typescript
// stores/composables/useShoppingFlow.ts
export const useShoppingFlow = () => {
  const userStore = useUserStore()
  const cartStore = useCartStore()
  const orderStore = useOrderStore()
  const paymentStore = usePaymentStore()
  
  // 完整的购物流程
  const completeOrder = async (paymentMethod: PaymentMethod) => {
    // 1. 验证用户登录
    if (!userStore.isLoggedIn) {
      throw new Error('请先登录')
    }
    
    // 2. 验证购物车
    if (cartStore.isEmpty) {
      throw new Error('购物车为空')
    }
    
    // 3. 创建订单
    const order = await orderStore.createOrder({
      items: cartStore.items,
      totalAmount: cartStore.totalPrice
    })
    
    // 4. 处理支付
    const payment = await paymentStore.processPayment({
      orderId: order.id,
      amount: order.totalAmount,
      method: paymentMethod
    })
    
    // 5. 更新订单状态
    await orderStore.updateOrderStatus(order.id, 'paid')
    
    return { order, payment }
  }
  
  return {
    completeOrder,
    // 暴露相关状态
    user: userStore.user,
    cartItems: cartStore.items,
    cartTotal: cartStore.totalPrice,
    isProcessing: computed(() => 
      orderStore.loading || paymentStore.loading
    )
  }
}
```

### 4. Store 状态同步

```typescript
// stores/sync.ts
export const useSyncStore = defineStore('sync', () => {
  const syncQueue = ref<SyncTask[]>([])
  const isSyncing = ref(false)
  
  // 添加同步任务
  const addSyncTask = (task: SyncTask) => {
    syncQueue.value.push(task)
    processSyncQueue()
  }
  
  // 处理同步队列
  const processSyncQueue = async () => {
    if (isSyncing.value || syncQueue.value.length === 0) return
    
    isSyncing.value = true
    
    while (syncQueue.value.length > 0) {
      const task = syncQueue.value.shift()!
      
      try {
        await task.execute()
        task.onSuccess?.()
      } catch (error) {
        task.onError?.(error)
        
        // 重试逻辑
        if (task.retryCount < task.maxRetries) {
          task.retryCount++
          syncQueue.value.unshift(task)
        }
      }
    }
    
    isSyncing.value = false
  }
  
  return { addSyncTask, isSyncing: readonly(isSyncing) }
})

// 在其他 store 中使用同步
export const useDataStore = defineStore('data', () => {
  const syncStore = useSyncStore()
  const localData = ref<any[]>([])
  
  const updateLocalData = (data: any) => {
    localData.value.push(data)
    
    // 添加同步任务
    syncStore.addSyncTask({
      id: `sync-${Date.now()}`,
      execute: () => api.syncData(data),
      retryCount: 0,
      maxRetries: 3,
      onSuccess: () => console.log('数据同步成功'),
      onError: (error) => console.error('数据同步失败', error)
    })
  }
  
  return { localData, updateLocalData }
})
```

## 性能优化模式

### 1. 懒加载 Store

```typescript
// stores/lazy-loader.ts
export const createLazyStore = <T>(
  storeFactory: () => T,
  storeName: string
): (() => T) => {
  let storeInstance: T | null = null
  
  return () => {
    if (!storeInstance) {
      storeInstance = storeFactory()
      console.log(`Lazy loaded store: ${storeName}`)
    }
    return storeInstance
  }
}

// 使用示例
export const useLazyProductStore = createLazyStore(
  () => useProductStore(),
  'product'
)
```

### 2. Store 数据分片

```typescript
// stores/chunked-data.ts
export const useChunkedDataStore = defineStore('chunked-data', () => {
  const chunks = ref<Map<string, any[]>>(new Map())
  const chunkSize = 100
  
  const addData = (key: string, data: any[]) => {
    const chunkedData = []
    for (let i = 0; i < data.length; i += chunkSize) {
      chunkedData.push(data.slice(i, i + chunkSize))
    }
    chunks.value.set(key, chunkedData)
  }
  
  const getData = (key: string, chunkIndex = 0) => {
    const chunkedData = chunks.value.get(key)
    return chunkedData?.[chunkIndex] || []
  }
  
  return { addData, getData }
})
```

### 3. 内存管理

```typescript
// stores/memory-manager.ts
export const useMemoryManagerStore = defineStore('memory-manager', () => {
  const cacheSize = ref(0)
  const maxCacheSize = 50 * 1024 * 1024 // 50MB
  
  const trackMemoryUsage = (storeName: string, data: any) => {
    const size = JSON.stringify(data).length * 2 // 粗略估算
    cacheSize.value += size
    
    if (cacheSize.value > maxCacheSize) {
      console.warn(`Cache size exceeded: ${cacheSize.value} bytes`)
      // 触发清理逻辑
      triggerCleanup()
    }
  }
  
  const triggerCleanup = () => {
    // 通知各个 store 清理过期数据
    eventBus.emit('memory:cleanup', {})
  }
  
  return { trackMemoryUsage, cacheSize: readonly(cacheSize) }
})
```