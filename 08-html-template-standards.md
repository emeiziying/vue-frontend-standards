# HTML模板编写规范

## 概述

本文档定义了Vue项目中HTML模板的编写标准，包括Vue模板语法使用规范、指令使用最佳实践、条件渲染和列表渲染规范，以及插槽使用指南。遵循这些规范可以确保模板代码的一致性、可读性和可维护性。

## Vue模板语法使用标准

### 基础语法规范

#### 插值表达式
```vue
<template>
  <!-- ✅ 推荐：简洁的插值表达式 -->
  <h1>{{ title }}</h1>
  <p>{{ user.name }}</p>
  
  <!-- ✅ 推荐：使用计算属性处理复杂逻辑 -->
  <span>{{ formattedDate }}</span>
  
  <!-- ❌ 避免：在模板中使用复杂表达式 -->
  <span>{{ new Date(timestamp).toLocaleDateString('zh-CN') }}</span>
</template>
```

#### 属性绑定
```vue
<template>
  <!-- ✅ 推荐：使用v-bind简写语法 -->
  <img :src="imageUrl" :alt="imageAlt">
  <button :disabled="isLoading" :class="buttonClass">
    {{ buttonText }}
  </button>
  
  <!-- ✅ 推荐：布尔属性的正确使用 -->
  <input :required="isRequired" :readonly="isReadonly">
  
  <!-- ❌ 避免：不必要的字符串插值 -->
  <div :id="`user-${userId}`">
    <!-- 应该使用计算属性 -->
  </div>
</template>
```

#### 事件监听
```vue
<template>
  <!-- ✅ 推荐：使用@简写语法 -->
  <button @click="handleClick">点击</button>
  <input @input="handleInput" @blur="handleBlur">
  
  <!-- ✅ 推荐：传递参数 -->
  <button @click="handleDelete(item.id)">删除</button>
  
  <!-- ✅ 推荐：使用事件修饰符 -->
  <form @submit.prevent="handleSubmit">
    <input @keyup.enter="handleEnter">
  </form>
</template>
```

### 模板结构规范

#### 元素嵌套
```vue
<template>
  <!-- ✅ 推荐：合理的嵌套层级 -->
  <div class="user-card">
    <header class="user-card__header">
      <h2 class="user-card__title">{{ user.name }}</h2>
    </header>
    <main class="user-card__content">
      <p class="user-card__description">{{ user.description }}</p>
    </main>
  </div>
  
  <!-- ❌ 避免：过深的嵌套层级 -->
  <div>
    <div>
      <div>
        <div>
          <span>内容</span>
        </div>
      </div>
    </div>
  </div>
</template>
```

#### 属性顺序
```vue
<template>
  <!-- ✅ 推荐：按照优先级排列属性 -->
  <input
    id="username"
    ref="usernameRef"
    v-model="username"
    :class="inputClass"
    :disabled="isDisabled"
    type="text"
    placeholder="请输入用户名"
    @input="handleInput"
    @blur="handleBlur"
  >
</template>
```

**属性顺序优先级：**
1. `id`、`ref`
2. `v-model`、`v-if`、`v-for`等指令
3. `:class`、`:style`等样式相关
4. 其他动态属性
5. 静态属性
6. 事件监听器

## 指令使用最佳实践

### v-if 和 v-show 使用规范

#### 选择原则
```vue
<template>
  <!-- ✅ 推荐：频繁切换使用v-show -->
  <div v-show="isVisible" class="tooltip">
    提示信息
  </div>
  
  <!-- ✅ 推荐：条件很少改变使用v-if -->
  <div v-if="hasPermission" class="admin-panel">
    管理面板
  </div>
  
  <!-- ✅ 推荐：互斥条件使用v-if/v-else -->
  <div v-if="isLoading" class="loading">
    加载中...
  </div>
  <div v-else-if="hasError" class="error">
    {{ errorMessage }}
  </div>
  <div v-else class="content">
    {{ content }}
  </div>
</template>
```

#### 性能考虑
```vue
<template>
  <!-- ✅ 推荐：将v-if提升到更高层级 -->
  <template v-if="showUserInfo">
    <div class="user-avatar">
      <img :src="user.avatar" :alt="user.name">
    </div>
    <div class="user-details">
      <h3>{{ user.name }}</h3>
      <p>{{ user.email }}</p>
    </div>
  </template>
  
  <!-- ❌ 避免：在每个子元素上使用v-if -->
  <div v-if="showUserInfo" class="user-avatar">
    <img :src="user.avatar" :alt="user.name">
  </div>
  <div v-if="showUserInfo" class="user-details">
    <h3>{{ user.name }}</h3>
    <p>{{ user.email }}</p>
  </div>
</template>
```

### v-model 使用规范

#### 基础用法
```vue
<template>
  <!-- ✅ 推荐：表单元素的双向绑定 -->
  <input v-model="username" type="text" placeholder="用户名">
  <textarea v-model="description" placeholder="描述"></textarea>
  <select v-model="selectedOption">
    <option value="">请选择</option>
    <option v-for="option in options" :key="option.value" :value="option.value">
      {{ option.label }}
    </option>
  </select>
  
  <!-- ✅ 推荐：复选框和单选框 -->
  <input v-model="isChecked" type="checkbox" id="agree">
  <label for="agree">同意条款</label>
  
  <input v-model="selectedRadio" type="radio" value="option1" id="radio1">
  <label for="radio1">选项1</label>
</template>
```

#### 修饰符使用
```vue
<template>
  <!-- ✅ 推荐：使用适当的修饰符 -->
  <input v-model.trim="username" type="text">
  <input v-model.number="age" type="number">
  <input v-model.lazy="description" type="text">
</template>
```

### 其他指令规范

#### v-bind 和 v-on 简写
```vue
<template>
  <!-- ✅ 推荐：使用简写语法 -->
  <img :src="imageUrl" @click="handleClick">
  
  <!-- ❌ 避免：使用完整语法（除非为了清晰性） -->
  <img v-bind:src="imageUrl" v-on:click="handleClick">
</template>
```

#### v-text 和 v-html 使用
```vue
<template>
  <!-- ✅ 推荐：简单文本使用插值 -->
  <span>{{ message }}</span>
  
  <!-- ✅ 推荐：防止闪烁使用v-text -->
  <span v-text="message"></span>
  
  <!-- ⚠️ 谨慎：使用v-html时确保内容安全 -->
  <div v-html="sanitizedHtml"></div>
</template>
```

## 条件渲染和列表渲染规范

### 条件渲染最佳实践

#### 复杂条件逻辑
```vue
<template>
  <!-- ✅ 推荐：使用计算属性处理复杂条件 -->
  <div v-if="shouldShowContent" class="content">
    {{ content }}
  </div>
  
  <!-- ✅ 推荐：使用template包装多个元素 -->
  <template v-if="isAuthenticated">
    <nav class="user-nav">
      <a href="/profile">个人资料</a>
      <a href="/settings">设置</a>
    </nav>
    <button @click="logout">退出</button>
  </template>
</template>

<script setup lang="ts">
import { computed } from 'vue'

const shouldShowContent = computed(() => {
  return user.value && user.value.hasPermission && !isLoading.value
})
</script>
```

#### 条件渲染性能优化
```vue
<template>
  <!-- ✅ 推荐：避免不必要的重新渲染 -->
  <div v-if="expensiveCondition" :key="itemId">
    <ExpensiveComponent :data="data" />
  </div>
  
  <!-- ✅ 推荐：使用key确保正确的组件更新 -->
  <UserProfile v-if="currentUser" :key="currentUser.id" :user="currentUser" />
</template>
```

### 列表渲染最佳实践

#### v-for 基础用法
```vue
<template>
  <!-- ✅ 推荐：始终使用key -->
  <ul>
    <li v-for="item in items" :key="item.id">
      {{ item.name }}
    </li>
  </ul>
  
  <!-- ✅ 推荐：对象遍历 -->
  <div v-for="(value, key) in userInfo" :key="key">
    <strong>{{ key }}:</strong> {{ value }}
  </div>
  
  <!-- ✅ 推荐：数组索引遍历 -->
  <div v-for="(item, index) in items" :key="item.id">
    {{ index + 1 }}. {{ item.name }}
  </div>
</template>
```

#### key 的正确使用
```vue
<template>
  <!-- ✅ 推荐：使用唯一且稳定的key -->
  <div v-for="user in users" :key="user.id">
    <UserCard :user="user" />
  </div>
  
  <!-- ✅ 推荐：组合key确保唯一性 -->
  <div v-for="item in groupedItems" :key="`${item.category}-${item.id}`">
    {{ item.name }}
  </div>
  
  <!-- ❌ 避免：使用数组索引作为key（除非列表是静态的） -->
  <div v-for="(item, index) in items" :key="index">
    {{ item.name }}
  </div>
</template>
```

#### 列表性能优化
```vue
<template>
  <!-- ✅ 推荐：使用虚拟滚动处理大列表 -->
  <VirtualList :items="largeList" :item-height="50">
    <template #default="{ item }">
      <div class="list-item">{{ item.name }}</div>
    </template>
  </VirtualList>
  
  <!-- ✅ 推荐：避免在v-for中使用v-if -->
  <div v-for="user in activeUsers" :key="user.id">
    <UserCard :user="user" />
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'

// ✅ 推荐：使用计算属性过滤列表
const activeUsers = computed(() => {
  return users.value.filter(user => user.isActive)
})
</script>
```

#### 嵌套列表处理
```vue
<template>
  <!-- ✅ 推荐：嵌套列表的正确结构 -->
  <div v-for="category in categories" :key="category.id" class="category">
    <h3>{{ category.name }}</h3>
    <ul>
      <li v-for="item in category.items" :key="`${category.id}-${item.id}`">
        {{ item.name }}
      </li>
    </ul>
  </div>
</template>
```

## 插槽使用指南

### 基础插槽

#### 默认插槽
```vue
<!-- 父组件 -->
<template>
  <Card>
    <h2>卡片标题</h2>
    <p>卡片内容</p>
  </Card>
</template>

<!-- Card.vue 组件 -->
<template>
  <div class="card">
    <div class="card-body">
      <slot></slot>
    </div>
  </div>
</template>
```

#### 具名插槽
```vue
<!-- 父组件 -->
<template>
  <Modal>
    <template #header>
      <h2>确认删除</h2>
    </template>
    
    <template #default>
      <p>确定要删除这个项目吗？此操作不可撤销。</p>
    </template>
    
    <template #footer>
      <button @click="cancel">取消</button>
      <button @click="confirm" class="danger">删除</button>
    </template>
  </Modal>
</template>

<!-- Modal.vue 组件 -->
<template>
  <div class="modal">
    <header class="modal-header">
      <slot name="header"></slot>
    </header>
    
    <main class="modal-body">
      <slot></slot>
    </main>
    
    <footer class="modal-footer">
      <slot name="footer"></slot>
    </footer>
  </div>
</template>
```

### 作用域插槽

#### 基础作用域插槽
```vue
<!-- 父组件 -->
<template>
  <UserList>
    <template #default="{ user, index }">
      <div class="user-item">
        <span class="user-number">{{ index + 1 }}</span>
        <img :src="user.avatar" :alt="user.name" class="user-avatar">
        <span class="user-name">{{ user.name }}</span>
        <span class="user-email">{{ user.email }}</span>
      </div>
    </template>
  </UserList>
</template>

<!-- UserList.vue 组件 -->
<template>
  <div class="user-list">
    <div v-for="(user, index) in users" :key="user.id">
      <slot :user="user" :index="index"></slot>
    </div>
  </div>
</template>

<script setup lang="ts">
interface User {
  id: number
  name: string
  email: string
  avatar: string
}

defineProps<{
  users: User[]
}>()
</script>
```

#### 高级作用域插槽模式
```vue
<!-- 父组件 -->
<template>
  <DataTable :data="tableData" :loading="isLoading">
    <!-- 自定义列渲染 -->
    <template #column-name="{ row }">
      <strong>{{ row.name }}</strong>
    </template>
    
    <template #column-status="{ row }">
      <span :class="getStatusClass(row.status)">
        {{ row.status }}
      </span>
    </template>
    
    <template #column-actions="{ row }">
      <button @click="editRow(row)">编辑</button>
      <button @click="deleteRow(row)" class="danger">删除</button>
    </template>
    
    <!-- 空状态 -->
    <template #empty>
      <div class="empty-state">
        <p>暂无数据</p>
        <button @click="loadData">重新加载</button>
      </div>
    </template>
    
    <!-- 加载状态 -->
    <template #loading>
      <div class="loading-state">
        <span>数据加载中...</span>
      </div>
    </template>
  </DataTable>
</template>

<!-- DataTable.vue 组件 -->
<template>
  <div class="data-table">
    <div v-if="loading" class="table-loading">
      <slot name="loading">
        <div>加载中...</div>
      </slot>
    </div>
    
    <div v-else-if="data.length === 0" class="table-empty">
      <slot name="empty">
        <div>暂无数据</div>
      </slot>
    </div>
    
    <table v-else class="table">
      <thead>
        <tr>
          <th v-for="column in columns" :key="column.key">
            {{ column.title }}
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="row in data" :key="row.id">
          <td v-for="column in columns" :key="column.key">
            <slot :name="`column-${column.key}`" :row="row" :column="column">
              {{ row[column.key] }}
            </slot>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
```

### 插槽最佳实践

#### 插槽命名规范
```vue
<template>
  <!-- ✅ 推荐：使用描述性的插槽名称 -->
  <Card>
    <template #header>头部内容</template>
    <template #content>主要内容</template>
    <template #footer>底部内容</template>
  </Card>
  
  <!-- ✅ 推荐：使用kebab-case命名 -->
  <DataList>
    <template #list-item="{ item }">
      {{ item.name }}
    </template>
    <template #empty-state>
      暂无数据
    </template>
  </DataList>
</template>
```

#### 插槽默认内容
```vue
<!-- 组件定义 -->
<template>
  <div class="notification">
    <div class="notification-icon">
      <slot name="icon">
        <!-- 默认图标 -->
        <svg>...</svg>
      </slot>
    </div>
    
    <div class="notification-content">
      <slot>
        <!-- 默认内容 -->
        <p>这是一条通知消息</p>
      </slot>
    </div>
    
    <div class="notification-actions">
      <slot name="actions">
        <!-- 默认操作按钮 -->
        <button @click="close">关闭</button>
      </slot>
    </div>
  </div>
</template>
```

#### 条件插槽渲染
```vue
<template>
  <div class="component">
    <!-- ✅ 推荐：检查插槽是否存在 -->
    <header v-if="$slots.header" class="component-header">
      <slot name="header"></slot>
    </header>
    
    <main class="component-main">
      <slot></slot>
    </main>
    
    <footer v-if="$slots.footer" class="component-footer">
      <slot name="footer"></slot>
    </footer>
  </div>
</template>

<script setup lang="ts">
import { useSlots } from 'vue'

const slots = useSlots()

// 也可以在脚本中检查插槽
const hasHeaderSlot = computed(() => !!slots.header)
</script>
```

## 模板性能优化

### 避免不必要的重新渲染
```vue
<template>
  <!-- ✅ 推荐：使用v-memo优化列表渲染 -->
  <div v-for="item in list" :key="item.id" v-memo="[item.id, item.name]">
    <ExpensiveComponent :item="item" />
  </div>
  
  <!-- ✅ 推荐：使用v-once渲染静态内容 -->
  <div v-once>
    <h1>{{ title }}</h1>
    <p>{{ staticDescription }}</p>
  </div>
</template>
```

### 模板编译优化
```vue
<template>
  <!-- ✅ 推荐：避免内联对象和数组 -->
  <Component :config="componentConfig" :items="itemList" />
  
  <!-- ❌ 避免：内联对象会导致不必要的重新渲染 -->
  <Component :config="{ theme: 'dark', size: 'large' }" />
</template>

<script setup lang="ts">
// ✅ 推荐：在setup中定义稳定的引用
const componentConfig = { theme: 'dark', size: 'large' }
const itemList = ref([])
</script>
```

## 常见错误和解决方案

### 模板语法错误
```vue
<template>
  <!-- ❌ 错误：在模板中使用复杂逻辑 -->
  <div>{{ items.filter(item => item.active).map(item => item.name).join(', ') }}</div>
  
  <!-- ✅ 正确：使用计算属性 -->
  <div>{{ activeItemNames }}</div>
</template>

<script setup lang="ts">
const activeItemNames = computed(() => {
  return items.value
    .filter(item => item.active)
    .map(item => item.name)
    .join(', ')
})
</script>
```

### 事件处理错误
```vue
<template>
  <!-- ❌ 错误：在模板中定义函数 -->
  <button @click="() => handleClick(item.id)">点击</button>
  
  <!-- ✅ 正确：使用方法或内联处理 -->
  <button @click="handleClick(item.id)">点击</button>
  
  <!-- ✅ 正确：使用事件对象 -->
  <input @input="handleInput($event)" />
</template>
```

### 条件渲染错误
```vue
<template>
  <!-- ❌ 错误：v-for和v-if在同一元素上 -->
  <div v-for="item in items" v-if="item.visible" :key="item.id">
    {{ item.name }}
  </div>
  
  <!-- ✅ 正确：使用template或计算属性 -->
  <template v-for="item in items" :key="item.id">
    <div v-if="item.visible">
      {{ item.name }}
    </div>
  </template>
  
  <!-- ✅ 更好：使用计算属性过滤 -->
  <div v-for="item in visibleItems" :key="item.id">
    {{ item.name }}
  </div>
</template>
```

## 总结

遵循这些HTML模板编写规范可以：

1. **提高代码可读性**：统一的语法和结构让代码更易理解
2. **增强可维护性**：规范的命名和组织方式便于后期维护
3. **优化性能**：正确使用指令和优化技巧提升应用性能
4. **减少错误**：避免常见的模板语法错误和反模式
5. **提升开发效率**：标准化的开发模式加快开发速度

建议开发团队定期review模板代码，确保遵循这些规范，并根据项目需要适当调整和扩展规范内容。