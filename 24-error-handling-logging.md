# 错误处理和日志规范

## 概述

本文档定义了Vue前端项目中错误处理和日志记录的标准规范，包括全局错误处理配置、错误边界使用、日志记录标准和错误上报机制。

## 1. 全局错误处理配置

### 1.1 Vue应用全局错误处理

```typescript
// src/main.ts
import { createApp } from 'vue'
import App from './App.vue'
import { setupErrorHandler } from './utils/errorHandler'

const app = createApp(App)

// 配置全局错误处理器
app.config.errorHandler = (err, instance, info) => {
  console.error('Vue Error:', err)
  console.error('Component:', instance)
  console.error('Error Info:', info)
  
  // 发送错误到监控服务
  setupErrorHandler().captureException(err, {
    context: 'vue-error-handler',
    component: instance?.$options.name || 'Unknown',
    info
  })
}

// 配置警告处理器（仅开发环境）
if (import.meta.env.DEV) {
  app.config.warnHandler = (msg, instance, trace) => {
    console.warn('Vue Warning:', msg)
    console.warn('Trace:', trace)
  }
}

app.mount('#app')
```

### 1.2 未捕获异常处理

```typescript
// src/utils/errorHandler.ts
interface ErrorContext {
  context: string
  component?: string
  info?: string
  userId?: string
  timestamp?: number
}

class ErrorHandler {
  private static instance: ErrorHandler
  
  private constructor() {
    this.setupGlobalHandlers()
  }
  
  static getInstance(): ErrorHandler {
    if (!ErrorHandler.instance) {
      ErrorHandler.instance = new ErrorHandler()
    }
    return ErrorHandler.instance
  }
  
  private setupGlobalHandlers(): void {
    // 捕获未处理的Promise拒绝
    window.addEventListener('unhandledrejection', (event) => {
      console.error('Unhandled Promise Rejection:', event.reason)
      this.captureException(event.reason, {
        context: 'unhandled-promise-rejection'
      })
      
      // 阻止默认行为（在控制台显示错误）
      event.preventDefault()
    })
    
    // 捕获全局JavaScript错误
    window.addEventListener('error', (event) => {
      console.error('Global Error:', event.error)
      this.captureException(event.error, {
        context: 'global-error',
        info: `${event.filename}:${event.lineno}:${event.colno}`
      })
    })
    
    // 捕获资源加载错误
    window.addEventListener('error', (event) => {
      if (event.target !== window) {
        console.error('Resource Error:', event.target)
        this.captureException(new Error('Resource loading failed'), {
          context: 'resource-error',
          info: (event.target as any)?.src || (event.target as any)?.href
        })
      }
    }, true)
  }
  
  captureException(error: Error, context: ErrorContext): void {
    const errorData = {
      message: error.message,
      stack: error.stack,
      name: error.name,
      timestamp: Date.now(),
      url: window.location.href,
      userAgent: navigator.userAgent,
      ...context
    }
    
    // 发送到错误监控服务
    this.sendToMonitoring(errorData)
    
    // 记录到本地日志
    this.logError(errorData)
  }
  
  private sendToMonitoring(errorData: any): void {
    // 这里集成具体的错误监控服务，如Sentry、Bugsnag等
    if (import.meta.env.PROD) {
      // 生产环境发送到监控服务
      fetch('/api/errors', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(errorData)
      }).catch(err => {
        console.error('Failed to send error to monitoring:', err)
      })
    }
  }
  
  private logError(errorData: any): void {
    // 本地错误日志记录
    console.group('🚨 Error Report')
    console.error('Message:', errorData.message)
    console.error('Context:', errorData.context)
    console.error('Stack:', errorData.stack)
    console.error('Timestamp:', new Date(errorData.timestamp).toISOString())
    console.groupEnd()
  }
}

export const setupErrorHandler = () => ErrorHandler.getInstance()
```

## 2. 错误边界使用指南

### 2.1 Vue 3错误边界组件

```vue
<!-- src/components/ErrorBoundary.vue -->
<template>
  <div v-if="hasError" class="error-boundary">
    <div class="error-boundary__content">
      <h2 class="error-boundary__title">{{ title }}</h2>
      <p class="error-boundary__message">{{ message }}</p>
      <button 
        v-if="showRetry"
        @click="retry"
        class="error-boundary__retry-btn"
      >
        重试
      </button>
      <details v-if="showDetails && isDev" class="error-boundary__details">
        <summary>错误详情</summary>
        <pre>{{ errorDetails }}</pre>
      </details>
    </div>
  </div>
  <slot v-else />
</template>

<script setup lang="ts">
import { ref, onErrorCaptured, nextTick } from 'vue'
import { setupErrorHandler } from '@/utils/errorHandler'

interface Props {
  title?: string
  message?: string
  showRetry?: boolean
  showDetails?: boolean
  fallbackComponent?: any
}

const props = withDefaults(defineProps<Props>(), {
  title: '出现了一些问题',
  message: '页面遇到错误，请稍后重试',
  showRetry: true,
  showDetails: false
})

const emit = defineEmits<{
  error: [error: Error, info: string]
  retry: []
}>()

const hasError = ref(false)
const errorDetails = ref('')
const isDev = import.meta.env.DEV

onErrorCaptured((error: Error, instance, info: string) => {
  hasError.value = true
  errorDetails.value = `${error.message}\n${error.stack}`
  
  // 发送错误到监控服务
  setupErrorHandler().captureException(error, {
    context: 'error-boundary',
    component: instance?.$options.name || 'Unknown',
    info
  })
  
  // 触发错误事件
  emit('error', error, info)
  
  // 返回false阻止错误继续传播
  return false
})

const retry = async () => {
  hasError.value = false
  errorDetails.value = ''
  emit('retry')
  
  // 等待下一个tick确保组件重新渲染
  await nextTick()
}
</script>

<style scoped>
.error-boundary {
  @apply min-h-[200px] flex items-center justify-center p-6 bg-red-50 border border-red-200 rounded-lg;
}

.error-boundary__content {
  @apply text-center max-w-md;
}

.error-boundary__title {
  @apply text-xl font-semibold text-red-800 mb-2;
}

.error-boundary__message {
  @apply text-red-600 mb-4;
}

.error-boundary__retry-btn {
  @apply px-4 py-2 bg-red-600 text-white rounded hover:bg-red-700 transition-colors;
}

.error-boundary__details {
  @apply mt-4 text-left;
}

.error-boundary__details summary {
  @apply cursor-pointer text-red-700 font-medium;
}

.error-boundary__details pre {
  @apply mt-2 p-3 bg-red-100 text-red-800 text-sm rounded overflow-auto;
}
</style>
```

### 2.2 错误边界使用示例

```vue
<!-- 页面级错误边界 -->
<template>
  <ErrorBoundary
    title="页面加载失败"
    message="页面遇到错误，请刷新页面重试"
    :show-details="isDev"
    @error="handlePageError"
    @retry="handleRetry"
  >
    <router-view />
  </ErrorBoundary>
</template>

<!-- 组件级错误边界 -->
<template>
  <div class="user-profile">
    <ErrorBoundary
      title="用户信息加载失败"
      message="无法加载用户信息，请稍后重试"
      @error="handleUserError"
    >
      <UserInfo :user-id="userId" />
    </ErrorBoundary>
    
    <ErrorBoundary
      title="订单信息加载失败"
      message="无法加载订单信息，请稍后重试"
      @error="handleOrderError"
    >
      <OrderList :user-id="userId" />
    </ErrorBoundary>
  </div>
</template>
```

## 3. 日志记录标准和格式

### 3.1 日志级别定义

```typescript
// src/utils/logger.ts
export enum LogLevel {
  DEBUG = 0,
  INFO = 1,
  WARN = 2,
  ERROR = 3,
  FATAL = 4
}

export interface LogEntry {
  level: LogLevel
  message: string
  timestamp: number
  context?: string
  data?: any
  userId?: string
  sessionId?: string
  traceId?: string
}

class Logger {
  private static instance: Logger
  private logLevel: LogLevel
  private logs: LogEntry[] = []
  private maxLogs = 1000
  
  private constructor() {
    this.logLevel = import.meta.env.DEV ? LogLevel.DEBUG : LogLevel.INFO
  }
  
  static getInstance(): Logger {
    if (!Logger.instance) {
      Logger.instance = new Logger()
    }
    return Logger.instance
  }
  
  setLevel(level: LogLevel): void {
    this.logLevel = level
  }
  
  debug(message: string, context?: string, data?: any): void {
    this.log(LogLevel.DEBUG, message, context, data)
  }
  
  info(message: string, context?: string, data?: any): void {
    this.log(LogLevel.INFO, message, context, data)
  }
  
  warn(message: string, context?: string, data?: any): void {
    this.log(LogLevel.WARN, message, context, data)
  }
  
  error(message: string, context?: string, data?: any): void {
    this.log(LogLevel.ERROR, message, context, data)
  }
  
  fatal(message: string, context?: string, data?: any): void {
    this.log(LogLevel.FATAL, message, context, data)
  }
  
  private log(level: LogLevel, message: string, context?: string, data?: any): void {
    if (level < this.logLevel) return
    
    const logEntry: LogEntry = {
      level,
      message,
      timestamp: Date.now(),
      context,
      data,
      userId: this.getCurrentUserId(),
      sessionId: this.getSessionId(),
      traceId: this.generateTraceId()
    }
    
    // 添加到内存日志
    this.addToMemoryLog(logEntry)
    
    // 控制台输出
    this.consoleLog(logEntry)
    
    // 发送到日志服务
    if (level >= LogLevel.WARN) {
      this.sendToLogService(logEntry)
    }
  }
  
  private addToMemoryLog(logEntry: LogEntry): void {
    this.logs.push(logEntry)
    
    // 保持日志数量在限制内
    if (this.logs.length > this.maxLogs) {
      this.logs = this.logs.slice(-this.maxLogs)
    }
  }
  
  private consoleLog(logEntry: LogEntry): void {
    const timestamp = new Date(logEntry.timestamp).toISOString()
    const levelName = LogLevel[logEntry.level]
    const prefix = `[${timestamp}] [${levelName}]`
    
    const logMessage = logEntry.context 
      ? `${prefix} [${logEntry.context}] ${logEntry.message}`
      : `${prefix} ${logEntry.message}`
    
    switch (logEntry.level) {
      case LogLevel.DEBUG:
        console.debug(logMessage, logEntry.data)
        break
      case LogLevel.INFO:
        console.info(logMessage, logEntry.data)
        break
      case LogLevel.WARN:
        console.warn(logMessage, logEntry.data)
        break
      case LogLevel.ERROR:
      case LogLevel.FATAL:
        console.error(logMessage, logEntry.data)
        break
    }
  }
  
  private sendToLogService(logEntry: LogEntry): void {
    if (import.meta.env.PROD) {
      fetch('/api/logs', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(logEntry)
      }).catch(err => {
        console.error('Failed to send log to service:', err)
      })
    }
  }
  
  private getCurrentUserId(): string | undefined {
    // 从用户状态管理中获取用户ID
    return undefined // 实际实现中应该从store获取
  }
  
  private getSessionId(): string {
    let sessionId = sessionStorage.getItem('sessionId')
    if (!sessionId) {
      sessionId = this.generateId()
      sessionStorage.setItem('sessionId', sessionId)
    }
    return sessionId
  }
  
  private generateTraceId(): string {
    return this.generateId()
  }
  
  private generateId(): string {
    return Math.random().toString(36).substr(2, 9)
  }
  
  // 获取内存中的日志
  getLogs(): LogEntry[] {
    return [...this.logs]
  }
  
  // 清空内存日志
  clearLogs(): void {
    this.logs = []
  }
  
  // 导出日志
  exportLogs(): string {
    return JSON.stringify(this.logs, null, 2)
  }
}

export const logger = Logger.getInstance()
```

### 3.2 日志使用示例

```typescript
// 在组件中使用日志
import { logger } from '@/utils/logger'

export default {
  setup() {
    const fetchUserData = async (userId: string) => {
      logger.info('开始获取用户数据', 'UserProfile', { userId })
      
      try {
        const response = await api.getUser(userId)
        logger.info('用户数据获取成功', 'UserProfile', { 
          userId, 
          dataSize: JSON.stringify(response.data).length 
        })
        return response.data
      } catch (error) {
        logger.error('用户数据获取失败', 'UserProfile', { 
          userId, 
          error: error.message 
        })
        throw error
      }
    }
    
    return { fetchUserData }
  }
}
```

### 3.3 结构化日志格式

```typescript
// src/utils/structuredLogger.ts
interface StructuredLogData {
  // 基础信息
  timestamp: string
  level: string
  message: string
  
  // 上下文信息
  service: string
  version: string
  environment: string
  
  // 用户信息
  userId?: string
  sessionId: string
  
  // 请求信息
  requestId?: string
  url?: string
  method?: string
  
  // 性能信息
  duration?: number
  
  // 错误信息
  error?: {
    name: string
    message: string
    stack: string
  }
  
  // 自定义数据
  extra?: Record<string, any>
}

export class StructuredLogger {
  private baseData: Partial<StructuredLogData>
  
  constructor() {
    this.baseData = {
      service: 'vue-frontend',
      version: import.meta.env.VITE_APP_VERSION || '1.0.0',
      environment: import.meta.env.MODE,
      sessionId: this.getSessionId()
    }
  }
  
  log(level: string, message: string, extra?: Record<string, any>): void {
    const logData: StructuredLogData = {
      ...this.baseData,
      timestamp: new Date().toISOString(),
      level,
      message,
      url: window.location.href,
      extra
    }
    
    // 发送结构化日志
    this.send(logData)
  }
  
  private send(logData: StructuredLogData): void {
    // 发送到日志收集服务
    console.log(JSON.stringify(logData))
  }
  
  private getSessionId(): string {
    return sessionStorage.getItem('sessionId') || 'unknown'
  }
}
```

## 4. 错误上报机制规范

### 4.1 错误分类和优先级

```typescript
// src/utils/errorReporting.ts
export enum ErrorSeverity {
  LOW = 'low',
  MEDIUM = 'medium',
  HIGH = 'high',
  CRITICAL = 'critical'
}

export enum ErrorCategory {
  NETWORK = 'network',
  VALIDATION = 'validation',
  PERMISSION = 'permission',
  BUSINESS_LOGIC = 'business_logic',
  SYSTEM = 'system',
  THIRD_PARTY = 'third_party'
}

interface ErrorReport {
  id: string
  category: ErrorCategory
  severity: ErrorSeverity
  message: string
  stack?: string
  context: Record<string, any>
  timestamp: number
  userId?: string
  sessionId: string
  userAgent: string
  url: string
}

class ErrorReporting {
  private static instance: ErrorReporting
  private reportQueue: ErrorReport[] = []
  private isOnline = navigator.onLine
  
  private constructor() {
    this.setupNetworkListeners()
    this.setupPeriodicFlush()
  }
  
  static getInstance(): ErrorReporting {
    if (!ErrorReporting.instance) {
      ErrorReporting.instance = new ErrorReporting()
    }
    return ErrorReporting.instance
  }
  
  report(
    error: Error | string,
    category: ErrorCategory,
    severity: ErrorSeverity,
    context: Record<string, any> = {}
  ): void {
    const errorReport: ErrorReport = {
      id: this.generateId(),
      category,
      severity,
      message: typeof error === 'string' ? error : error.message,
      stack: typeof error === 'object' ? error.stack : undefined,
      context,
      timestamp: Date.now(),
      userId: this.getCurrentUserId(),
      sessionId: this.getSessionId(),
      userAgent: navigator.userAgent,
      url: window.location.href
    }
    
    // 添加到队列
    this.reportQueue.push(errorReport)
    
    // 立即发送高优先级错误
    if (severity === ErrorSeverity.CRITICAL || severity === ErrorSeverity.HIGH) {
      this.flush()
    }
  }
  
  private setupNetworkListeners(): void {
    window.addEventListener('online', () => {
      this.isOnline = true
      this.flush()
    })
    
    window.addEventListener('offline', () => {
      this.isOnline = false
    })
  }
  
  private setupPeriodicFlush(): void {
    // 每30秒发送一次错误报告
    setInterval(() => {
      if (this.reportQueue.length > 0) {
        this.flush()
      }
    }, 30000)
  }
  
  private async flush(): Promise<void> {
    if (!this.isOnline || this.reportQueue.length === 0) {
      return
    }
    
    const reports = [...this.reportQueue]
    this.reportQueue = []
    
    try {
      await fetch('/api/error-reports', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({ reports })
      })
      
      logger.info('错误报告发送成功', 'ErrorReporting', { count: reports.length })
    } catch (error) {
      // 发送失败，重新加入队列
      this.reportQueue.unshift(...reports)
      logger.error('错误报告发送失败', 'ErrorReporting', { error })
    }
  }
  
  private generateId(): string {
    return `${Date.now()}-${Math.random().toString(36).substr(2, 9)}`
  }
  
  private getCurrentUserId(): string | undefined {
    // 从用户状态管理中获取
    return undefined
  }
  
  private getSessionId(): string {
    return sessionStorage.getItem('sessionId') || 'unknown'
  }
}

export const errorReporting = ErrorReporting.getInstance()
```

### 4.2 自动错误上报集成

```typescript
// src/plugins/errorReporting.ts
import { App } from 'vue'
import { errorReporting, ErrorCategory, ErrorSeverity } from '@/utils/errorReporting'
import { logger } from '@/utils/logger'

export function setupErrorReporting(app: App): void {
  // Vue错误处理
  app.config.errorHandler = (err, instance, info) => {
    logger.error('Vue组件错误', 'VueErrorHandler', { err, info })
    
    errorReporting.report(
      err,
      ErrorCategory.SYSTEM,
      ErrorSeverity.HIGH,
      {
        component: instance?.$options.name || 'Unknown',
        errorInfo: info,
        props: instance?.$props
      }
    )
  }
  
  // 全局Promise拒绝处理
  window.addEventListener('unhandledrejection', (event) => {
    logger.error('未处理的Promise拒绝', 'UnhandledRejection', { reason: event.reason })
    
    errorReporting.report(
      event.reason,
      ErrorCategory.SYSTEM,
      ErrorSeverity.MEDIUM,
      {
        type: 'unhandled-promise-rejection'
      }
    )
  })
  
  // 全局JavaScript错误
  window.addEventListener('error', (event) => {
    logger.error('全局JavaScript错误', 'GlobalError', { 
      message: event.message,
      filename: event.filename,
      lineno: event.lineno,
      colno: event.colno
    })
    
    errorReporting.report(
      event.error || event.message,
      ErrorCategory.SYSTEM,
      ErrorSeverity.HIGH,
      {
        filename: event.filename,
        lineno: event.lineno,
        colno: event.colno
      }
    )
  })
}
```

### 4.3 业务错误上报示例

```typescript
// 在业务代码中使用错误上报
import { errorReporting, ErrorCategory, ErrorSeverity } from '@/utils/errorReporting'

// API请求错误
const handleApiError = (error: any, endpoint: string) => {
  errorReporting.report(
    error,
    ErrorCategory.NETWORK,
    error.status >= 500 ? ErrorSeverity.HIGH : ErrorSeverity.MEDIUM,
    {
      endpoint,
      status: error.status,
      response: error.response?.data
    }
  )
}

// 业务逻辑错误
const handleBusinessError = (message: string, context: any) => {
  errorReporting.report(
    message,
    ErrorCategory.BUSINESS_LOGIC,
    ErrorSeverity.MEDIUM,
    context
  )
}

// 权限错误
const handlePermissionError = (action: string, resource: string) => {
  errorReporting.report(
    `权限不足: ${action} on ${resource}`,
    ErrorCategory.PERMISSION,
    ErrorSeverity.MEDIUM,
    { action, resource }
  )
}
```

## 5. 错误监控和分析

### 5.1 错误统计和分析

```typescript
// src/utils/errorAnalytics.ts
interface ErrorStats {
  totalErrors: number
  errorsByCategory: Record<ErrorCategory, number>
  errorsBySeverity: Record<ErrorSeverity, number>
  topErrors: Array<{ message: string; count: number }>
  errorTrends: Array<{ timestamp: number; count: number }>
}

class ErrorAnalytics {
  private errors: ErrorReport[] = []
  
  addError(error: ErrorReport): void {
    this.errors.push(error)
    
    // 保持最近1000个错误
    if (this.errors.length > 1000) {
      this.errors = this.errors.slice(-1000)
    }
  }
  
  getStats(timeRange: number = 24 * 60 * 60 * 1000): ErrorStats {
    const now = Date.now()
    const recentErrors = this.errors.filter(
      error => now - error.timestamp <= timeRange
    )
    
    const stats: ErrorStats = {
      totalErrors: recentErrors.length,
      errorsByCategory: {} as Record<ErrorCategory, number>,
      errorsBySeverity: {} as Record<ErrorSeverity, number>,
      topErrors: [],
      errorTrends: []
    }
    
    // 按类别统计
    Object.values(ErrorCategory).forEach(category => {
      stats.errorsByCategory[category] = recentErrors.filter(
        error => error.category === category
      ).length
    })
    
    // 按严重程度统计
    Object.values(ErrorSeverity).forEach(severity => {
      stats.errorsBySeverity[severity] = recentErrors.filter(
        error => error.severity === severity
      ).length
    })
    
    // 错误消息统计
    const errorCounts = new Map<string, number>()
    recentErrors.forEach(error => {
      const count = errorCounts.get(error.message) || 0
      errorCounts.set(error.message, count + 1)
    })
    
    stats.topErrors = Array.from(errorCounts.entries())
      .sort((a, b) => b[1] - a[1])
      .slice(0, 10)
      .map(([message, count]) => ({ message, count }))
    
    return stats
  }
}

export const errorAnalytics = new ErrorAnalytics()
```

### 5.2 错误监控面板组件

```vue
<!-- src/components/ErrorMonitorPanel.vue -->
<template>
  <div class="error-monitor-panel">
    <h3>错误监控面板</h3>
    
    <div class="stats-grid">
      <div class="stat-card">
        <h4>总错误数</h4>
        <span class="stat-value">{{ stats.totalErrors }}</span>
      </div>
      
      <div class="stat-card">
        <h4>严重错误</h4>
        <span class="stat-value critical">
          {{ stats.errorsBySeverity.critical || 0 }}
        </span>
      </div>
      
      <div class="stat-card">
        <h4>高优先级错误</h4>
        <span class="stat-value high">
          {{ stats.errorsBySeverity.high || 0 }}
        </span>
      </div>
    </div>
    
    <div class="top-errors">
      <h4>频繁错误</h4>
      <ul>
        <li v-for="error in stats.topErrors" :key="error.message">
          <span class="error-message">{{ error.message }}</span>
          <span class="error-count">{{ error.count }}</span>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { errorAnalytics } from '@/utils/errorAnalytics'

const stats = ref(errorAnalytics.getStats())

onMounted(() => {
  // 每分钟更新统计数据
  setInterval(() => {
    stats.value = errorAnalytics.getStats()
  }, 60000)
})
</script>

<style scoped>
.error-monitor-panel {
  @apply p-4 bg-white rounded-lg shadow;
}

.stats-grid {
  @apply grid grid-cols-3 gap-4 mb-6;
}

.stat-card {
  @apply p-4 bg-gray-50 rounded text-center;
}

.stat-value {
  @apply text-2xl font-bold;
}

.stat-value.critical {
  @apply text-red-600;
}

.stat-value.high {
  @apply text-orange-600;
}

.top-errors ul {
  @apply space-y-2;
}

.top-errors li {
  @apply flex justify-between items-center p-2 bg-gray-50 rounded;
}

.error-message {
  @apply flex-1 truncate;
}

.error-count {
  @apply bg-red-100 text-red-800 px-2 py-1 rounded text-sm;
}
</style>
```

## 6. 最佳实践

### 6.1 错误处理最佳实践

1. **分层错误处理**
   - 全局级别：捕获未处理的错误
   - 页面级别：使用错误边界包装路由组件
   - 组件级别：处理组件特定错误
   - 函数级别：处理具体业务逻辑错误

2. **错误信息规范**
   - 用户友好的错误消息
   - 开发者详细的错误信息
   - 结构化的错误数据

3. **错误恢复策略**
   - 提供重试机制
   - 降级处理方案
   - 用户引导和帮助

### 6.2 日志记录最佳实践

1. **日志级别使用**
   - DEBUG: 调试信息，仅开发环境
   - INFO: 一般信息，记录关键操作
   - WARN: 警告信息，需要关注但不影响功能
   - ERROR: 错误信息，影响功能但不致命
   - FATAL: 致命错误，导致应用崩溃

2. **日志内容规范**
   - 包含足够的上下文信息
   - 避免记录敏感信息
   - 使用结构化格式
   - 添加唯一标识符

3. **性能考虑**
   - 异步发送日志
   - 批量处理日志
   - 控制日志数量
   - 压缩日志数据

### 6.3 错误上报最佳实践

1. **上报策略**
   - 立即上报严重错误
   - 批量上报一般错误
   - 离线缓存机制
   - 重试机制

2. **隐私保护**
   - 过滤敏感信息
   - 数据脱敏处理
   - 用户同意机制
   - 符合隐私法规

3. **监控和告警**
   - 设置错误阈值
   - 实时告警机制
   - 错误趋势分析
   - 自动化响应

## 7. 配置示例

### 7.1 环境配置

```typescript
// src/config/errorConfig.ts
interface ErrorConfig {
  enableErrorReporting: boolean
  logLevel: LogLevel
  maxLogs: number
  reportingEndpoint: string
  enableConsoleLog: boolean
}

export const errorConfig: ErrorConfig = {
  enableErrorReporting: import.meta.env.PROD,
  logLevel: import.meta.env.DEV ? LogLevel.DEBUG : LogLevel.INFO,
  maxLogs: 1000,
  reportingEndpoint: import.meta.env.VITE_ERROR_REPORTING_ENDPOINT || '/api/errors',
  enableConsoleLog: import.meta.env.DEV
}
```

### 7.2 Vite配置集成

```typescript
// vite.config.ts
export default defineConfig({
  define: {
    __ERROR_REPORTING_ENABLED__: JSON.stringify(process.env.NODE_ENV === 'production'),
    __LOG_LEVEL__: JSON.stringify(process.env.NODE_ENV === 'development' ? 'DEBUG' : 'INFO')
  }
})
```

这份错误处理和日志规范文档提供了完整的错误处理体系，包括全局错误处理配置、错误边界使用指南、结构化日志记录标准和自动化错误上报机制，确保应用的稳定性和可维护性。