# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a comprehensive Vue.js frontend development standards repository containing detailed documentation and best practices for building modern Vue applications. The repository serves as a complete style guide and reference for teams adopting Vue 3, TypeScript, and modern frontend tooling.

## Key Architecture & Structure

### Documentation Organization
The repository follows a well-organized documentation structure under `vue-frontend-standards/`:

- **Core Development**: Component structure, props/events, lifecycle management
- **Language Standards**: JavaScript/TypeScript patterns, type definitions, async programming
- **Tooling Configuration**: ESLint 9, Prettier, build tools (Vite)
- **State Management**: Pinia stores and patterns
- **Routing**: Vue Router configuration and guards
- **Quality Assurance**: Testing, error handling, code review processes
- **Performance**: Optimization strategies for Vue apps and builds
- **Workflow**: Git standards, branch management, CI/CD practices

### Technology Stack Standards
- **Framework**: Vue 3 with Composition API (script setup syntax)
- **Language**: TypeScript with strict type checking
- **Build Tool**: Vite for development and production builds
- **Styling**: CSS with BEM methodology + TailwindCSS integration
- **State Management**: Pinia (Vue's official state management)
- **Routing**: Vue Router 4
- **Testing**: Vitest + Vue Test Utils
- **Code Quality**: ESLint 9 + Prettier + oxlint for performance

## Development Commands

### Linting and Code Quality
```bash
# Fast error checking with oxlint (recommended for development)
npm run lint:oxlint

# Comprehensive ESLint checking (recommended for pre-commit)
npm run lint:eslint

# Run all linting tools in sequence
npm run lint

# Auto-fix code issues
npm run lint:fix
```

### Code Formatting
```bash
# Format code with Prettier
npm run format

# Check formatting without changing files
npm run format:check
```

### Testing
```bash
# Run unit tests
npm run test:unit

# Run tests with coverage
npm run test:coverage

# Run tests in UI mode
npm run test:ui
```

### Building and Type Checking
```bash
# Type checking with TypeScript
npm run type-check

# Build for production
npm run build

# Build for development
npm run build:dev

# Preview production build
npm run preview
```

## Vue Component Development Standards

### Component Structure Template
```vue
<template>
  <div class="component-name">
    <!-- Use BEM naming convention for CSS classes -->
    <header v-if="showHeader" class="component-name__header">
      <h2 class="component-name__title">{{ title }}</h2>
    </header>
    
    <main class="component-name__content">
      <slot name="content">
        <p class="component-name__default-text">Default content</p>
      </slot>
    </main>
  </div>
</template>

<script setup lang="ts">
// 1. Imports (Vue APIs first, then third-party, then local)
import { ref, computed, onMounted } from 'vue'
import type { User } from '@/types/user'

// 2. Props interface and definition
interface Props {
  title: string
  count?: number
  user?: User
}

const props = withDefaults(defineProps<Props>(), {
  count: 0,
  user: undefined
})

// 3. Emits interface and definition
interface Emits {
  update: [value: string]
  change: [event: Event]
}

const emit = defineEmits<Emits>()

// 4. Reactive state
const isLoading = ref(false)

// 5. Computed properties
const displayTitle = computed(() => props.title.toUpperCase())

// 6. Methods
const handleClick = () => {
  emit('update', 'new value')
}

// 7. Lifecycle hooks
onMounted(() => {
  // Component initialization
})
</script>

<style scoped>
/* Use BEM methodology for CSS classes */
.component-name {
  /* Layout properties first */
  display: flex;
  flex-direction: column;
  
  /* Size properties */
  width: 100%;
  padding: 1rem;
  
  /* Appearance properties */
  background-color: #ffffff;
  border: 1px solid #e5e7eb;
  border-radius: 0.5rem;
}

.component-name__header {
  margin-bottom: 1rem;
}

.component-name__title {
  font-size: 1.25rem;
  font-weight: 600;
  color: #1f2937;
  margin: 0;
}
</style>
```

### Key Development Patterns

1. **Script Setup Syntax**: Always use `<script setup lang="ts">` for new components
2. **TypeScript Types**: Define explicit interfaces for Props and Emits
3. **BEM CSS**: Use Block__Element--Modifier naming for CSS classes
4. **Composition API**: Prefer composables over mixins for reusable logic

## File Organization Standards

### Directory Structure
```
src/
├── components/
│   ├── base/              # Basic UI components (BaseButton, BaseInput)
│   ├── common/            # Reusable business components
│   └── layout/            # Layout components (Header, Sidebar)
├── views/                 # Page components
├── stores/                # Pinia stores
│   └── modules/           # Store modules by feature
├── composables/           # Composition functions
├── utils/                 # Utility functions
│   ├── helpers/           # Helper functions
│   ├── validators/        # Validation functions
│   └── constants/         # Constants
├── services/              # API services
│   ├── api/               # API endpoints
│   └── http/              # HTTP client configuration
├── types/                 # TypeScript type definitions
└── assets/                # Static assets
```

### File Naming Conventions
- **Components**: PascalCase (UserProfile.vue, ProductCard.vue)
- **Pages**: PascalCase + Page suffix (UserListPage.vue)
- **Composables**: camelCase with 'use' prefix (useUser.ts, useAuth.ts)
- **Utils**: camelCase (formatUtils.ts, apiHelpers.ts)
- **Types**: camelCase + .types.ts (user.types.ts, api.types.ts)

## ESLint Configuration (v9)

The project uses ESLint 9 with Vue official flat configuration:

### Key Rules Enforced
- Vue component naming must use PascalCase
- TypeScript strict mode with explicit types
- No unused variables (prefix with _ to ignore)
- Console statements warned in production
- Consistent code formatting via Prettier integration

### Performance Optimization
- **oxlint**: Ultra-fast Rust-based linter for basic error checking
- **ESLint**: Comprehensive style and best practice checking
- Use `npm run lint:oxlint` for quick feedback during development
- Use `npm run lint:eslint` for thorough checking before commits

## Testing Standards

### Test Structure
```typescript
// UserProfile.test.ts
import { describe, it, expect, beforeEach } from 'vitest'
import { mount } from '@vue/test-utils'
import UserProfile from '@/components/UserProfile.vue'

describe('UserProfile', () => {
  let wrapper

  beforeEach(() => {
    wrapper = mount(UserProfile, {
      props: {
        user: { name: 'John Doe', email: 'john@example.com' }
      }
    })
  })

  describe('rendering', () => {
    it('should display user information correctly', () => {
      expect(wrapper.find('[data-testid="user-name"]').text()).toBe('John Doe')
    })
  })

  describe('interactions', () => {
    it('should emit edit event when edit button clicked', async () => {
      await wrapper.find('[data-testid="edit-button"]').trigger('click')
      expect(wrapper.emitted('edit')).toBeTruthy()
    })
  })
})
```

### Coverage Requirements
- Core business logic: 90%+ coverage
- Utility functions: 95%+ coverage  
- Vue components: 80%+ coverage
- Always use `data-testid` attributes for test selectors

## State Management with Pinia

### Store Structure
```typescript
// stores/modules/user.ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

export const useUserStore = defineStore('user', () => {
  // State
  const user = ref<User | null>(null)
  const isLoading = ref(false)

  // Getters
  const isLoggedIn = computed(() => !!user.value)

  // Actions
  const login = async (credentials: LoginCredentials) => {
    isLoading.value = true
    try {
      const response = await userApi.login(credentials)
      user.value = response.data
    } finally {
      isLoading.value = false
    }
  }

  return { user, isLoading, isLoggedIn, login }
})
```

## Important Development Notes

1. **Always run linting before commits**: Use `npm run lint` to catch issues early
2. **Type safety**: Leverage TypeScript strictly - avoid `any` types
3. **Component composition**: Keep components focused and composable
4. **Performance**: Use Vue's built-in optimizations (v-memo, v-once when appropriate)
5. **Accessibility**: Include proper ARIA attributes and semantic HTML
6. **Mobile-first**: Design responsive layouts from mobile up

## Git Workflow

### Commit Message Format
```
type: brief description

feat: add user authentication feature
fix: resolve navigation menu overflow issue  
docs: update component documentation
style: format code with prettier
refactor: simplify user store logic
test: add unit tests for UserCard component
```

### Branch Naming
- `feature/user-authentication`
- `fix/navigation-overflow`
- `docs/update-readme`

This repository represents a production-ready Vue development standard that emphasizes type safety, performance, maintainability, and modern best practices.