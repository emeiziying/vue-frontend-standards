# 构建优化规范

## 概述

本文档定义了Vue项目构建优化的标准和最佳实践，包括代码分割策略、资源压缩配置和缓存策略，以提升应用的加载性能和用户体验。

## 代码分割策略指南

### 路由级代码分割

#### 基础路由分割配置

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
      path: '/dashboard',
      name: 'Dashboard',
      component: () => import('@/views/DashboardView.vue'),
      children: [
        {
          path: 'analytics',
          name: 'Analytics',
          component: () => import('@/views/dashboard/AnalyticsView.vue')
        },
        {
          path: 'reports',
          name: 'Reports',
          component: () => import('@/views/dashboard/ReportsView.vue')
        }
      ]
    },
    {
      path: '/admin',
      name: 'Admin',
      component: () => import('@/views/AdminView.vue'),
      meta: { requiresAuth: true, role: 'admin' }
    }
  ]
})

export default router
```

#### 命名 Chunk 分割

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [vue()],
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          // 将 Vue 相关库分离到单独的 chunk
          'vue-vendor': ['vue', 'vue-router', 'pinia'],
          
          // 将 UI 组件库分离
          'ui-vendor': ['element-plus', '@element-plus/icons-vue'],
          
          // 将工具库分离
          'utils-vendor': ['lodash-es', 'dayjs', 'axios'],
          
          // 将图表库分离
          'chart-vendor': ['chart.js', 'echarts'],
          
          // 按功能模块分离
          'admin': [
            './src/views/admin/AdminDashboard.vue',
            './src/views/admin/UserManagement.vue',
            './src/views/admin/SystemSettings.vue'
          ],
          
          'dashboard': [
            './src/views/dashboard/Analytics.vue',
            './src/views/dashboard/Reports.vue'
          ]
        },
        
        // 自定义 chunk 文件名
        chunkFileNames: (chunkInfo) => {
          const facadeModuleId = chunkInfo.facadeModuleId
          if (facadeModuleId) {
            if (facadeModuleId.includes('views/admin')) {
              return 'js/admin-[name]-[hash].js'
            }
            if (facadeModuleId.includes('views/dashboard')) {
              return 'js/dashboard-[name]-[hash].js'
            }
          }
          return 'js/[name]-[hash].js'
        }
      }
    }
  }
})
```

### 动态导入优化

#### 条件性代码分割

```typescript
// utils/dynamicImports.ts

// 基于用户权限的动态导入
export const loadAdminModule = async (userRole: string) => {
  if (userRole === 'admin') {
    return await import('@/modules/admin')
  }
  throw new Error('Insufficient permissions')
}

// 基于功能特性的动态导入
export const loadFeatureModule = async (featureName: string) => {
  const modules = {
    'advanced-charts': () => import('@/modules/advanced-charts'),
    'data-export': () => import('@/modules/data-export'),
    'real-time-notifications': () => import('@/modules/notifications'),
    'file-upload': () => import('@/modules/file-upload')
  }
  
  const moduleLoader = modules[featureName as keyof typeof modules]
  if (!moduleLoader) {
    throw new Error(`Feature module '${featureName}' not found`)
  }
  
  return await moduleLoader()
}

// 基于设备类型的动态导入
export const loadDeviceSpecificModule = async () => {
  const isMobile = window.innerWidth < 768
  
  if (isMobile) {
    return await import('@/modules/mobile-optimized')
  } else {
    return await import('@/modules/desktop-enhanced')
  }
}
```

#### 预加载策略

```typescript
// utils/preloadStrategy.ts

export class PreloadManager {
  private preloadedModules = new Set<string>()
  
  // 预加载关键路由
  async preloadCriticalRoutes() {
    const criticalRoutes = [
      () => import('@/views/DashboardView.vue'),
      () => import('@/views/ProfileView.vue')
    ]
    
    // 在空闲时间预加载
    if ('requestIdleCallback' in window) {
      window.requestIdleCallback(() => {
        criticalRoutes.forEach(loader => {
          loader().catch(console.error)
        })
      })
    }
  }
  
  // 基于用户行为预加载
  async preloadOnHover(routeName: string) {
    if (this.preloadedModules.has(routeName)) {
      return
    }
    
    this.preloadedModules.add(routeName)
    
    const routeModules = {
      'admin': () => import('@/views/AdminView.vue'),
      'settings': () => import('@/views/SettingsView.vue'),
      'reports': () => import('@/views/ReportsView.vue')
    }
    
    const loader = routeModules[routeName as keyof typeof routeModules]
    if (loader) {
      try {
        await loader()
        console.log(`Preloaded module: ${routeName}`)
      } catch (error) {
        console.error(`Failed to preload module: ${routeName}`, error)
      }
    }
  }
  
  // 智能预加载
  async intelligentPreload() {
    // 基于用户历史行为预加载
    const userHistory = this.getUserNavigationHistory()
    const predictedRoutes = this.predictNextRoutes(userHistory)
    
    predictedRoutes.forEach(route => {
      this.preloadOnHover(route)
    })
  }
  
  private getUserNavigationHistory(): string[] {
    // 从 localStorage 或分析服务获取用户导航历史
    return JSON.parse(localStorage.getItem('navigationHistory') || '[]')
  }
  
  private predictNextRoutes(history: string[]): string[] {
    // 简单的预测算法，可以根据需要实现更复杂的机器学习模型
    const routeFrequency = history.reduce((acc, route) => {
      acc[route] = (acc[route] || 0) + 1
      return acc
    }, {} as Record<string, number>)
    
    return Object.entries(routeFrequency)
      .sort(([, a], [, b]) => b - a)
      .slice(0, 3)
      .map(([route]) => route)
  }
}

// 使用示例
const preloadManager = new PreloadManager()

// 在应用启动时预加载关键路由
preloadManager.preloadCriticalRoutes()

// 在导航链接上添加预加载
export const usePreloadOnHover = () => {
  const handleMouseEnter = (routeName: string) => {
    preloadManager.preloadOnHover(routeName)
  }
  
  return { handleMouseEnter }
}
```

### 第三方库分割策略

```typescript
// vite.config.ts - 高级分割配置
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks: (id) => {
          // Node modules 分割
          if (id.includes('node_modules')) {
            // 大型库单独分割
            if (id.includes('echarts')) {
              return 'echarts'
            }
            if (id.includes('monaco-editor')) {
              return 'monaco-editor'
            }
            if (id.includes('three')) {
              return 'three'
            }
            
            // UI 框架分割
            if (id.includes('element-plus')) {
              return 'element-plus'
            }
            if (id.includes('ant-design-vue')) {
              return 'antd'
            }
            
            // 工具库分组
            if (id.includes('lodash') || id.includes('ramda')) {
              return 'utils'
            }
            if (id.includes('dayjs') || id.includes('moment')) {
              return 'date-utils'
            }
            if (id.includes('axios') || id.includes('ky')) {
              return 'http-utils'
            }
            
            // 其他第三方库
            return 'vendor'
          }
          
          // 业务模块分割
          if (id.includes('/src/modules/')) {
            const moduleName = id.split('/modules/')[1].split('/')[0]
            return `module-${moduleName}`
          }
          
          // 组件库分割
          if (id.includes('/src/components/')) {
            return 'components'
          }
        }
      }
    }
  }
})
```

## 资源压缩配置标准

### JavaScript 和 CSS 压缩

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import { resolve } from 'path'

export default defineConfig({
  plugins: [vue()],
  
  build: {
    // 启用/禁用 minification
    minify: 'terser',
    
    // Terser 配置
    terserOptions: {
      compress: {
        // 移除 console.log
        drop_console: true,
        // 移除 debugger
        drop_debugger: true,
        // 移除未使用的代码
        dead_code: true,
        // 压缩条件表达式
        conditionals: true,
        // 压缩布尔值
        booleans: true,
        // 压缩循环
        loops: true,
        // 移除未使用的函数参数
        unused: true,
        // 压缩对象属性访问
        properties: true
      },
      mangle: {
        // 混淆变量名
        toplevel: true,
        // 保留类名
        keep_classnames: true,
        // 保留函数名（用于调试）
        keep_fnames: false
      },
      format: {
        // 移除注释
        comments: false
      }
    },
    
    // CSS 压缩配置
    cssCodeSplit: true,
    
    // 资源内联阈值
    assetsInlineLimit: 4096,
    
    // 输出目录清理
    emptyOutDir: true,
    
    // 构建目标
    target: 'es2015',
    
    // 源码映射
    sourcemap: false, // 生产环境关闭
    
    // 报告压缩详情
    reportCompressedSize: true
  },
  
  // CSS 预处理器配置
  css: {
    preprocessorOptions: {
      scss: {
        // 压缩 SCSS 输出
        outputStyle: 'compressed',
        // 移除源码映射
        sourceMap: false
      }
    },
    
    // PostCSS 配置
    postcss: {
      plugins: [
        // 自动添加浏览器前缀
        require('autoprefixer'),
        // CSS 压缩
        require('cssnano')({
          preset: ['default', {
            // 移除注释
            discardComments: { removeAll: true },
            // 压缩颜色值
            colormin: true,
            // 合并规则
            mergeRules: true,
            // 移除未使用的 CSS
            discardUnused: true
          }]
        })
      ]
    }
  }
})
```

### 图片和静态资源优化

```typescript
// vite.config.ts - 资源优化配置
import { defineConfig } from 'vite'
import { ViteImageOptimize } from 'vite-plugin-imagemin'

export default defineConfig({
  plugins: [
    vue(),
    
    // 图片压缩插件
    ViteImageOptimize({
      // JPEG 压缩
      mozjpeg: {
        quality: 80
      },
      // PNG 压缩
      pngquant: {
        quality: [0.65, 0.8]
      },
      // SVG 压缩
      svgo: {
        plugins: [
          { name: 'removeViewBox', active: false },
          { name: 'removeEmptyAttrs', active: true }
        ]
      },
      // WebP 转换
      webp: {
        quality: 80
      }
    })
  ],
  
  build: {
    rollupOptions: {
      output: {
        // 静态资源文件名
        assetFileNames: (assetInfo) => {
          const info = assetInfo.name.split('.')
          const ext = info[info.length - 1]
          
          if (/\.(png|jpe?g|gif|svg|webp|ico)$/i.test(assetInfo.name)) {
            return `images/[name]-[hash][extname]`
          }
          if (/\.(woff2?|eot|ttf|otf)$/i.test(assetInfo.name)) {
            return `fonts/[name]-[hash][extname]`
          }
          if (/\.css$/i.test(assetInfo.name)) {
            return `css/[name]-[hash][extname]`
          }
          
          return `assets/[name]-[hash][extname]`
        }
      }
    }
  }
})
```

### Gzip 和 Brotli 压缩

```typescript
// vite.config.ts - 压缩配置
import { defineConfig } from 'vite'
import viteCompression from 'vite-plugin-compression'

export default defineConfig({
  plugins: [
    vue(),
    
    // Gzip 压缩
    viteCompression({
      verbose: true,
      disable: false,
      threshold: 10240, // 10KB 以上的文件才压缩
      algorithm: 'gzip',
      ext: '.gz',
      deleteOriginFile: false // 保留原文件
    }),
    
    // Brotli 压缩
    viteCompression({
      verbose: true,
      disable: false,
      threshold: 10240,
      algorithm: 'brotliCompress',
      ext: '.br',
      deleteOriginFile: false
    })
  ]
})
```

## 缓存策略最佳实践

### HTTP 缓存配置

#### Nginx 缓存配置

```nginx
# nginx.conf
server {
    listen 80;
    server_name example.com;
    root /var/www/html;
    
    # 静态资源缓存
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
        add_header Vary "Accept-Encoding";
        
        # 启用 Gzip
        gzip on;
        gzip_vary on;
        gzip_min_length 1024;
        gzip_types
            text/plain
            text/css
            text/xml
            text/javascript
            application/javascript
            application/xml+rss
            application/json;
    }
    
    # HTML 文件缓存
    location ~* \.html$ {
        expires 1h;
        add_header Cache-Control "public, must-revalidate";
    }
    
    # API 缓存
    location /api/ {
        expires 5m;
        add_header Cache-Control "public, max-age=300";
        proxy_pass http://backend;
    }
    
    # 启用 Brotli 压缩
    brotli on;
    brotli_comp_level 6;
    brotli_types
        text/plain
        text/css
        application/json
        application/javascript
        text/xml
        application/xml
        application/xml+rss
        text/javascript;
}
```

#### Apache 缓存配置

```apache
# .htaccess
<IfModule mod_expires.c>
    ExpiresActive On
    
    # 静态资源缓存
    ExpiresByType text/css "access plus 1 year"
    ExpiresByType application/javascript "access plus 1 year"
    ExpiresByType image/png "access plus 1 year"
    ExpiresByType image/jpg "access plus 1 year"
    ExpiresByType image/jpeg "access plus 1 year"
    ExpiresByType image/gif "access plus 1 year"
    ExpiresByType image/svg+xml "access plus 1 year"
    ExpiresByType font/woff "access plus 1 year"
    ExpiresByType font/woff2 "access plus 1 year"
    
    # HTML 文件缓存
    ExpiresByType text/html "access plus 1 hour"
</IfModule>

<IfModule mod_headers.c>
    # 添加缓存控制头
    <FilesMatch "\.(css|js|png|jpg|jpeg|gif|ico|svg|woff|woff2)$">
        Header set Cache-Control "public, max-age=31536000, immutable"
    </FilesMatch>
    
    <FilesMatch "\.html$">
        Header set Cache-Control "public, max-age=3600, must-revalidate"
    </FilesMatch>
</IfModule>
```

### 浏览器缓存策略

#### Service Worker 缓存

```typescript
// public/sw.js
const CACHE_NAME = 'vue-app-v1.0.0'
const STATIC_CACHE = 'static-v1.0.0'
const DYNAMIC_CACHE = 'dynamic-v1.0.0'

// 需要缓存的静态资源
const STATIC_ASSETS = [
  '/',
  '/index.html',
  '/manifest.json',
  // 其他静态资源将在运行时添加
]

// 安装事件
self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open(STATIC_CACHE)
      .then(cache => cache.addAll(STATIC_ASSETS))
      .then(() => self.skipWaiting())
  )
})

// 激活事件
self.addEventListener('activate', (event) => {
  event.waitUntil(
    caches.keys().then(cacheNames => {
      return Promise.all(
        cacheNames.map(cacheName => {
          if (cacheName !== STATIC_CACHE && cacheName !== DYNAMIC_CACHE) {
            return caches.delete(cacheName)
          }
        })
      )
    }).then(() => self.clients.claim())
  )
})

// 拦截请求
self.addEventListener('fetch', (event) => {
  const { request } = event
  const url = new URL(request.url)
  
  // 静态资源缓存策略
  if (request.destination === 'script' || 
      request.destination === 'style' || 
      request.destination === 'image') {
    event.respondWith(
      caches.match(request).then(response => {
        return response || fetch(request).then(fetchResponse => {
          const responseClone = fetchResponse.clone()
          caches.open(STATIC_CACHE).then(cache => {
            cache.put(request, responseClone)
          })
          return fetchResponse
        })
      })
    )
    return
  }
  
  // API 请求缓存策略
  if (url.pathname.startsWith('/api/')) {
    event.respondWith(
      fetch(request).then(response => {
        const responseClone = response.clone()
        if (response.ok) {
          caches.open(DYNAMIC_CACHE).then(cache => {
            cache.put(request, responseClone)
          })
        }
        return response
      }).catch(() => {
        return caches.match(request)
      })
    )
    return
  }
  
  // HTML 页面缓存策略
  if (request.mode === 'navigate') {
    event.respondWith(
      fetch(request).catch(() => {
        return caches.match('/index.html')
      })
    )
  }
})
```

#### 应用级缓存管理

```typescript
// utils/cacheManager.ts

export class CacheManager {
  private static instance: CacheManager
  private memoryCache = new Map<string, { data: any; timestamp: number; ttl: number }>()
  
  static getInstance(): CacheManager {
    if (!CacheManager.instance) {
      CacheManager.instance = new CacheManager()
    }
    return CacheManager.instance
  }
  
  // 内存缓存
  set(key: string, data: any, ttl = 300000) { // 默认 5 分钟
    this.memoryCache.set(key, {
      data,
      timestamp: Date.now(),
      ttl
    })
  }
  
  get(key: string): any | null {
    const cached = this.memoryCache.get(key)
    if (!cached) return null
    
    if (Date.now() - cached.timestamp > cached.ttl) {
      this.memoryCache.delete(key)
      return null
    }
    
    return cached.data
  }
  
  // localStorage 缓存
  setLocal(key: string, data: any, ttl = 86400000) { // 默认 24 小时
    const item = {
      data,
      timestamp: Date.now(),
      ttl
    }
    localStorage.setItem(key, JSON.stringify(item))
  }
  
  getLocal(key: string): any | null {
    try {
      const item = localStorage.getItem(key)
      if (!item) return null
      
      const parsed = JSON.parse(item)
      if (Date.now() - parsed.timestamp > parsed.ttl) {
        localStorage.removeItem(key)
        return null
      }
      
      return parsed.data
    } catch {
      return null
    }
  }
  
  // IndexedDB 缓存（用于大型数据）
  async setIndexedDB(key: string, data: any, ttl = 604800000) { // 默认 7 天
    if (!('indexedDB' in window)) return
    
    const db = await this.openDB()
    const transaction = db.transaction(['cache'], 'readwrite')
    const store = transaction.objectStore('cache')
    
    await store.put({
      key,
      data,
      timestamp: Date.now(),
      ttl
    })
  }
  
  async getIndexedDB(key: string): Promise<any | null> {
    if (!('indexedDB' in window)) return null
    
    try {
      const db = await this.openDB()
      const transaction = db.transaction(['cache'], 'readonly')
      const store = transaction.objectStore('cache')
      const result = await store.get(key)
      
      if (!result) return null
      
      if (Date.now() - result.timestamp > result.ttl) {
        await this.deleteIndexedDB(key)
        return null
      }
      
      return result.data
    } catch {
      return null
    }
  }
  
  private async openDB(): Promise<IDBDatabase> {
    return new Promise((resolve, reject) => {
      const request = indexedDB.open('AppCache', 1)
      
      request.onerror = () => reject(request.error)
      request.onsuccess = () => resolve(request.result)
      
      request.onupgradeneeded = () => {
        const db = request.result
        if (!db.objectStoreNames.contains('cache')) {
          db.createObjectStore('cache', { keyPath: 'key' })
        }
      }
    })
  }
  
  private async deleteIndexedDB(key: string): Promise<void> {
    const db = await this.openDB()
    const transaction = db.transaction(['cache'], 'readwrite')
    const store = transaction.objectStore('cache')
    await store.delete(key)
  }
  
  // 清理过期缓存
  cleanup() {
    // 清理内存缓存
    for (const [key, value] of this.memoryCache.entries()) {
      if (Date.now() - value.timestamp > value.ttl) {
        this.memoryCache.delete(key)
      }
    }
    
    // 清理 localStorage
    for (let i = 0; i < localStorage.length; i++) {
      const key = localStorage.key(i)
      if (key) {
        try {
          const item = JSON.parse(localStorage.getItem(key) || '')
          if (item.timestamp && Date.now() - item.timestamp > item.ttl) {
            localStorage.removeItem(key)
          }
        } catch {
          // 忽略非缓存项
        }
      }
    }
  }
}

// 使用示例
const cacheManager = CacheManager.getInstance()

// 定期清理缓存
setInterval(() => {
  cacheManager.cleanup()
}, 300000) // 每 5 分钟清理一次
```

### CDN 缓存优化

```typescript
// utils/cdnOptimization.ts

export class CDNOptimizer {
  private cdnDomains = [
    'https://cdn1.example.com',
    'https://cdn2.example.com',
    'https://cdn3.example.com'
  ]
  
  // 域名分片
  getOptimalCDN(resourceType: string): string {
    const hash = this.hashCode(resourceType)
    const index = Math.abs(hash) % this.cdnDomains.length
    return this.cdnDomains[index]
  }
  
  // 资源 URL 优化
  optimizeResourceUrl(path: string, options: {
    width?: number
    height?: number
    quality?: number
    format?: 'webp' | 'avif' | 'jpg' | 'png'
  } = {}): string {
    const cdn = this.getOptimalCDN(path)
    const params = new URLSearchParams()
    
    if (options.width) params.set('w', options.width.toString())
    if (options.height) params.set('h', options.height.toString())
    if (options.quality) params.set('q', options.quality.toString())
    if (options.format) params.set('f', options.format)
    
    const queryString = params.toString()
    return `${cdn}${path}${queryString ? `?${queryString}` : ''}`
  }
  
  // 预加载关键资源
  preloadCriticalResources(resources: string[]) {
    resources.forEach(resource => {
      const link = document.createElement('link')
      link.rel = 'preload'
      link.href = this.optimizeResourceUrl(resource)
      
      if (resource.endsWith('.css')) {
        link.as = 'style'
      } else if (resource.endsWith('.js')) {
        link.as = 'script'
      } else if (/\.(jpg|jpeg|png|webp|avif)$/i.test(resource)) {
        link.as = 'image'
      }
      
      document.head.appendChild(link)
    })
  }
  
  // DNS 预解析
  prefetchDNS() {
    this.cdnDomains.forEach(domain => {
      const link = document.createElement('link')
      link.rel = 'dns-prefetch'
      link.href = domain
      document.head.appendChild(link)
    })
  }
  
  private hashCode(str: string): number {
    let hash = 0
    for (let i = 0; i < str.length; i++) {
      const char = str.charCodeAt(i)
      hash = ((hash << 5) - hash) + char
      hash = hash & hash // 转换为 32 位整数
    }
    return hash
  }
}

// 使用示例
const cdnOptimizer = new CDNOptimizer()

// 在应用启动时预解析 DNS
cdnOptimizer.prefetchDNS()

// 预加载关键资源
cdnOptimizer.preloadCriticalResources([
  '/css/critical.css',
  '/js/vendor.js',
  '/images/hero-banner.jpg'
])
```

## 构建性能监控

### 构建时间分析

```typescript
// vite.config.ts - 构建分析配置
import { defineConfig } from 'vite'
import { visualizer } from 'rollup-plugin-visualizer'
import { BundleAnalyzerPlugin } from 'webpack-bundle-analyzer'

export default defineConfig({
  plugins: [
    vue(),
    
    // Bundle 分析器
    visualizer({
      filename: 'dist/stats.html',
      open: true,
      gzipSize: true,
      brotliSize: true
    })
  ],
  
  build: {
    // 启用构建报告
    reportCompressedSize: true,
    
    rollupOptions: {
      plugins: [
        // 构建时间分析
        {
          name: 'build-timer',
          buildStart() {
            this.buildStartTime = Date.now()
          },
          generateBundle() {
            const buildTime = Date.now() - this.buildStartTime
            console.log(`Build completed in ${buildTime}ms`)
          }
        }
      ]
    }
  }
})
```

### 运行时性能监控

```typescript
// utils/performanceMonitor.ts

export class PerformanceMonitor {
  private metrics: Map<string, number[]> = new Map()
  
  // 监控页面加载性能
  monitorPageLoad() {
    window.addEventListener('load', () => {
      const navigation = performance.getEntriesByType('navigation')[0] as PerformanceNavigationTiming
      
      const metrics = {
        // DNS 查询时间
        dnsLookup: navigation.domainLookupEnd - navigation.domainLookupStart,
        // TCP 连接时间
        tcpConnect: navigation.connectEnd - navigation.connectStart,
        // 请求响应时间
        requestResponse: navigation.responseEnd - navigation.requestStart,
        // DOM 解析时间
        domParse: navigation.domContentLoadedEventEnd - navigation.responseEnd,
        // 资源加载时间
        resourceLoad: navigation.loadEventEnd - navigation.domContentLoadedEventEnd,
        // 总加载时间
        totalLoad: navigation.loadEventEnd - navigation.navigationStart
      }
      
      console.table(metrics)
      this.reportMetrics('page-load', metrics)
    })
  }
  
  // 监控资源加载性能
  monitorResourceLoad() {
    const observer = new PerformanceObserver((list) => {
      list.getEntries().forEach((entry) => {
        if (entry.entryType === 'resource') {
          const resource = entry as PerformanceResourceTiming
          
          const metrics = {
            name: resource.name,
            duration: resource.duration,
            transferSize: resource.transferSize,
            encodedBodySize: resource.encodedBodySize,
            decodedBodySize: resource.decodedBodySize
          }
          
          this.recordMetric('resource-load', resource.duration)
          
          // 检测慢资源
          if (resource.duration > 1000) {
            console.warn('Slow resource detected:', metrics)
          }
        }
      })
    })
    
    observer.observe({ entryTypes: ['resource'] })
  }
  
  // 监控 LCP (Largest Contentful Paint)
  monitorLCP() {
    const observer = new PerformanceObserver((list) => {
      const entries = list.getEntries()
      const lastEntry = entries[entries.length - 1]
      
      console.log('LCP:', lastEntry.startTime)
      this.recordMetric('lcp', lastEntry.startTime)
    })
    
    observer.observe({ entryTypes: ['largest-contentful-paint'] })
  }
  
  // 监控 FID (First Input Delay)
  monitorFID() {
    const observer = new PerformanceObserver((list) => {
      list.getEntries().forEach((entry) => {
        console.log('FID:', entry.processingStart - entry.startTime)
        this.recordMetric('fid', entry.processingStart - entry.startTime)
      })
    })
    
    observer.observe({ entryTypes: ['first-input'] })
  }
  
  // 监控 CLS (Cumulative Layout Shift)
  monitorCLS() {
    let clsValue = 0
    
    const observer = new PerformanceObserver((list) => {
      list.getEntries().forEach((entry) => {
        if (!entry.hadRecentInput) {
          clsValue += entry.value
        }
      })
      
      console.log('CLS:', clsValue)
      this.recordMetric('cls', clsValue)
    })
    
    observer.observe({ entryTypes: ['layout-shift'] })
  }
  
  // 记录指标
  private recordMetric(name: string, value: number) {
    if (!this.metrics.has(name)) {
      this.metrics.set(name, [])
    }
    this.metrics.get(name)!.push(value)
  }
  
  // 上报指标
  private reportMetrics(category: string, metrics: Record<string, number>) {
    // 发送到分析服务
    if (navigator.sendBeacon) {
      navigator.sendBeacon('/api/metrics', JSON.stringify({
        category,
        metrics,
        timestamp: Date.now(),
        userAgent: navigator.userAgent,
        url: location.href
      }))
    }
  }
  
  // 获取性能报告
  getPerformanceReport() {
    const report: Record<string, any> = {}
    
    this.metrics.forEach((values, name) => {
      report[name] = {
        count: values.length,
        average: values.reduce((a, b) => a + b, 0) / values.length,
        min: Math.min(...values),
        max: Math.max(...values),
        p95: this.percentile(values, 0.95)
      }
    })
    
    return report
  }
  
  private percentile(values: number[], p: number): number {
    const sorted = values.slice().sort((a, b) => a - b)
    const index = Math.ceil(sorted.length * p) - 1
    return sorted[index]
  }
}

// 使用示例
const performanceMonitor = new PerformanceMonitor()

// 启动性能监控
performanceMonitor.monitorPageLoad()
performanceMonitor.monitorResourceLoad()
performanceMonitor.monitorLCP()
performanceMonitor.monitorFID()
performanceMonitor.monitorCLS()

// 定期生成性能报告
setInterval(() => {
  const report = performanceMonitor.getPerformanceReport()
  console.log('Performance Report:', report)
}, 60000) // 每分钟生成一次报告
```

## 最佳实践总结

### 构建优化检查清单

- [ ] 实现路由级代码分割
- [ ] 配置第三方库分离
- [ ] 启用资源压缩（Gzip/Brotli）
- [ ] 优化图片和静态资源
- [ ] 配置适当的缓存策略
- [ ] 实现 Service Worker 缓存
- [ ] 使用 CDN 加速资源加载
- [ ] 监控构建和运行时性能
- [ ] 定期分析 Bundle 大小
- [ ] 实施性能预算控制

### 性能目标

1. **首屏加载时间** < 2 秒
2. **JavaScript Bundle** < 250KB (gzipped)
3. **CSS Bundle** < 50KB (gzipped)
4. **图片优化率** > 70%
5. **缓存命中率** > 90%
6. **Lighthouse 性能评分** > 90

### 持续优化建议

1. **定期审查依赖包大小**
2. **监控 Core Web Vitals 指标**
3. **实施性能预算**
4. **使用现代图片格式（WebP/AVIF）**
5. **优化关键渲染路径**
6. **实施渐进式 Web 应用（PWA）特性**