# 构建工具配置指南

## 概述

本文档提供Vue项目中构建工具的配置规范和最佳实践，主要涵盖Vite构建工具的配置、环境变量管理、打包优化和开发服务器配置。

## Vite配置最佳实践

### 基础配置结构

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import { resolve } from 'path'

export default defineConfig({
  plugins: [vue()],
  resolve: {
    alias: {
      '@': resolve(__dirname, 'src'),
      '@components': resolve(__dirname, 'src/components'),
      '@utils': resolve(__dirname, 'src/utils'),
      '@assets': resolve(__dirname, 'src/assets'),
      '@stores': resolve(__dirname, 'src/stores'),
      '@types': resolve(__dirname, 'src/types')
    }
  },
  // 其他配置...
})
```

### 路径别名配置

```typescript
// 推荐的路径别名配置
resolve: {
  alias: {
    // 基础别名
    '@': resolve(__dirname, 'src'),
    
    // 功能模块别名
    '@components': resolve(__dirname, 'src/components'),
    '@views': resolve(__dirname, 'src/views'),
    '@stores': resolve(__dirname, 'src/stores'),
    '@utils': resolve(__dirname, 'src/utils'),
    '@api': resolve(__dirname, 'src/api'),
    '@types': resolve(__dirname, 'src/types'),
    
    // 资源别名
    '@assets': resolve(__dirname, 'src/assets'),
    '@images': resolve(__dirname, 'src/assets/images'),
    '@styles': resolve(__dirname, 'src/assets/styles')
  }
}
```

### 插件配置

```typescript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import { resolve } from 'path'

// 开发环境插件
const developmentPlugins = [
  vue({
    script: {
      defineModel: true,
      propsDestructure: true
    }
  })
]

// 生产环境插件
const productionPlugins = [
  ...developmentPlugins,
  // 生产环境特定插件
]

export default defineConfig(({ mode }) => ({
  plugins: mode === 'development' ? developmentPlugins : productionPlugins,
  // 其他配置...
}))
```

## 环境变量管理规范

### 环境变量文件结构

```
项目根目录/
├── .env                    # 所有环境的默认变量
├── .env.local             # 本地环境变量（不提交到版本控制）
├── .env.development       # 开发环境变量
├── .env.production        # 生产环境变量
├── .env.staging          # 预发布环境变量
└── .env.test             # 测试环境变量
```

### 环境变量命名规范

```bash
# .env.development
# API配置
VITE_API_BASE_URL=http://localhost:3000/api
VITE_API_TIMEOUT=10000

# 应用配置
VITE_APP_TITLE=Vue应用开发版
VITE_APP_VERSION=1.0.0-dev
VITE_APP_ENV=development

# 功能开关
VITE_ENABLE_MOCK=true
VITE_ENABLE_DEBUG=true
VITE_ENABLE_ANALYTICS=false

# 第三方服务
VITE_SENTRY_DSN=
VITE_GA_TRACKING_ID=
```

```bash
# .env.production
# API配置
VITE_API_BASE_URL=https://api.example.com
VITE_API_TIMEOUT=5000

# 应用配置
VITE_APP_TITLE=Vue应用
VITE_APP_VERSION=1.0.0
VITE_APP_ENV=production

# 功能开关
VITE_ENABLE_MOCK=false
VITE_ENABLE_DEBUG=false
VITE_ENABLE_ANALYTICS=true

# 第三方服务
VITE_SENTRY_DSN=https://your-sentry-dsn
VITE_GA_TRACKING_ID=GA-XXXXXXXXX
```

### 环境变量类型定义

```typescript
// src/types/env.d.ts
interface ImportMetaEnv {
  // API配置
  readonly VITE_API_BASE_URL: string
  readonly VITE_API_TIMEOUT: string
  
  // 应用配置
  readonly VITE_APP_TITLE: string
  readonly VITE_APP_VERSION: string
  readonly VITE_APP_ENV: 'development' | 'production' | 'staging' | 'test'
  
  // 功能开关
  readonly VITE_ENABLE_MOCK: string
  readonly VITE_ENABLE_DEBUG: string
  readonly VITE_ENABLE_ANALYTICS: string
  
  // 第三方服务
  readonly VITE_SENTRY_DSN: string
  readonly VITE_GA_TRACKING_ID: string
}

interface ImportMeta {
  readonly env: ImportMetaEnv
}
```

### 环境变量使用规范

```typescript
// src/config/env.ts
export const env = {
  // API配置
  apiBaseUrl: import.meta.env.VITE_API_BASE_URL,
  apiTimeout: Number(import.meta.env.VITE_API_TIMEOUT),
  
  // 应用配置
  appTitle: import.meta.env.VITE_APP_TITLE,
  appVersion: import.meta.env.VITE_APP_VERSION,
  appEnv: import.meta.env.VITE_APP_ENV,
  
  // 功能开关
  enableMock: import.meta.env.VITE_ENABLE_MOCK === 'true',
  enableDebug: import.meta.env.VITE_ENABLE_DEBUG === 'true',
  enableAnalytics: import.meta.env.VITE_ENABLE_ANALYTICS === 'true',
  
  // 第三方服务
  sentryDsn: import.meta.env.VITE_SENTRY_DSN,
  gaTrackingId: import.meta.env.VITE_GA_TRACKING_ID
}

// 环境检查工具
export const isDevelopment = env.appEnv === 'development'
export const isProduction = env.appEnv === 'production'
export const isStaging = env.appEnv === 'staging'
export const isTest = env.appEnv === 'test'
```

## 打包优化配置标准

### 基础打包优化

```typescript
// vite.config.ts
export default defineConfig({
  build: {
    // 输出目录
    outDir: 'dist',
    
    // 静态资源目录
    assetsDir: 'assets',
    
    // 小于此阈值的导入或引用资源将内联为base64编码
    assetsInlineLimit: 4096,
    
    // 启用CSS代码拆分
    cssCodeSplit: true,
    
    // 构建后是否生成source map文件
    sourcemap: false,
    
    // 设置为false可以禁用最小化混淆
    minify: 'terser',
    
    // 传递给Terser的更多minify选项
    terserOptions: {
      compress: {
        drop_console: true,
        drop_debugger: true
      }
    },
    
    // 启用/禁用gzip压缩大小报告
    reportCompressedSize: false,
    
    // chunk大小警告的限制（以kbs为单位）
    chunkSizeWarningLimit: 500
  }
})
```

### 代码分割配置

```typescript
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        // 手动分割代码
        manualChunks: {
          // 将Vue相关库打包到vendor chunk
          vue: ['vue', 'vue-router', 'pinia'],
          
          // 将UI库单独打包
          ui: ['element-plus', '@element-plus/icons-vue'],
          
          // 将工具库单独打包
          utils: ['lodash-es', 'dayjs', 'axios']
        },
        
        // 自定义chunk文件名
        chunkFileNames: (chunkInfo) => {
          const facadeModuleId = chunkInfo.facadeModuleId
          if (facadeModuleId) {
            const fileName = facadeModuleId.split('/').pop()?.replace(/\.\w+$/, '')
            return `js/${fileName}-[hash].js`
          }
          return 'js/[name]-[hash].js'
        },
        
        // 自定义入口文件名
        entryFileNames: 'js/[name]-[hash].js',
        
        // 自定义静态资源文件名
        assetFileNames: (assetInfo) => {
          const info = assetInfo.name?.split('.') || []
          let extType = info[info.length - 1]
          
          if (/\.(mp4|webm|ogg|mp3|wav|flac|aac)(\?.*)?$/i.test(assetInfo.name || '')) {
            extType = 'media'
          } else if (/\.(png|jpe?g|gif|svg)(\?.*)?$/i.test(assetInfo.name || '')) {
            extType = 'images'
          } else if (/\.(woff2?|eot|ttf|otf)(\?.*)?$/i.test(assetInfo.name || '')) {
            extType = 'fonts'
          }
          
          return `${extType}/[name]-[hash].[ext]`
        }
      }
    }
  }
})
```

### 性能优化配置

```typescript
export default defineConfig({
  build: {
    rollupOptions: {
      // 外部化依赖
      external: ['vue'],
      output: {
        globals: {
          vue: 'Vue'
        }
      }
    }
  },
  
  // 依赖预构建
  optimizeDeps: {
    include: [
      'vue',
      'vue-router',
      'pinia',
      'axios',
      'lodash-es'
    ],
    exclude: [
      // 排除不需要预构建的依赖
    ]
  }
})
```

## 开发服务器配置指南

### 基础开发服务器配置

```typescript
export default defineConfig({
  server: {
    // 服务器主机名
    host: '0.0.0.0',
    
    // 服务器端口号
    port: 3000,
    
    // 端口被占用时是否自动尝试下一个可用端口
    strictPort: false,
    
    // 服务器启动时自动在浏览器中打开应用程序
    open: true,
    
    // 自定义代理规则
    proxy: {
      '/api': {
        target: 'http://localhost:8080',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, '')
      }
    },
    
    // 为开发服务器配置CORS
    cors: true
  }
})
```

### 代理配置详解

```typescript
export default defineConfig({
  server: {
    proxy: {
      // 基础API代理
      '/api': {
        target: 'http://localhost:8080',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, '/api/v1')
      },
      
      // WebSocket代理
      '/ws': {
        target: 'ws://localhost:8080',
        ws: true,
        changeOrigin: true
      },
      
      // 静态资源代理
      '/uploads': {
        target: 'http://localhost:8080',
        changeOrigin: true
      },
      
      // 条件代理
      '^/fallback/.*': {
        target: 'http://localhost:8080',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/fallback/, '')
      }
    }
  }
})
```

### 热更新配置

```typescript
export default defineConfig({
  server: {
    hmr: {
      // HMR端口
      port: 24678,
      
      // 禁用或配置HMR连接
      // 设置 server.hmr.overlay 为 false 可以禁用开发服务器错误的屏蔽
      overlay: true
    },
    
    // 监听文件变化
    watch: {
      // 排除监听的文件
      ignored: ['**/node_modules/**', '**/.git/**'],
      
      // 使用轮询监听
      usePolling: false
    }
  }
})
```

### HTTPS配置

```typescript
import { defineConfig } from 'vite'
import fs from 'fs'

export default defineConfig({
  server: {
    https: {
      key: fs.readFileSync('path/to/key.pem'),
      cert: fs.readFileSync('path/to/cert.pem')
    },
    
    // 或者使用自动生成的证书
    // https: true
  }
})
```

## 环境特定配置

### 开发环境配置

```typescript
// vite.config.development.ts
export default defineConfig({
  mode: 'development',
  
  define: {
    __DEV__: true,
    __PROD__: false
  },
  
  server: {
    port: 3000,
    open: true,
    proxy: {
      '/api': 'http://localhost:8080'
    }
  },
  
  build: {
    sourcemap: true,
    minify: false
  }
})
```

### 生产环境配置

```typescript
// vite.config.production.ts
export default defineConfig({
  mode: 'production',
  
  define: {
    __DEV__: false,
    __PROD__: true
  },
  
  build: {
    sourcemap: false,
    minify: 'terser',
    terserOptions: {
      compress: {
        drop_console: true,
        drop_debugger: true
      }
    },
    
    rollupOptions: {
      output: {
        manualChunks: {
          vue: ['vue', 'vue-router', 'pinia'],
          vendor: ['axios', 'lodash-es']
        }
      }
    }
  }
})
```

## 配置文件组织

### 多环境配置管理

```typescript
// config/vite.base.ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import { resolve } from 'path'

export const baseConfig = defineConfig({
  plugins: [vue()],
  resolve: {
    alias: {
      '@': resolve(__dirname, '../src')
    }
  }
})

// config/vite.dev.ts
import { mergeConfig } from 'vite'
import { baseConfig } from './vite.base'

export default mergeConfig(baseConfig, {
  mode: 'development',
  server: {
    port: 3000,
    open: true
  }
})

// config/vite.prod.ts
import { mergeConfig } from 'vite'
import { baseConfig } from './vite.base'

export default mergeConfig(baseConfig, {
  mode: 'production',
  build: {
    minify: 'terser',
    sourcemap: false
  }
})
```

### 主配置文件

```typescript
// vite.config.ts
import { defineConfig } from 'vite'

export default defineConfig(({ command, mode }) => {
  if (command === 'serve') {
    // 开发环境配置
    return {
      // 开发特定配置
    }
  } else {
    // 生产环境配置
    return {
      // 生产特定配置
    }
  }
})
```

## 最佳实践总结

### 配置原则

1. **环境分离**：不同环境使用不同的配置文件
2. **安全性**：敏感信息通过环境变量管理
3. **性能优化**：合理配置代码分割和资源优化
4. **开发体验**：配置热更新和代理提升开发效率

### 常见问题解决

1. **端口冲突**：配置自动端口检测
2. **代理失效**：检查target地址和changeOrigin设置
3. **热更新失败**：检查文件监听配置
4. **构建缓存**：合理配置缓存策略

### 性能监控

```typescript
// 构建分析插件
import { defineConfig } from 'vite'
import { visualizer } from 'rollup-plugin-visualizer'

export default defineConfig({
  plugins: [
    // 生成构建分析报告
    visualizer({
      filename: 'dist/stats.html',
      open: true,
      gzipSize: true
    })
  ]
})
```

通过遵循这些配置规范，可以确保Vue项目的构建工具配置标准化、高效化，提升开发体验和应用性能。