# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a comprehensive Vue.js frontend development standards documentation repository containing detailed guidelines and best practices for building modern Vue applications. The repository serves as a complete reference guide for teams adopting Vue 3, TypeScript, and modern frontend tooling.

## Key Architecture & Structure

### Documentation Organization
The repository is organized as a collection of markdown documentation files covering all aspects of Vue frontend development:

- **Core Development**: Component structure, props/events, lifecycle management (01-04)
- **Language Standards**: JavaScript/TypeScript patterns, type definitions, async programming (05-07)
- **Template & Styling**: HTML templates, CSS standards, styling methodologies (08-09)
- **Tooling Configuration**: ESLint 9, Prettier, build tools configuration (10-12)
- **State Management**: Pinia stores and state patterns (13-14)
- **Routing**: Vue Router configuration and guards (15-16)
- **Workflow**: Git standards, branch management, code review processes (17-19)
- **Performance & Quality**: Optimization strategies, testing standards, error handling (20-25)

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

Since this is a documentation-only repository, there are no executable commands. However, the documentation provides comprehensive command templates for Vue projects:

### Linting and Code Quality (from documentation)
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

### Code Formatting (from documentation)
```bash
# Format code with Prettier
npm run format

# Check formatting without changing files
npm run format:check
```

### Testing (from documentation)
```bash
# Run unit tests
npm run test:unit

# Run tests with coverage
npm run test:coverage

# Run tests in UI mode
npm run test:ui
```

### Building and Type Checking (from documentation)
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

## Key Documentation Files

### Quick Reference
- **README.md**: Main overview with quick start guide and navigation
- **QUICK_START.md**: 5-minute setup guide for new projects
- **INDEX.md**: Multi-dimensional navigation and quick lookup
- **TABLE_OF_CONTENTS.md**: Complete document index
- **EXAMPLES.md**: Complete code examples and templates
- **TEMPLATES.md**: Configuration file templates

### Core Standards
- **01-project-structure.md**: Standard directory organization and file naming
- **02-vue-component-structure.md**: Component templates and script setup patterns
- **10-eslint-configuration.md**: ESLint 9 configuration with Vue official flat config
- **13-pinia-store-structure.md**: State management patterns
- **25-code-quality-checklist.md**: Quality assurance guidelines

## Working with This Repository

### Understanding the Structure
This repository contains 25 numbered documentation files covering different aspects of Vue development, plus several reference files (README, INDEX, etc.). Each file follows a consistent structure with:

- Clear explanations of standards
- Code examples and templates
- Best practices and anti-patterns
- Configuration templates

### Common Tasks

#### Finding Specific Information
1. Check **INDEX.md** for quick topic lookup
2. Use **TABLE_OF_CONTENTS.md** for detailed navigation
3. Search for specific patterns using grep/search tools

#### Understanding Vue Standards
1. Start with **README.md** for overview
2. Follow **QUICK_START.md** for practical setup
3. Reference specific numbered files for detailed standards
4. Use **EXAMPLES.md** for complete implementation examples

#### Configuration Templates
- ESLint 9 config: **10-eslint-configuration.md**
- Prettier config: **11-prettier-configuration.md**
- Build tools: **12-build-tool-configuration.md**
- All templates: **TEMPLATES.md**

## File Organization Standards

### Naming Conventions
- Numbered files (01-25): Core standards in logical progression
- README.md: Main entry point with overview
- INDEX.md: Multi-dimensional navigation
- QUICK_START.md: Fast setup guide
- EXAMPLES.md: Complete code examples
- TEMPLATES.md: Configuration templates
- TABLE_OF_CONTENTS.md: Detailed file listing

### Content Structure
Each documentation file typically includes:
1. Overview and objectives
2. Standards and guidelines  
3. Code examples and templates
4. Best practices
5. Common pitfalls to avoid
6. Related resources

## ESLint 9 and Modern Tooling Focus

This documentation emphasizes modern Vue development with:
- **ESLint 9**: Flat configuration format with Vue official configs
- **Oxlint Integration**: High-performance Rust-based linting
- **TypeScript**: Strict type checking patterns
- **Vue 3**: Composition API and script setup syntax
- **Modern Build Tools**: Vite configuration and optimization

## Important Notes

1. **Documentation-Only Repository**: This contains no executable code, only comprehensive documentation
2. **Reference Material**: Designed as a complete style guide for Vue teams
3. **Modern Standards**: Focuses on Vue 3, TypeScript, ESLint 9, and current best practices
4. **Template Source**: Provides copy-paste templates and configurations for real projects
5. **Chinese + English**: Some documents contain Chinese content with English technical terms

## Usage Workflow

When working with this repository:
1. **For Overview**: Start with README.md
2. **For Quick Setup**: Use QUICK_START.md  
3. **For Specific Topics**: Use INDEX.md to navigate to relevant files
4. **For Examples**: Reference EXAMPLES.md and TEMPLATES.md
5. **For Implementation**: Copy templates and adapt to specific projects

This repository serves as the authoritative source for Vue frontend development standards and should be referenced when setting up new projects or establishing team coding guidelines.