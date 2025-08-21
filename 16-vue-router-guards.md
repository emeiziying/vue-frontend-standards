# Vue Router 路由守卫和权限控制规范

## 概述

本文档定义了Vue Router中路由守卫的使用规范和权限控制的最佳实践，包括导航守卫的配置、权限验证机制和路由元信息的标准化使用。

## 导航守卫使用指南

### 全局前置守卫

```typescript
// router/guards.ts
import { NavigationGuardNext, RouteLocationNormalized } from 'vue-router'
import { useUserStore } from '@/stores/user'
import { ElMessage } from 'element-plus'

/**
 * 全局前置守卫
 * 处理身份验证、权限检查等
 */
export function setupRouterGuards(router: Router) {
  router.beforeEach(async (
    to: RouteLocationNormalized,
    from: RouteLocationNormalized,
    next: NavigationGuardNext
  ) => {
    // 1. 显示加载状态
    showPageLoading()

    try {
      // 2. 检查是否需要身份验证
      if (to.meta.requiresAuth) {
        const userStore = useUserStore()
        
        // 检查用户是否已登录
        if (!userStore.isLoggedIn) {
          ElMessage.warning('请先登录')
          next({
            name: 'Login',
            query: { redirect: to.fullPath }
          })
          return
        }

        // 检查用户信息是否完整
        if (!userStore.userInfo) {
          await userStore.fetchUserInfo()
        }
      }

      // 3. 检查角色权限
      if (to.meta.roles && to.meta.roles.length > 0) {
        const hasRole = checkUserRole(to.meta.roles)
        if (!hasRole) {
          ElMessage.error('权限不足')
          next({ name: 'Forbidden' })
          return
        }
      }

      // 4. 检查具体权限
      if (to.meta.permissions && to.meta.permissions.length > 0) {
        const hasPermission = await checkUserPermissions(to.meta.permissions)
        if (!hasPermission) {
          ElMessage.error('权限不足')
          next({ name: 'Forbidden' })
          return
        }
      }

      // 5. 设置页面标题
      if (to.meta.title) {
        document.title = `${to.meta.title} - ${import.meta.env.VITE_APP_TITLE}`
      }

      next()
    } catch (error) {
      console.error('路由守卫错误:', error)
      ElMessage.error('页面加载失败')
      next({ name: 'Error' })
    } finally {
      hidePageLoading()
    }
  })
}
```

### 全局后置守卫

```typescript
// router/guards.ts
export function setupAfterGuards(router: Router) {
  router.afterEach((to, from) => {
    // 1. 隐藏加载状态
    hidePageLoading()

    // 2. 记录页面访问日志
    logPageVisit(to)

    // 3. 更新面包屑导航
    updateBreadcrumb(to)

    // 4. 处理页面缓存
    handlePageCache(to, from)

    // 5. 埋点统计
    trackPageView(to)
  })
}
```

### 路由独享守卫

```typescript
// 在路由配置中使用
const routes: RouteRecordRaw[] = [
  {
    path: '/admin',
    name: 'Admin',
    component: () => import('@/views/AdminView.vue'),
    beforeEnter: (to, from, next) => {
      // 管理员页面特殊验证逻辑
      const userStore = useUserStore()
      
      if (!userStore.isAdmin) {
        ElMessage.error('需要管理员权限')
        next({ name: 'Home' })
        return
      }
      
      // 检查管理员会话是否过期
      if (userStore.isAdminSessionExpired) {
        next({ name: 'AdminLogin' })
        return
      }
      
      next()
    }
  }
]
```

### 组件内守卫

```vue
<!-- UserProfileView.vue -->
<script setup lang="ts">
import { onBeforeRouteEnter, onBeforeRouteUpdate, onBeforeRouteLeave } from 'vue-router'
import { useUserStore } from '@/stores/user'

const userStore = useUserStore()

// 进入路由前
onBeforeRouteEnter((to, from, next) => {
  // 检查是否是用户本人或管理员
  const userId = to.params.id as string
  if (userId !== userStore.currentUser?.id && !userStore.isAdmin) {
    ElMessage.error('只能查看自己的资料')
    next({ name: 'Home' })
    return
  }
  next()
})

// 路由更新时
onBeforeRouteUpdate((to, from, next) => {
  // 处理用户ID变化
  const newUserId = to.params.id as string
  loadUserProfile(newUserId)
  next()
})

// 离开路由前
onBeforeRouteLeave((to, from, next) => {
  // 检查是否有未保存的更改
  if (hasUnsavedChanges.value) {
    const answer = window.confirm('有未保存的更改，确定要离开吗？')
    if (!answer) {
      next(false)
      return
    }
  }
  next()
})
</script>
```

## 权限验证最佳实践

### 权限系统设计

```typescript
// types/auth.ts
export interface Permission {
  id: string
  name: string
  code: string
  resource: string
  action: string
}

export interface Role {
  id: string
  name: string
  code: string
  permissions: Permission[]
}

export interface User {
  id: string
  username: string
  roles: Role[]
  permissions: Permission[]
}
```

### 权限检查工具函数

```typescript
// utils/auth.ts
import { useUserStore } from '@/stores/user'

/**
 * 检查用户是否具有指定角色
 */
export function hasRole(roleCode: string | string[]): boolean {
  const userStore = useUserStore()
  const userRoles = userStore.userInfo?.roles || []
  
  if (Array.isArray(roleCode)) {
    return roleCode.some(code => 
      userRoles.some(role => role.code === code)
    )
  }
  
  return userRoles.some(role => role.code === roleCode)
}

/**
 * 检查用户是否具有指定权限
 */
export function hasPermission(permissionCode: string | string[]): boolean {
  const userStore = useUserStore()
  const userPermissions = userStore.userInfo?.permissions || []
  
  if (Array.isArray(permissionCode)) {
    return permissionCode.every(code =>
      userPermissions.some(permission => permission.code === code)
    )
  }
  
  return userPermissions.some(permission => permission.code === permissionCode)
}

/**
 * 检查资源操作权限
 */
export function hasResourcePermission(resource: string, action: string): boolean {
  const userStore = useUserStore()
  const userPermissions = userStore.userInfo?.permissions || []
  
  return userPermissions.some(permission => 
    permission.resource === resource && permission.action === action
  )
}

/**
 * 检查用户角色（用于路由守卫）
 */
export function checkUserRole(requiredRoles: string[]): boolean {
  if (!requiredRoles || requiredRoles.length === 0) {
    return true
  }
  
  return hasRole(requiredRoles)
}

/**
 * 检查用户权限（用于路由守卫）
 */
export async function checkUserPermissions(requiredPermissions: string[]): Promise<boolean> {
  if (!requiredPermissions || requiredPermissions.length === 0) {
    return true
  }
  
  // 如果需要，可以在这里进行异步权限检查
  return hasPermission(requiredPermissions)
}
```

### 权限指令

```typescript
// directives/permission.ts
import { Directive, DirectiveBinding } from 'vue'
import { hasPermission, hasRole } from '@/utils/auth'

/**
 * 权限指令
 * v-permission="'user:create'"
 * v-permission="['user:create', 'user:update']"
 */
export const permission: Directive = {
  mounted(el: HTMLElement, binding: DirectiveBinding) {
    const { value } = binding
    
    if (value) {
      const hasAuth = hasPermission(value)
      if (!hasAuth) {
        el.style.display = 'none'
        // 或者移除元素
        // el.parentNode?.removeChild(el)
      }
    }
  },
  updated(el: HTMLElement, binding: DirectiveBinding) {
    const { value } = binding
    
    if (value) {
      const hasAuth = hasPermission(value)
      el.style.display = hasAuth ? '' : 'none'
    }
  }
}

/**
 * 角色指令
 * v-role="'admin'"
 * v-role="['admin', 'manager']"
 */
export const role: Directive = {
  mounted(el: HTMLElement, binding: DirectiveBinding) {
    const { value } = binding
    
    if (value) {
      const hasAuth = hasRole(value)
      if (!hasAuth) {
        el.style.display = 'none'
      }
    }
  },
  updated(el: HTMLElement, binding: DirectiveBinding) {
    const { value } = binding
    
    if (value) {
      const hasAuth = hasRole(value)
      el.style.display = hasAuth ? '' : 'none'
    }
  }
}
```

### 权限组件

```vue
<!-- components/PermissionWrapper.vue -->
<template>
  <div v-if="hasAuth">
    <slot />
  </div>
  <div v-else-if="$slots.fallback">
    <slot name="fallback" />
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import { hasPermission, hasRole } from '@/utils/auth'

interface Props {
  permission?: string | string[]
  role?: string | string[]
  requireAll?: boolean  // 是否需要满足所有权限/角色
}

const props = withDefaults(defineProps<Props>(), {
  requireAll: false
})

const hasAuth = computed(() => {
  let permissionCheck = true
  let roleCheck = true
  
  // 检查权限
  if (props.permission) {
    if (Array.isArray(props.permission)) {
      permissionCheck = props.requireAll
        ? props.permission.every(p => hasPermission(p))
        : props.permission.some(p => hasPermission(p))
    } else {
      permissionCheck = hasPermission(props.permission)
    }
  }
  
  // 检查角色
  if (props.role) {
    if (Array.isArray(props.role)) {
      roleCheck = props.requireAll
        ? props.role.every(r => hasRole(r))
        : props.role.some(r => hasRole(r))
    } else {
      roleCheck = hasRole(props.role)
    }
  }
  
  return permissionCheck && roleCheck
})
</script>
```

## 路由元信息配置标准

### 标准元信息字段定义

```typescript
// types/router.d.ts
import 'vue-router'

declare module 'vue-router' {
  interface RouteMeta {
    // 基础信息
    title?: string                    // 页面标题
    description?: string              // 页面描述
    keywords?: string[]               // SEO关键词
    
    // 权限控制
    requiresAuth?: boolean            // 是否需要登录
    roles?: string[]                  // 允许访问的角色
    permissions?: string[]            // 需要的权限
    
    // 页面行为
    keepAlive?: boolean               // 是否缓存组件
    showInMenu?: boolean              // 是否在菜单中显示
    hideInBreadcrumb?: boolean        // 是否在面包屑中隐藏
    affix?: boolean                   // 是否固定在标签页
    
    // 菜单相关
    icon?: string                     // 菜单图标
    order?: number                    // 菜单排序
    badge?: string | number           // 菜单徽章
    
    // 布局控制
    layout?: string                   // 使用的布局组件
    fullscreen?: boolean              // 是否全屏显示
    
    // 缓存控制
    noCache?: boolean                 // 禁用缓存
    cacheKey?: string                 // 自定义缓存键
    
    // 其他
    transition?: string               // 页面切换动画
    loading?: boolean                 // 是否显示加载状态
    external?: boolean                // 是否外部链接
  }
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
      description: '系统概览和数据统计',
      requiresAuth: true,
      roles: ['admin', 'user'],
      keepAlive: true,
      showInMenu: true,
      icon: 'dashboard',
      order: 1
    }
  },
  {
    path: '/user/profile',
    name: 'UserProfile',
    component: () => import('@/views/user/ProfileView.vue'),
    meta: {
      title: '个人资料',
      requiresAuth: true,
      permissions: ['user:profile:read'],
      keepAlive: false,
      showInMenu: false,
      hideInBreadcrumb: false
    }
  },
  {
    path: '/admin/users',
    name: 'AdminUsers',
    component: () => import('@/views/admin/UsersView.vue'),
    meta: {
      title: '用户管理',
      requiresAuth: true,
      roles: ['admin'],
      permissions: ['user:list'],
      showInMenu: true,
      icon: 'users',
      order: 10,
      layout: 'AdminLayout'
    }
  }
]
```

## 高级权限控制模式

### 动态路由生成

```typescript
// router/dynamicRoutes.ts
import { RouteRecordRaw } from 'vue-router'
import { useUserStore } from '@/stores/user'

/**
 * 根据用户权限动态生成路由
 */
export async function generateDynamicRoutes(): Promise<RouteRecordRaw[]> {
  const userStore = useUserStore()
  const userPermissions = userStore.userInfo?.permissions || []
  
  // 所有可能的路由配置
  const allRoutes: RouteRecordRaw[] = [
    {
      path: '/admin/users',
      name: 'AdminUsers',
      component: () => import('@/views/admin/UsersView.vue'),
      meta: {
        title: '用户管理',
        permissions: ['user:list']
      }
    },
    {
      path: '/admin/roles',
      name: 'AdminRoles',
      component: () => import('@/views/admin/RolesView.vue'),
      meta: {
        title: '角色管理',
        permissions: ['role:list']
      }
    }
    // ... 更多路由
  ]
  
  // 过滤用户有权限访问的路由
  return allRoutes.filter(route => {
    if (!route.meta?.permissions) {
      return true
    }
    
    return route.meta.permissions.some(permission =>
      userPermissions.some(userPerm => userPerm.code === permission)
    )
  })
}

/**
 * 添加动态路由到路由器
 */
export async function addDynamicRoutes(router: Router) {
  const dynamicRoutes = await generateDynamicRoutes()
  
  dynamicRoutes.forEach(route => {
    router.addRoute(route)
  })
}
```

### 权限缓存策略

```typescript
// stores/permission.ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

export const usePermissionStore = defineStore('permission', () => {
  const permissions = ref<Permission[]>([])
  const roles = ref<Role[]>([])
  const lastUpdateTime = ref<number>(0)
  
  // 权限缓存有效期（5分钟）
  const CACHE_DURATION = 5 * 60 * 1000
  
  const isCacheValid = computed(() => {
    return Date.now() - lastUpdateTime.value < CACHE_DURATION
  })
  
  /**
   * 获取用户权限（带缓存）
   */
  async function fetchPermissions(force = false) {
    if (!force && isCacheValid.value && permissions.value.length > 0) {
      return permissions.value
    }
    
    try {
      const response = await api.getUserPermissions()
      permissions.value = response.data.permissions
      roles.value = response.data.roles
      lastUpdateTime.value = Date.now()
      
      return permissions.value
    } catch (error) {
      console.error('获取权限失败:', error)
      throw error
    }
  }
  
  /**
   * 清除权限缓存
   */
  function clearCache() {
    permissions.value = []
    roles.value = []
    lastUpdateTime.value = 0
  }
  
  return {
    permissions: readonly(permissions),
    roles: readonly(roles),
    isCacheValid,
    fetchPermissions,
    clearCache
  }
})
```

## 错误处理和用户体验

### 权限错误处理

```typescript
// utils/errorHandler.ts
export function handleAuthError(error: any, router: Router) {
  switch (error.code) {
    case 401:
      // 未登录
      ElMessage.warning('登录已过期，请重新登录')
      router.push({ name: 'Login' })
      break
      
    case 403:
      // 权限不足
      ElMessage.error('权限不足，无法访问该页面')
      router.push({ name: 'Forbidden' })
      break
      
    case 404:
      // 页面不存在
      ElMessage.error('页面不存在')
      router.push({ name: 'NotFound' })
      break
      
    default:
      ElMessage.error('访问出错，请稍后重试')
      router.push({ name: 'Home' })
  }
}
```

### 加载状态管理

```typescript
// composables/usePageLoading.ts
import { ref } from 'vue'

const isLoading = ref(false)
const loadingText = ref('加载中...')

export function usePageLoading() {
  function showLoading(text = '加载中...') {
    isLoading.value = true
    loadingText.value = text
  }
  
  function hideLoading() {
    isLoading.value = false
  }
  
  return {
    isLoading: readonly(isLoading),
    loadingText: readonly(loadingText),
    showLoading,
    hideLoading
  }
}

// 全局使用
export function showPageLoading(text?: string) {
  const { showLoading } = usePageLoading()
  showLoading(text)
}

export function hidePageLoading() {
  const { hideLoading } = usePageLoading()
  hideLoading()
}
```

## 测试策略

### 路由守卫测试

```typescript
// tests/router/guards.test.ts
import { describe, it, expect, vi, beforeEach } from 'vitest'
import { createRouter, createWebHistory } from 'vue-router'
import { setupRouterGuards } from '@/router/guards'

describe('路由守卫', () => {
  let router: Router
  
  beforeEach(() => {
    router = createRouter({
      history: createWebHistory(),
      routes: [
        {
          path: '/login',
          name: 'Login',
          component: { template: '<div>Login</div>' }
        },
        {
          path: '/dashboard',
          name: 'Dashboard',
          component: { template: '<div>Dashboard</div>' },
          meta: { requiresAuth: true }
        }
      ]
    })
    
    setupRouterGuards(router)
  })
  
  it('未登录用户访问需要认证的页面应该重定向到登录页', async () => {
    // 模拟未登录状态
    vi.mocked(useUserStore).mockReturnValue({
      isLoggedIn: false
    })
    
    await router.push('/dashboard')
    
    expect(router.currentRoute.value.name).toBe('Login')
    expect(router.currentRoute.value.query.redirect).toBe('/dashboard')
  })
  
  it('已登录用户可以正常访问需要认证的页面', async () => {
    // 模拟已登录状态
    vi.mocked(useUserStore).mockReturnValue({
      isLoggedIn: true,
      userInfo: { id: '1', roles: ['user'] }
    })
    
    await router.push('/dashboard')
    
    expect(router.currentRoute.value.name).toBe('Dashboard')
  })
})
```

### 权限检查测试

```typescript
// tests/utils/auth.test.ts
import { describe, it, expect, vi } from 'vitest'
import { hasRole, hasPermission } from '@/utils/auth'

describe('权限检查工具', () => {
  it('hasRole 应该正确检查用户角色', () => {
    vi.mocked(useUserStore).mockReturnValue({
      userInfo: {
        roles: [
          { code: 'admin', name: '管理员' },
          { code: 'user', name: '用户' }
        ]
      }
    })
    
    expect(hasRole('admin')).toBe(true)
    expect(hasRole('guest')).toBe(false)
    expect(hasRole(['admin', 'user'])).toBe(true)
    expect(hasRole(['guest', 'visitor'])).toBe(false)
  })
  
  it('hasPermission 应该正确检查用户权限', () => {
    vi.mocked(useUserStore).mockReturnValue({
      userInfo: {
        permissions: [
          { code: 'user:read', name: '查看用户' },
          { code: 'user:write', name: '编辑用户' }
        ]
      }
    })
    
    expect(hasPermission('user:read')).toBe(true)
    expect(hasPermission('user:delete')).toBe(false)
    expect(hasPermission(['user:read', 'user:write'])).toBe(true)
    expect(hasPermission(['user:read', 'user:delete'])).toBe(false)
  })
})
```

## 最佳实践总结

### 1. 权限设计原则

- **最小权限原则**：用户只获得完成工作所需的最小权限
- **权限分离**：将不同类型的权限分开管理
- **动态权限**：支持运行时权限变更
- **权限继承**：合理设计角色和权限的继承关系

### 2. 性能优化

- **权限缓存**：合理缓存用户权限信息
- **懒加载**：按需加载权限相关的路由和组件
- **批量检查**：避免频繁的权限检查请求

### 3. 用户体验

- **友好提示**：权限不足时给出明确的提示信息
- **优雅降级**：部分功能不可用时提供替代方案
- **加载状态**：权限检查过程中显示适当的加载状态

### 4. 安全考虑

- **前后端一致**：前端权限检查不能替代后端验证
- **敏感信息**：避免在前端暴露敏感的权限信息
- **会话管理**：合理处理用户会话过期和权限变更