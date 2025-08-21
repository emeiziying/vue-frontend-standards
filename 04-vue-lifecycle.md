# Vue组件生命周期使用规范

## 概述

本文档定义了Vue 3 Composition API生命周期钩子的使用规范、副作用处理标准和组件清理最佳实践，确保组件的性能和内存安全。

## Composition API生命周期钩子

### 生命周期钩子对照表

| Options API | Composition API | 执行时机 |
|-------------|-----------------|----------|
| beforeCreate | setup() | 组件实例创建之前 |
| created | setup() | 组件实例创建之后 |
| beforeMount | onBeforeMount | 组件挂载之前 |
| mounted | onMounted | 组件挂载之后 |
| beforeUpdate | onBeforeUpdate | 组件更新之前 |
| updated | onUpdated | 组件更新之后 |
| beforeUnmount | onBeforeUnmount | 组件卸载之前 |
| unmounted | onUnmounted | 组件卸载之后 |
| errorCaptured | onErrorCaptured | 捕获子组件错误 |
| activated | onActivated | keep-alive组件激活 |
| deactivated | onDeactivated | keep-alive组件停用 |

### 基本使用规范

```vue
<script setup lang="ts">
import {
  ref,
  onBeforeMount,
  onMounted,
  onBeforeUpdate,
  onUpdated,
  onBeforeUnmount,
  onUnmounted,
  onErrorCaptured
} from 'vue'

// 响应式数据
const isLoading = ref(false)
const data = ref<any[]>([])

// 组件挂载前
onBeforeMount(() => {
  console.log('组件即将挂载')
  // 初始化配置，但不要进行DOM操作
  initializeConfig()
})

// 组件挂载后
onMounted(() => {
  console.log('组件已挂载')
  // DOM已可用，可以进行DOM操作和API调用
  loadInitialData()
  setupEventListeners()
})

// 组件更新前
onBeforeUpdate(() => {
  console.log('组件即将更新')
  // 在DOM更新前访问现有DOM
})

// 组件更新后
onUpdated(() => {
  console.log('组件已更新')
  // DOM已更新，可以进行依赖于DOM的操作
})

// 组件卸载前
onBeforeUnmount(() => {
  console.log('组件即将卸载')
  // 清理工作：移除事件监听器、取消定时器等
  cleanup()
})

// 组件卸载后
onUnmounted(() => {
  console.log('组件已卸载')
  // 最终清理工作
})

// 错误捕获
onErrorCaptured((error, instance, info) => {
  console.error('捕获到错误:', error, info)
  // 错误处理逻辑
  return false // 阻止错误继续传播
})
</script>
```

## 生命周期钩子使用指南

### 1. onMounted - 组件挂载后

**适用场景：**
- DOM操作
- API数据加载
- 第三方库初始化
- 事件监听器设置

```vue
<script setup lang="ts">
import { ref, onMounted } from 'vue'

const chartContainer = ref<HTMLElement>()
const data = ref<any[]>([])

onMounted(async () => {
  // 1. DOM操作
  if (chartContainer.value) {
    initializeChart(chartContainer.value)
  }
  
  // 2. API数据加载
  try {
    const response = await fetchData()
    data.value = response.data
  } catch (error) {
    console.error('数据加载失败:', error)
  }
  
  // 3. 第三方库初始化
  const editor = new Editor({
    element: chartContainer.value,
    config: editorConfig
  })
  
  // 4. 全局事件监听
  window.addEventListener('resize', handleResize)
  document.addEventListener('keydown', handleKeydown)
})

const handleResize = () => {
  // 处理窗口大小变化
}

const handleKeydown = (event: KeyboardEvent) => {
  // 处理键盘事件
}
</script>
```

### 2. onBeforeUnmount - 组件卸载前

**适用场景：**
- 清理定时器
- 移除事件监听器
- 取消网络请求
- 清理第三方库实例

```vue
<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue'

let timer: number | null = null
let abortController: AbortController | null = null
let observer: IntersectionObserver | null = null

onMounted(() => {
  // 设置定时器
  timer = setInterval(() => {
    updateData()
  }, 5000)
  
  // 创建AbortController用于取消请求
  abortController = new AbortController()
  
  // 创建Intersection Observer
  observer = new IntersectionObserver(handleIntersection)
  if (targetElement.value) {
    observer.observe(targetElement.value)
  }
})

onBeforeUnmount(() => {
  // 1. 清理定时器
  if (timer) {
    clearInterval(timer)
    timer = null
  }
  
  // 2. 取消网络请求
  if (abortController) {
    abortController.abort()
    abortController = null
  }
  
  // 3. 清理Observer
  if (observer) {
    observer.disconnect()
    observer = null
  }
  
  // 4. 移除事件监听器
  window.removeEventListener('resize', handleResize)
  document.removeEventListener('keydown', handleKeydown)
  
  // 5. 清理第三方库实例
  if (editorInstance) {
    editorInstance.destroy()
  }
})
</script>
```

### 3. onUpdated - 组件更新后

**适用场景：**
- DOM更新后的操作
- 重新计算布局
- 更新第三方库

```vue
<script setup lang="ts">
import { ref, nextTick, onUpdated } from 'vue'

const listContainer = ref<HTMLElement>()
const items = ref<any[]>([])

onUpdated(async () => {
  // 等待DOM完全更新
  await nextTick()
  
  // 1. 重新计算容器高度
  if (listContainer.value) {
    updateContainerHeight()
  }
  
  // 2. 更新第三方库
  if (chartInstance) {
    chartInstance.update(items.value)
  }
  
  // 3. 滚动到新添加的元素
  scrollToNewItem()
})

const updateContainerHeight = () => {
  if (listContainer.value) {
    const height = listContainer.value.scrollHeight
    listContainer.value.style.height = `${height}px`
  }
}
</script>
```

### 4. onErrorCaptured - 错误捕获

**适用场景：**
- 子组件错误处理
- 错误日志记录
- 错误恢复机制

```vue
<script setup lang="ts">
import { ref, onErrorCaptured } from 'vue'

const hasError = ref(false)
const errorMessage = ref('')

onErrorCaptured((error, instance, info) => {
  // 1. 记录错误信息
  console.error('组件错误:', {
    error: error.message,
    stack: error.stack,
    component: instance?.$options.name,
    info
  })
  
  // 2. 发送错误报告
  reportError({
    message: error.message,
    stack: error.stack,
    component: instance?.$options.name,
    info,
    timestamp: new Date().toISOString()
  })
  
  // 3. 设置错误状态
  hasError.value = true
  errorMessage.value = error.message
  
  // 4. 阻止错误继续传播（可选）
  return false
})

const reportError = async (errorInfo: any) => {
  try {
    await fetch('/api/errors', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(errorInfo)
    })
  } catch (e) {
    console.error('错误报告发送失败:', e)
  }
}
</script>

<template>
  <div v-if="hasError" class="error-boundary">
    <h3>出现了错误</h3>
    <p>{{ errorMessage }}</p>
    <button @click="hasError = false">重试</button>
  </div>
  <div v-else>
    <!-- 正常内容 -->
    <slot />
  </div>
</template>
```

## 副作用处理规范

### 1. 网络请求管理

```vue
<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue'

const data = ref<any[]>([])
const loading = ref(false)
const error = ref<string | null>(null)

// 使用AbortController管理请求
let abortController: AbortController | null = null

const fetchData = async () => {
  // 取消之前的请求
  if (abortController) {
    abortController.abort()
  }
  
  abortController = new AbortController()
  loading.value = true
  error.value = null
  
  try {
    const response = await fetch('/api/data', {
      signal: abortController.signal
    })
    
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}`)
    }
    
    const result = await response.json()
    data.value = result.data
  } catch (err) {
    if (err.name !== 'AbortError') {
      error.value = err.message
      console.error('数据获取失败:', err)
    }
  } finally {
    loading.value = false
  }
}

onMounted(() => {
  fetchData()
})

onBeforeUnmount(() => {
  // 组件卸载时取消请求
  if (abortController) {
    abortController.abort()
  }
})
</script>
```

### 2. 定时器管理

```vue
<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue'

const currentTime = ref(new Date())
const isActive = ref(true)

// 定时器引用
let timeTimer: number | null = null
let dataTimer: number | null = null

// 启动定时器
const startTimers = () => {
  // 时间更新定时器
  timeTimer = setInterval(() => {
    if (isActive.value) {
      currentTime.value = new Date()
    }
  }, 1000)
  
  // 数据刷新定时器
  dataTimer = setInterval(() => {
    if (isActive.value) {
      refreshData()
    }
  }, 30000)
}

// 停止定时器
const stopTimers = () => {
  if (timeTimer) {
    clearInterval(timeTimer)
    timeTimer = null
  }
  
  if (dataTimer) {
    clearInterval(dataTimer)
    dataTimer = null
  }
}

// 暂停/恢复定时器
const toggleTimers = () => {
  isActive.value = !isActive.value
}

onMounted(() => {
  startTimers()
})

onBeforeUnmount(() => {
  stopTimers()
})

// 页面可见性变化时的处理
const handleVisibilityChange = () => {
  if (document.hidden) {
    isActive.value = false
  } else {
    isActive.value = true
  }
}

onMounted(() => {
  document.addEventListener('visibilitychange', handleVisibilityChange)
})

onBeforeUnmount(() => {
  document.removeEventListener('visibilitychange', handleVisibilityChange)
})
</script>
```

### 3. 事件监听器管理

```vue
<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue'

const windowWidth = ref(window.innerWidth)
const scrollY = ref(window.scrollY)
const isOnline = ref(navigator.onLine)

// 事件处理函数
const handleResize = () => {
  windowWidth.value = window.innerWidth
}

const handleScroll = () => {
  scrollY.value = window.scrollY
}

const handleOnline = () => {
  isOnline.value = true
}

const handleOffline = () => {
  isOnline.value = false
}

// 键盘事件处理
const handleKeydown = (event: KeyboardEvent) => {
  if (event.key === 'Escape') {
    // 处理ESC键
    closeModal()
  }
}

onMounted(() => {
  // 添加事件监听器
  window.addEventListener('resize', handleResize)
  window.addEventListener('scroll', handleScroll)
  window.addEventListener('online', handleOnline)
  window.addEventListener('offline', handleOffline)
  document.addEventListener('keydown', handleKeydown)
})

onBeforeUnmount(() => {
  // 移除事件监听器
  window.removeEventListener('resize', handleResize)
  window.removeEventListener('scroll', handleScroll)
  window.removeEventListener('online', handleOnline)
  window.removeEventListener('offline', handleOffline)
  document.removeEventListener('keydown', handleKeydown)
})
</script>
```

### 4. 第三方库集成

```vue
<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount, watch } from 'vue'

const chartContainer = ref<HTMLElement>()
const chartData = ref<any[]>([])

// 第三方库实例
let chartInstance: any = null
let mapInstance: any = null

// 初始化图表
const initializeChart = () => {
  if (!chartContainer.value) return
  
  chartInstance = new Chart(chartContainer.value, {
    type: 'line',
    data: {
      datasets: [{
        data: chartData.value
      }]
    },
    options: {
      responsive: true,
      maintainAspectRatio: false
    }
  })
}

// 更新图表数据
const updateChart = () => {
  if (chartInstance) {
    chartInstance.data.datasets[0].data = chartData.value
    chartInstance.update()
  }
}

// 监听数据变化
watch(chartData, () => {
  updateChart()
}, { deep: true })

onMounted(() => {
  // 初始化第三方库
  initializeChart()
  
  // 初始化地图（如果需要）
  if (window.google) {
    mapInstance = new google.maps.Map(mapContainer.value, {
      zoom: 10,
      center: { lat: 39.9042, lng: 116.4074 }
    })
  }
})

onBeforeUnmount(() => {
  // 清理图表实例
  if (chartInstance) {
    chartInstance.destroy()
    chartInstance = null
  }
  
  // 清理地图实例
  if (mapInstance) {
    // Google Maps没有destroy方法，设置为null即可
    mapInstance = null
  }
})
</script>
```

## 组件清理和内存管理规范

### 1. 内存泄漏预防清单

```vue
<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue'

// 需要清理的资源列表
const cleanupTasks: Array<() => void> = []

// 添加清理任务的辅助函数
const addCleanupTask = (task: () => void) => {
  cleanupTasks.push(task)
}

onMounted(() => {
  // 1. 定时器
  const timer = setInterval(() => {
    // 定时任务
  }, 1000)
  addCleanupTask(() => clearInterval(timer))
  
  // 2. 事件监听器
  const handleClick = () => {}
  document.addEventListener('click', handleClick)
  addCleanupTask(() => document.removeEventListener('click', handleClick))
  
  // 3. 网络请求
  const controller = new AbortController()
  fetch('/api/data', { signal: controller.signal })
  addCleanupTask(() => controller.abort())
  
  // 4. WebSocket连接
  const ws = new WebSocket('ws://localhost:8080')
  addCleanupTask(() => ws.close())
  
  // 5. 第三方库实例
  const instance = new ThirdPartyLib()
  addCleanupTask(() => instance.destroy())
})

onBeforeUnmount(() => {
  // 执行所有清理任务
  cleanupTasks.forEach(task => {
    try {
      task()
    } catch (error) {
      console.error('清理任务执行失败:', error)
    }
  })
  cleanupTasks.length = 0
})
</script>
```

### 2. 响应式数据清理

```vue
<script setup lang="ts">
import { ref, reactive, watch, onBeforeUnmount } from 'vue'

// 大型数据对象
const largeDataSet = ref<any[]>([])
const complexObject = reactive<Record<string, any>>({})

// 监听器停止函数
const stopWatchers: Array<() => void> = []

// 添加监听器
const addWatcher = (watcher: () => void) => {
  const stop = watch(watcher)
  stopWatchers.push(stop)
}

onMounted(() => {
  // 添加各种监听器
  addWatcher(() => largeDataSet.value, (newVal) => {
    // 处理数据变化
  })
  
  addWatcher(() => complexObject.someProperty, (newVal) => {
    // 处理属性变化
  })
})

onBeforeUnmount(() => {
  // 1. 停止所有监听器
  stopWatchers.forEach(stop => stop())
  stopWatchers.length = 0
  
  // 2. 清空大型数据
  largeDataSet.value = []
  
  // 3. 清空复杂对象
  Object.keys(complexObject).forEach(key => {
    delete complexObject[key]
  })
})
</script>
```

### 3. DOM引用清理

```vue
<script setup lang="ts">
import { ref, onBeforeUnmount } from 'vue'

// DOM元素引用
const containerRef = ref<HTMLElement>()
const inputRefs = ref<HTMLInputElement[]>([])

// 存储DOM相关的清理函数
const domCleanupTasks: Array<() => void> = []

onMounted(() => {
  // 为DOM元素添加事件监听器
  if (containerRef.value) {
    const handleContainerClick = (event: Event) => {
      // 处理点击事件
    }
    
    containerRef.value.addEventListener('click', handleContainerClick)
    domCleanupTasks.push(() => {
      containerRef.value?.removeEventListener('click', handleContainerClick)
    })
  }
  
  // 为多个输入框添加事件监听器
  inputRefs.value.forEach((input, index) => {
    const handleInput = (event: Event) => {
      // 处理输入事件
    }
    
    input.addEventListener('input', handleInput)
    domCleanupTasks.push(() => {
      input.removeEventListener('input', handleInput)
    })
  })
})

onBeforeUnmount(() => {
  // 执行DOM清理任务
  domCleanupTasks.forEach(task => task())
  domCleanupTasks.length = 0
  
  // 清空DOM引用
  containerRef.value = undefined
  inputRefs.value = []
})
</script>
```

## 异步操作最佳实践

### 1. 异步数据加载

```vue
<script setup lang="ts">
import { ref, onMounted } from 'vue'

const data = ref<any[]>([])
const loading = ref(false)
const error = ref<string | null>(null)

// 组件是否已卸载的标志
let isMounted = true

const loadData = async () => {
  loading.value = true
  error.value = null
  
  try {
    const response = await fetch('/api/data')
    const result = await response.json()
    
    // 检查组件是否仍然挂载
    if (isMounted) {
      data.value = result.data
    }
  } catch (err) {
    if (isMounted) {
      error.value = err.message
    }
  } finally {
    if (isMounted) {
      loading.value = false
    }
  }
}

onMounted(() => {
  loadData()
})

onBeforeUnmount(() => {
  isMounted = false
})
</script>
```

### 2. 防抖和节流

```vue
<script setup lang="ts">
import { ref, onBeforeUnmount } from 'vue'

const searchQuery = ref('')
const searchResults = ref<any[]>([])

// 防抖定时器
let debounceTimer: number | null = null

// 防抖搜索函数
const debouncedSearch = (query: string) => {
  if (debounceTimer) {
    clearTimeout(debounceTimer)
  }
  
  debounceTimer = setTimeout(async () => {
    if (query.trim()) {
      const results = await searchAPI(query)
      searchResults.value = results
    } else {
      searchResults.value = []
    }
  }, 300)
}

// 节流滚动处理
let throttleTimer: number | null = null
const throttledScroll = () => {
  if (throttleTimer) return
  
  throttleTimer = setTimeout(() => {
    handleScroll()
    throttleTimer = null
  }, 100)
}

onMounted(() => {
  window.addEventListener('scroll', throttledScroll)
})

onBeforeUnmount(() => {
  // 清理定时器
  if (debounceTimer) {
    clearTimeout(debounceTimer)
  }
  if (throttleTimer) {
    clearTimeout(throttleTimer)
  }
  
  // 移除事件监听器
  window.removeEventListener('scroll', throttledScroll)
})
</script>
```

## Keep-Alive组件处理

### 1. 缓存组件生命周期

```vue
<script setup lang="ts">
import { ref, onActivated, onDeactivated, onMounted, onBeforeUnmount } from 'vue'

const data = ref<any[]>([])
const timer = ref<number | null>(null)

// 启动定时器
const startTimer = () => {
  timer.value = setInterval(() => {
    refreshData()
  }, 5000)
}

// 停止定时器
const stopTimer = () => {
  if (timer.value) {
    clearInterval(timer.value)
    timer.value = null
  }
}

// 组件首次挂载
onMounted(() => {
  console.log('组件首次挂载')
  loadInitialData()
  startTimer()
})

// 组件激活（从缓存中恢复）
onActivated(() => {
  console.log('组件被激活')
  // 重新启动定时器
  startTimer()
  // 刷新数据
  refreshData()
})

// 组件停用（被缓存）
onDeactivated(() => {
  console.log('组件被停用')
  // 停止定时器以节省资源
  stopTimer()
})

// 组件完全卸载
onBeforeUnmount(() => {
  console.log('组件完全卸载')
  stopTimer()
})
</script>
```

### 2. 缓存状态管理

```vue
<script setup lang="ts">
import { ref, onActivated, onDeactivated } from 'vue'

const scrollPosition = ref(0)
const formData = ref({
  name: '',
  email: ''
})

// 保存状态
const saveState = () => {
  scrollPosition.value = window.scrollY
  // 可以将状态保存到sessionStorage
  sessionStorage.setItem('componentState', JSON.stringify({
    scrollPosition: scrollPosition.value,
    formData: formData.value
  }))
}

// 恢复状态
const restoreState = () => {
  const savedState = sessionStorage.getItem('componentState')
  if (savedState) {
    const state = JSON.parse(savedState)
    scrollPosition.value = state.scrollPosition
    formData.value = state.formData
    
    // 恢复滚动位置
    nextTick(() => {
      window.scrollTo(0, scrollPosition.value)
    })
  }
}

onActivated(() => {
  restoreState()
})

onDeactivated(() => {
  saveState()
})
</script>
```

## 错误处理和调试

### 1. 生命周期错误处理

```vue
<script setup lang="ts">
import { ref, onMounted, onErrorCaptured } from 'vue'

const hasError = ref(false)
const errorInfo = ref<any>(null)

// 安全的异步操作包装器
const safeAsync = async (operation: () => Promise<void>, errorMessage: string) => {
  try {
    await operation()
  } catch (error) {
    console.error(errorMessage, error)
    hasError.value = true
    errorInfo.value = { message: errorMessage, error }
  }
}

onMounted(async () => {
  await safeAsync(async () => {
    // 可能失败的初始化操作
    await initializeComponent()
  }, '组件初始化失败')
  
  await safeAsync(async () => {
    // 可能失败的数据加载
    await loadData()
  }, '数据加载失败')
})

// 捕获子组件错误
onErrorCaptured((error, instance, info) => {
  console.error('子组件错误:', { error, instance, info })
  
  // 记录错误信息
  errorInfo.value = {
    message: error.message,
    component: instance?.$options.name,
    info
  }
  
  hasError.value = true
  
  // 阻止错误继续传播
  return false
})
</script>

<template>
  <div v-if="hasError" class="error-state">
    <h3>组件出现错误</h3>
    <p>{{ errorInfo?.message }}</p>
    <button @click="hasError = false">重试</button>
  </div>
  <div v-else>
    <!-- 正常内容 -->
  </div>
</template>
```

### 2. 开发环境调试

```vue
<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount, watch } from 'vue'

const componentName = 'MyComponent'
const debugMode = process.env.NODE_ENV === 'development'

// 调试日志函数
const debugLog = (message: string, data?: any) => {
  if (debugMode) {
    console.log(`[${componentName}] ${message}`, data)
  }
}

// 性能监控
const performanceMarks: string[] = []

const markPerformance = (label: string) => {
  if (debugMode) {
    const mark = `${componentName}-${label}`
    performance.mark(mark)
    performanceMarks.push(mark)
    debugLog(`Performance mark: ${label}`)
  }
}

onMounted(() => {
  markPerformance('mounted-start')
  debugLog('组件已挂载')
  
  // 模拟异步操作
  setTimeout(() => {
    markPerformance('async-operation-complete')
    debugLog('异步操作完成')
  }, 1000)
})

onBeforeUnmount(() => {
  debugLog('组件即将卸载')
  
  // 输出性能信息
  if (debugMode && performanceMarks.length > 1) {
    const startMark = performanceMarks[0]
    const endMark = performanceMarks[performanceMarks.length - 1]
    
    try {
      performance.measure(`${componentName}-total`, startMark, endMark)
      const measure = performance.getEntriesByName(`${componentName}-total`)[0]
      debugLog(`组件总耗时: ${measure.duration.toFixed(2)}ms`)
    } catch (error) {
      debugLog('性能测量失败', error)
    }
  }
  
  // 清理性能标记
  performanceMarks.forEach(mark => {
    try {
      performance.clearMarks(mark)
    } catch (error) {
      // 忽略清理错误
    }
  })
})

// 监听响应式数据变化（仅开发环境）
if (debugMode) {
  watch(() => someReactiveData.value, (newVal, oldVal) => {
    debugLog('数据变化', { from: oldVal, to: newVal })
  })
}
</script>
```

## 总结

遵循这些生命周期使用规范可以确保：

1. **资源管理**：正确清理定时器、事件监听器和第三方库实例
2. **内存安全**：避免内存泄漏和不必要的资源占用
3. **性能优化**：合理使用生命周期钩子，避免不必要的操作
4. **错误处理**：完善的错误捕获和恢复机制
5. **调试友好**：提供充分的调试信息和性能监控

在实际开发中，应该根据组件的具体需求选择合适的生命周期钩子，并始终记住在组件卸载时进行必要的清理工作。