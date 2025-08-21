# Vue Router 路由配置规范

## 概述

本文档定义了Vue Router在项目中的配置规范和最佳实践，包括路由定义、嵌套路由组织、动态路由处理等标准。

## 路由定义标准格式

### 基础路由配置

```typescript
// router/index.ts
import { createRouter, createWebHistory, RouteRecordRaw } from 'vue-router'

const routes: RouteRecordRaw[] = [
  {
    path: '/',
    name: 'Home',
    component: () => import('@/views/HomeView.vue'),
    meta: {
      title: '首页',
      requiresAuth: false,
      keepAlive: true
    }
  },
  {
    path: '/about',
    name: 'About',
    component: () => import('@/views/AboutView.vue'),
    meta: {
      title: '关于我们',
      requiresAuth: false
    }
  }
]

const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes,
  scrollBehavior(to, from, savedPosition) {
    if (savedPosition) {
      return savedPosition
    } else {
      return { top: 0 }
    }
  }
})

export default router
```

### 路由命名规范

**路由名称（name）**
- 使用PascalCase命名
- 名称应该描述性强，易于理解
- 避免使用缩写，除非是广泛认知的缩写

```typescript
// ✅ 推荐
{ name: 'UserProfile', path: '/user/profile' }
{ name: 'ProductDetail', path: '/product/:id' }
{ name: 'OrderHistory', path: '/order/history' }

// ❌ 不推荐
{ name: 'usrProf', path: '/user/profile' }
{ name: 'prodDtl', path: '/product/:id' }
{ name: 'ordHist', path: '/order/history' }
```

**路径（path）规范**
- 使用kebab-case命名
- 路径应该语义化，反映页面功能
- 避免过深的嵌套层级（建议不超过3层）

```typescript
// ✅ 推荐
'/user-management'
'/product-category'
'/order-history'

// ❌ 不推荐
'/userManagement'
'/ProductCategory'
'/order_history'
```

## 嵌套路由组织指南

### 布局路由结构

```typescript
const routes: RouteRecordRaw[] = [
  {
    path: '/',
    component: () => import('@/layouts/DefaultLayout.vue'),
    children: [
      {
        path: '',
        name: 'Home',
        component: () => import('@/views/HomeView.vue')
      },
      {
        path: 'about',
        name: 'About',
        component: () => import('@/views/AboutView.vue')
      }
    ]
  },
  {
    path: '/admin',
    component: () => import('@/layouts/AdminLayout.vue'),
    meta: { requiresAuth: true, role: 'admin' },
    children: [
      {
        path: '',
        name: 'AdminDashboard',
        component: () => import('@/views/admin/DashboardView.vue')
      },
      {
        path: 'users',
        name: 'AdminUsers',
        component: () => import('@/views/admin/UsersView.vue')
      }
    ]
  }
]
```

### 模块化路由组织

```typescript
// router/modules/user.ts
import { RouteRecordRaw } from 'vue-router'

export const userRoutes: RouteRecordRaw[] = [
  {
    path: '/user',
    component: () => import('@/layouts/UserLayout.vue'),
    meta: { requiresAuth: true },
    children: [
      {
        path: '',
        redirect: '/user/profile'
      },
      {
        path: 'profile',
        name: 'UserProfile',
        component: () => import('@/views/user/ProfileView.vue'),
        meta: { title: '个人资料' }
      },
      {
        path: 'settings',
        name: 'UserSettings',
        component: () => import('@/views/user/SettingsView.vue'),
        meta: { title: '账户设置' }
      }
    ]
  }
]
```

```typescript
// router/index.ts
import { userRoutes } from './modules/user'
import { adminRoutes } from './modules/admin'
import { productRoutes } from './modules/product'

const routes: RouteRecordRaw[] = [
  ...userRoutes,
  ...adminRoutes,
  ...productRoutes,
  // 其他路由
]
```

## 动态路由处理规范

### 参数路由定义

```typescript
const routes: RouteRecordRaw[] = [
  {
    path: '/user/:id',
    name: 'UserDetail',
    component: () => import('@/views/UserDetailView.vue'),
    props: true, // 将路由参数作为props传递给组件
    meta: {
      title: '用户详情',
      requiresAuth: true
    }
  },
  {
    path: '/product/:category/:id',
    name: 'ProductDetail',
    component: () => import('@/views/ProductDetailView.vue'),
    props: (route) => ({
      category: route.params.category,
      productId: route.params.id,
      // 可以添加查询参数
      tab: route.query.tab || 'overview'
    })
  }
]
```

### 可选参数处理

```typescript
const routes: RouteRecordRaw[] = [
  {
    // 可选参数使用?标记
    path: '/search/:keyword?',
    name: 'Search',
    component: () => import('@/views/SearchView.vue'),
    props: (route) => ({
      keyword: route.params.keyword || '',
      filters: route.query
    })
  }
]
```

### 通配符路由

```typescript
const routes: RouteRecordRaw[] = [
  // 404页面 - 必须放在路由配置的最后
  {
    path: '/:pathMatch(.*)*',
    name: 'NotFound',
    component: () => import('@/views/NotFoundView.vue'),
    meta: {
      title: '页面未找到'
    }
  }
]
```

## 路由元信息配置

### 标准元信息字段

```typescript
interface RouteMeta {
  title?: string           // 页面标题
  requiresAuth?: boolean   // 是否需要登录
  roles?: string[]         // 允许访问的角色
  permissions?: string[]   // 需要的权限
  keepAlive?: boolean      // 是否缓存组件
  showInMenu?: boolean     // 是否在菜单中显示
  icon?: string           // 菜单图标
  order?: number          // 菜单排序
  breadcrumb?: boolean    // 是否显示面包屑
  affix?: boolean         // 是否固定在标签页
}
```

### 元信息使用示例

```typescript
const routes: RouteRecordRaw[] = [
  {
    path: '/dashboard',
    name: 'Dashboard',
    component: () => import('@/views/DashboardView.vue'),
    meta: {
      title: '仪表盘',
      requiresAuth: true,
      roles: ['admin', 'user'],
      keepAlive: true,
      showInMenu: true,
      icon: 'dashboard',
      order: 1,
      breadcrumb: true
    }
  },
  {
    path: '/admin/system',
    name: 'SystemManagement',
    component: () => import('@/views/admin/SystemView.vue'),
    meta: {
      title: '系统管理',
      requiresAuth: true,
      roles: ['admin'],
      permissions: ['system:read'],
      showInMenu: true,
      icon: 'settings',
      order: 99
    }
  }
]
```

## 路由懒加载规范

### 组件懒加载

```typescript
// ✅ 推荐：使用动态导入
const routes: RouteRecordRaw[] = [
  {
    path: '/heavy-component',
    name: 'HeavyComponent',
    component: () => import('@/views/HeavyComponentView.vue')
  }
]

// ✅ 推荐：带有注释的懒加载（用于webpack chunk命名）
const routes: RouteRecordRaw[] = [
  {
    path: '/user',
    name: 'User',
    component: () => import(
      /* webpackChunkName: "user" */ 
      '@/views/UserView.vue'
    )
  }
]
```

### 路由分组懒加载

```typescript
// 将相关路由打包到同一个chunk中
const routes: RouteRecordRaw[] = [
  {
    path: '/admin/users',
    component: () => import(
      /* webpackChunkName: "admin" */ 
      '@/views/admin/UsersView.vue'
    )
  },
  {
    path: '/admin/settings',
    component: () => import(
      /* webpackChunkName: "admin" */ 
      '@/views/admin/SettingsView.vue'
    )
  }
]
```

## 路由配置最佳实践

### 1. 路由结构组织

```typescript
// 推荐的目录结构
src/
├── router/
│   ├── index.ts          // 主路由文件
│   ├── guards.ts         // 路由守卫
│   └── modules/          // 模块化路由
│       ├── user.ts
│       ├── admin.ts
│       └── product.ts
├── views/                // 页面组件
│   ├── HomeView.vue
│   ├── user/
│   ├── admin/
│   └── product/
└── layouts/              // 布局组件
    ├── DefaultLayout.vue
    ├── AdminLayout.vue
    └── UserLayout.vue
```

### 2. 路由命名约定

```typescript
// 路由名称应该与组件名称保持一致
{
  path: '/user/profile',
  name: 'UserProfile',           // 对应 UserProfileView.vue
  component: () => import('@/views/user/UserProfileView.vue')
}
```

### 3. 重定向处理

```typescript
const routes: RouteRecordRaw[] = [
  // 根路径重定向
  {
    path: '/',
    redirect: '/dashboard'
  },
  // 模块根路径重定向
  {
    path: '/user',
    redirect: '/user/profile'
  },
  // 条件重定向
  {
    path: '/admin',
    redirect: (to) => {
      // 根据用户权限决定重定向目标
      return '/admin/dashboard'
    }
  }
]
```

### 4. 错误处理

```typescript
const routes: RouteRecordRaw[] = [
  // 403 权限不足
  {
    path: '/403',
    name: 'Forbidden',
    component: () => import('@/views/error/ForbiddenView.vue'),
    meta: { title: '权限不足' }
  },
  // 404 页面未找到
  {
    path: '/:pathMatch(.*)*',
    name: 'NotFound',
    component: () => import('@/views/error/NotFoundView.vue'),
    meta: { title: '页面未找到' }
  }
]
```

## TypeScript 类型定义

### 扩展路由元信息类型

```typescript
// types/router.d.ts
import 'vue-router'

declare module 'vue-router' {
  interface RouteMeta {
    title?: string
    requiresAuth?: boolean
    roles?: string[]
    permissions?: string[]
    keepAlive?: boolean
    showInMenu?: boolean
    icon?: string
    order?: number
    breadcrumb?: boolean
    affix?: boolean
  }
}
```

### 路由参数类型定义

```typescript
// 定义路由参数接口
interface UserRouteParams {
  id: string
}

interface ProductRouteParams {
  category: string
  id: string
}

// 在组件中使用
import { useRoute } from 'vue-router'

const route = useRoute()
const userId = route.params.id as string
```

## 性能优化建议

### 1. 路由预加载

```typescript
// 在路由配置中添加预加载
const routes: RouteRecordRaw[] = [
  {
    path: '/dashboard',
    name: 'Dashboard',
    component: () => import('@/views/DashboardView.vue'),
    meta: {
      preload: true // 自定义标记，用于预加载逻辑
    }
  }
]
```

### 2. 组件缓存策略

```typescript
// 在路由元信息中配置缓存策略
{
  path: '/user/list',
  name: 'UserList',
  component: () => import('@/views/user/UserListView.vue'),
  meta: {
    keepAlive: true,        // 启用缓存
    maxCacheTime: 300000    // 缓存时间（毫秒）
  }
}
```

### 3. 路由切换优化

```typescript
// 在router配置中添加切换动画控制
const router = createRouter({
  history: createWebHistory(),
  routes,
  scrollBehavior(to, from, savedPosition) {
    // 优化滚动行为
    if (savedPosition) {
      return savedPosition
    }
    if (to.hash) {
      return { el: to.hash, behavior: 'smooth' }
    }
    return { top: 0, behavior: 'smooth' }
  }
})
```