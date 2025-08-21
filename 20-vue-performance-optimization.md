# Vue应用性能优化指南

## 概述

本文档提供Vue应用性能优化的最佳实践，包括组件懒加载、虚拟滚动、内存泄漏预防等关键优化策略。

## 组件懒加载最佳实践

### 路由级别懒加载

使用动态导入实现路由组件的懒加载：

```typescript
// router/index.ts
import { createRouter, createWebHistory } from 'vue-router'

const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: '/',
      name: 'Home',
      component: () => import('@/views/HomeView.vue')
    },
    {
      path: '/about',
      name: 'About',
      // 路由级别的代码分割
      // 这会为该路由生成一个单独的chunk文件
      component: () => import('@/views/AboutView.vue')
    },
    {
      path: '/dashboard',
      name: 'Dashboard',
      component: () => import('@/views/DashboardView.vue'),
      children: [
        {
          path: 'analytics',
          component: () => import('@/views/dashboard/AnalyticsView.vue')
        }
      ]
    }
  ]
})
```

### 组件级别懒加载

#### 异步组件定义

```vue
<template>
  <div>
    <h1>主页面</h1>
    <!-- 条件渲染重型组件 -->
    <HeavyComponent v-if="showHeavyComponent" />
    <button @click="loadHeavyComponent">加载重型组件</button>
  </div>
</template>

<script setup lang="ts">
import { ref, defineAsyncComponent } from 'vue'

// 异步组件定义
const HeavyComponent = defineAsyncComponent({
  loader: () => import('@/components/HeavyComponent.vue'),
  // 加载时显示的组件
  loadingComponent: () => import('@/components/LoadingSpinner.vue'),
  // 加载失败时显示的组件
  errorComponent: () => import('@/components/ErrorMessage.vue'),
  // 延迟显示加载组件的时间（毫秒）
  delay: 200,
  // 超时时间（毫秒）
  timeout: 3000
})

const showHeavyComponent = ref(false)

const loadHeavyComponent = () => {
  showHeavyComponent.value = true
}
</script>
```

#### 条件懒加载

```vue
<template>
  <div>
    <!-- 基于用户权限的懒加载 -->
    <AdminPanel v-if="isAdmin && showAdminPanel" />
    
    <!-- 基于设备类型的懒加载 -->
    <MobileOptimizedComponent v-if="isMobile" />
    <DesktopComponent v-else />
  </div>
</template>

<script setup lang="ts">
import { ref, computed, defineAsyncComponent } from 'vue'
import { useUserStore } from '@/stores/user'
import { useDeviceStore } from '@/stores/device'

const userStore = useUserStore()
const deviceStore = useDeviceStore()

const isAdmin = computed(() => userStore.hasRole('admin'))
const isMobile = computed(() => deviceStore.isMobile)

// 管理员面板懒加载
const AdminPanel = defineAsyncComponent(() => 
  import('@/components/admin/AdminPanel.vue')
)

// 移动端优化组件
const MobileOptimizedComponent = defineAsyncComponent(() =>
  import('@/components/mobile/MobileOptimizedComponent.vue')
)

// 桌面端组件
const DesktopComponent = defineAsyncComponent(() =>
  import('@/components/desktop/DesktopComponent.vue')
)

const showAdminPanel = ref(false)
</script>
```

### 第三方库懒加载

```typescript
// utils/lazyLibraries.ts

// 图表库懒加载
export const loadChartLibrary = async () => {
  const { default: Chart } = await import('chart.js/auto')
  return Chart
}

// 日期处理库懒加载
export const loadDateLibrary = async () => {
  const dayjs = await import('dayjs')
  return dayjs.default
}

// 富文本编辑器懒加载
export const loadRichTextEditor = async () => {
  const { default: Quill } = await import('quill')
  return Quill
}

// 使用示例
// components/ChartComponent.vue
<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { loadChartLibrary } from '@/utils/lazyLibraries'

const chartRef = ref<HTMLCanvasElement>()
const chartInstance = ref()

onMounted(async () => {
  try {
    const Chart = await loadChartLibrary()
    chartInstance.value = new Chart(chartRef.value!, {
      type: 'bar',
      data: {
        // 图表数据
      }
    })
  } catch (error) {
    console.error('Failed to load chart library:', error)
  }
})
</script>
```

## 虚拟滚动使用规范

### 基础虚拟滚动实现

```vue
<template>
  <div class="virtual-scroll-container" ref="containerRef">
    <div 
      class="virtual-scroll-content"
      :style="{ height: totalHeight + 'px' }"
    >
      <div
        class="virtual-scroll-viewport"
        :style="{ transform: `translateY(${offsetY}px)` }"
      >
        <div
          v-for="item in visibleItems"
          :key="item.id"
          class="virtual-scroll-item"
          :style="{ height: itemHeight + 'px' }"
        >
          <slot :item="item" :index="item.index">
            {{ item.content }}
          </slot>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue'

interface VirtualScrollItem {
  id: string | number
  content: any
  index: number
}

interface Props {
  items: any[]
  itemHeight: number
  containerHeight: number
  overscan?: number
}

const props = withDefaults(defineProps<Props>(), {
  overscan: 5
})

const containerRef = ref<HTMLElement>()
const scrollTop = ref(0)

// 计算总高度
const totalHeight = computed(() => props.items.length * props.itemHeight)

// 计算可见区域的起始和结束索引
const startIndex = computed(() => {
  const index = Math.floor(scrollTop.value / props.itemHeight)
  return Math.max(0, index - props.overscan)
})

const endIndex = computed(() => {
  const index = Math.ceil((scrollTop.value + props.containerHeight) / props.itemHeight)
  return Math.min(props.items.length - 1, index + props.overscan)
})

// 计算可见项目
const visibleItems = computed(() => {
  const items: VirtualScrollItem[] = []
  for (let i = startIndex.value; i <= endIndex.value; i++) {
    items.push({
      id: props.items[i]?.id || i,
      content: props.items[i],
      index: i
    })
  }
  return items
})

// 计算偏移量
const offsetY = computed(() => startIndex.value * props.itemHeight)

// 滚动事件处理
const handleScroll = (event: Event) => {
  const target = event.target as HTMLElement
  scrollTop.value = target.scrollTop
}

onMounted(() => {
  containerRef.value?.addEventListener('scroll', handleScroll)
})

onUnmounted(() => {
  containerRef.value?.removeEventListener('scroll', handleScroll)
})
</script>

<style scoped>
.virtual-scroll-container {
  overflow-y: auto;
  position: relative;
}

.virtual-scroll-content {
  position: relative;
}

.virtual-scroll-viewport {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
}

.virtual-scroll-item {
  display: flex;
  align-items: center;
  padding: 8px 16px;
  border-bottom: 1px solid #eee;
}
</style>
```

### 动态高度虚拟滚动

```vue
<template>
  <div class="dynamic-virtual-scroll" ref="containerRef">
    <div :style="{ height: totalHeight + 'px', position: 'relative' }">
      <div
        v-for="item in visibleItems"
        :key="item.id"
        :ref="el => setItemRef(el, item.index)"
        class="virtual-item"
        :style="{
          position: 'absolute',
          top: item.top + 'px',
          width: '100%'
        }"
      >
        <slot :item="item.data" :index="item.index">
          {{ item.data }}
        </slot>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted, nextTick } from 'vue'

interface Props {
  items: any[]
  estimatedItemHeight: number
  containerHeight: number
}

const props = defineProps<Props>()

const containerRef = ref<HTMLElement>()
const scrollTop = ref(0)
const itemHeights = ref<number[]>([])
const itemPositions = ref<number[]>([])

// 初始化项目高度和位置
const initializeItems = () => {
  itemHeights.value = new Array(props.items.length).fill(props.estimatedItemHeight)
  itemPositions.value = new Array(props.items.length)
  
  let position = 0
  for (let i = 0; i < props.items.length; i++) {
    itemPositions.value[i] = position
    position += itemHeights.value[i]
  }
}

// 更新项目高度
const updateItemHeight = (index: number, height: number) => {
  if (itemHeights.value[index] !== height) {
    const diff = height - itemHeights.value[index]
    itemHeights.value[index] = height
    
    // 更新后续项目的位置
    for (let i = index + 1; i < itemPositions.value.length; i++) {
      itemPositions.value[i] += diff
    }
  }
}

// 设置项目引用并测量高度
const setItemRef = (el: Element | null, index: number) => {
  if (el) {
    nextTick(() => {
      const height = el.getBoundingClientRect().height
      updateItemHeight(index, height)
    })
  }
}

// 计算总高度
const totalHeight = computed(() => {
  return itemPositions.value[itemPositions.value.length - 1] + 
         itemHeights.value[itemHeights.value.length - 1]
})

// 查找可见项目
const visibleItems = computed(() => {
  const items = []
  const viewportTop = scrollTop.value
  const viewportBottom = scrollTop.value + props.containerHeight
  
  for (let i = 0; i < props.items.length; i++) {
    const itemTop = itemPositions.value[i]
    const itemBottom = itemTop + itemHeights.value[i]
    
    if (itemBottom >= viewportTop && itemTop <= viewportBottom) {
      items.push({
        id: props.items[i]?.id || i,
        data: props.items[i],
        index: i,
        top: itemTop
      })
    }
  }
  
  return items
})

const handleScroll = (event: Event) => {
  const target = event.target as HTMLElement
  scrollTop.value = target.scrollTop
}

onMounted(() => {
  initializeItems()
  containerRef.value?.addEventListener('scroll', handleScroll)
})

onUnmounted(() => {
  containerRef.value?.removeEventListener('scroll', handleScroll)
})
</script>
```

### 第三方虚拟滚动库推荐

```typescript
// 推荐使用 @tanstack/vue-virtual 进行复杂虚拟滚动

// 安装: npm install @tanstack/vue-virtual

// 使用示例
import { useVirtualizer } from '@tanstack/vue-virtual'

export default defineComponent({
  setup() {
    const parentRef = ref<HTMLElement>()
    
    const virtualizer = useVirtualizer({
      count: 10000,
      getScrollElement: () => parentRef.value,
      estimateSize: () => 35,
      overscan: 5,
    })
    
    return {
      parentRef,
      virtualizer
    }
  }
})
```

## 内存泄漏预防指南

### 事件监听器清理

```vue
<script setup lang="ts">
import { onMounted, onUnmounted } from 'vue'

// ❌ 错误示例 - 未清理事件监听器
const handleResize = () => {
  // 处理窗口大小变化
}

onMounted(() => {
  window.addEventListener('resize', handleResize)
  // 忘记在组件卸载时移除监听器
})

// ✅ 正确示例 - 清理事件监听器
onMounted(() => {
  window.addEventListener('resize', handleResize)
})

onUnmounted(() => {
  window.removeEventListener('resize', handleResize)
})

// ✅ 更好的方式 - 使用 AbortController
const controller = new AbortController()

onMounted(() => {
  window.addEventListener('resize', handleResize, {
    signal: controller.signal
  })
})

onUnmounted(() => {
  controller.abort() // 自动清理所有相关的事件监听器
})
</script>
```

### 定时器清理

```vue
<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

const timer = ref<number>()
const intervalTimer = ref<number>()

// ❌ 错误示例 - 未清理定时器
onMounted(() => {
  setTimeout(() => {
    // 一些操作
  }, 5000)
  
  setInterval(() => {
    // 定期操作
  }, 1000)
})

// ✅ 正确示例 - 清理定时器
onMounted(() => {
  timer.value = setTimeout(() => {
    // 一些操作
  }, 5000)
  
  intervalTimer.value = setInterval(() => {
    // 定期操作
  }, 1000)
})

onUnmounted(() => {
  if (timer.value) {
    clearTimeout(timer.value)
  }
  
  if (intervalTimer.value) {
    clearInterval(intervalTimer.value)
  }
})
</script>
```

### 异步操作取消

```vue
<script setup lang="ts">
import { ref, onUnmounted } from 'vue'

// ✅ 使用 AbortController 取消 fetch 请求
const abortController = ref<AbortController>()

const fetchData = async () => {
  abortController.value = new AbortController()
  
  try {
    const response = await fetch('/api/data', {
      signal: abortController.value.signal
    })
    const data = await response.json()
    return data
  } catch (error) {
    if (error.name === 'AbortError') {
      console.log('Request was cancelled')
    } else {
      console.error('Request failed:', error)
    }
  }
}

onUnmounted(() => {
  abortController.value?.abort()
})

// ✅ Promise 取消模式
const createCancellablePromise = <T>(promise: Promise<T>) => {
  let cancelled = false
  
  const cancellablePromise = promise.then(
    value => cancelled ? Promise.reject(new Error('Cancelled')) : value,
    error => cancelled ? Promise.reject(new Error('Cancelled')) : Promise.reject(error)
  )
  
  return {
    promise: cancellablePromise,
    cancel: () => { cancelled = true }
  }
}

const cancellableRequest = ref<{ cancel: () => void }>()

const loadData = () => {
  const { promise, cancel } = createCancellablePromise(
    fetch('/api/data').then(res => res.json())
  )
  
  cancellableRequest.value = { cancel }
  
  promise.then(data => {
    // 处理数据
  }).catch(error => {
    if (error.message !== 'Cancelled') {
      console.error('Request failed:', error)
    }
  })
}

onUnmounted(() => {
  cancellableRequest.value?.cancel()
})
</script>
```

### 观察者模式清理

```vue
<script setup lang="ts">
import { onMounted, onUnmounted } from 'vue'

// ✅ IntersectionObserver 清理
const observer = ref<IntersectionObserver>()

onMounted(() => {
  observer.value = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        // 处理可见性变化
      }
    })
  })
  
  const target = document.querySelector('.observe-target')
  if (target) {
    observer.value.observe(target)
  }
})

onUnmounted(() => {
  observer.value?.disconnect()
})

// ✅ MutationObserver 清理
const mutationObserver = ref<MutationObserver>()

onMounted(() => {
  mutationObserver.value = new MutationObserver((mutations) => {
    mutations.forEach(mutation => {
      // 处理DOM变化
    })
  })
  
  mutationObserver.value.observe(document.body, {
    childList: true,
    subtree: true
  })
})

onUnmounted(() => {
  mutationObserver.value?.disconnect()
})
</script>
```

### 第三方库清理

```vue
<script setup lang="ts">
import { onMounted, onUnmounted } from 'vue'

// ✅ 图表库清理示例
const chartInstance = ref()

onMounted(async () => {
  const Chart = await import('chart.js/auto')
  const canvas = document.getElementById('myChart') as HTMLCanvasElement
  
  chartInstance.value = new Chart.default(canvas, {
    type: 'bar',
    data: {
      // 图表数据
    }
  })
})

onUnmounted(() => {
  // 销毁图表实例
  chartInstance.value?.destroy()
})

// ✅ 地图库清理示例
const mapInstance = ref()

onMounted(async () => {
  const L = await import('leaflet')
  
  mapInstance.value = L.map('map').setView([51.505, -0.09], 13)
  
  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(mapInstance.value)
})

onUnmounted(() => {
  // 移除地图实例
  mapInstance.value?.remove()
})
</script>
```

### 内存泄漏检测工具

```typescript
// utils/memoryMonitor.ts

export class MemoryMonitor {
  private static instance: MemoryMonitor
  private observers: Set<() => void> = new Set()
  
  static getInstance(): MemoryMonitor {
    if (!MemoryMonitor.instance) {
      MemoryMonitor.instance = new MemoryMonitor()
    }
    return MemoryMonitor.instance
  }
  
  // 监控内存使用情况
  startMonitoring(interval = 5000) {
    const monitor = () => {
      if ('memory' in performance) {
        const memory = (performance as any).memory
        console.log('Memory Usage:', {
          used: Math.round(memory.usedJSHeapSize / 1048576) + ' MB',
          total: Math.round(memory.totalJSHeapSize / 1048576) + ' MB',
          limit: Math.round(memory.jsHeapSizeLimit / 1048576) + ' MB'
        })
      }
    }
    
    const intervalId = setInterval(monitor, interval)
    
    return () => clearInterval(intervalId)
  }
  
  // 检测潜在的内存泄漏
  detectLeaks() {
    const initialMemory = (performance as any).memory?.usedJSHeapSize
    
    return {
      check: () => {
        const currentMemory = (performance as any).memory?.usedJSHeapSize
        const diff = currentMemory - initialMemory
        
        if (diff > 10 * 1024 * 1024) { // 10MB 增长
          console.warn('Potential memory leak detected:', {
            initial: Math.round(initialMemory / 1048576) + ' MB',
            current: Math.round(currentMemory / 1048576) + ' MB',
            diff: Math.round(diff / 1048576) + ' MB'
          })
        }
      }
    }
  }
}

// 在开发环境中使用
if (import.meta.env.DEV) {
  const monitor = MemoryMonitor.getInstance()
  monitor.startMonitoring()
}
```

## 性能监控和分析

### 组件性能分析

```vue
<script setup lang="ts">
import { ref, onMounted, onUpdated } from 'vue'

// 性能标记工具
const usePerformanceMarker = (componentName: string) => {
  const markStart = (operation: string) => {
    performance.mark(`${componentName}-${operation}-start`)
  }
  
  const markEnd = (operation: string) => {
    performance.mark(`${componentName}-${operation}-end`)
    performance.measure(
      `${componentName}-${operation}`,
      `${componentName}-${operation}-start`,
      `${componentName}-${operation}-end`
    )
  }
  
  return { markStart, markEnd }
}

const { markStart, markEnd } = usePerformanceMarker('MyComponent')

onMounted(() => {
  markStart('mount')
  // 组件挂载逻辑
  markEnd('mount')
})

onUpdated(() => {
  markStart('update')
  // 组件更新逻辑
  markEnd('update')
})
</script>
```

### 渲染性能优化

```vue
<template>
  <div>
    <!-- 使用 v-memo 优化列表渲染 -->
    <div
      v-for="item in expensiveList"
      :key="item.id"
      v-memo="[item.id, item.selected]"
    >
      <ExpensiveComponent :item="item" />
    </div>
    
    <!-- 使用 v-once 优化静态内容 -->
    <div v-once>
      <h1>{{ title }}</h1>
      <p>{{ description }}</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, shallowRef } from 'vue'

// 使用 shallowRef 优化大型对象
const largeDataSet = shallowRef({
  // 大量数据
})

// 使用 computed 缓存昂贵的计算
const expensiveComputation = computed(() => {
  // 昂贵的计算逻辑
  return largeDataSet.value.items?.filter(item => item.active)
})
</script>
```

## 最佳实践总结

### 性能优化检查清单

- [ ] 实现路由级别的代码分割
- [ ] 对重型组件使用异步加载
- [ ] 为长列表实现虚拟滚动
- [ ] 清理所有事件监听器
- [ ] 取消未完成的异步操作
- [ ] 销毁第三方库实例
- [ ] 使用性能监控工具
- [ ] 优化组件渲染性能
- [ ] 实现适当的缓存策略
- [ ] 监控内存使用情况

### 性能测试建议

1. **使用 Lighthouse 进行性能评估**
2. **在 Chrome DevTools 中分析内存使用**
3. **使用 Vue DevTools 监控组件性能**
4. **实施持续的性能监控**
5. **定期进行性能回归测试**