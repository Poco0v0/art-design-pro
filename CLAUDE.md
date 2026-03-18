# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

本项目是一个类 RuoYi 的通用管理端全栈项目的管理 ui 端，后端基于 Java 21 + Spring Boot 3.5，前端 UI 基于 [art-design-pro](https://github.com/Daymychen/art-design-pro)（Vue 3 + TypeScript）。

## Tech Stack

**Frontend**: Vue 3, TypeScript, Vite 7, TailwindCSS 4, Element Plus, Pinia, ECharts, Vue-i18n, Axios

## Build & Dev Commands

```bash
# Install dependencies
pnpm install

# Dev server (opens browser, port 3006, proxies /api to localhost:8080)
pnpm dev

# Production build (runs vue-tsc type check first)
pnpm build

# Preview production build
pnpm serve

# Lint
pnpm lint          # ESLint check
pnpm fix           # ESLint auto-fix
pnpm lint:prettier # Prettier format all
pnpm lint:stylelint # StyleLint fix
```

## Prerequisites

- Node.js >= 20.19.0, pnpm >= 8.8.0

## Architecture

基于 art-design-pro 的定制版本，基于 Vue 3 Composition API + TypeScript。

关键目录:

- `src/api/` — API 接口定义 (auth, system-manage)
- `src/components/core/` — 通用组件库 (banners, cards, charts, forms, tables, layouts 等)
- `src/components/business/` — 业务组件
- `src/hooks/core/` — Vue Composables (useAuth, useTable, useTheme, useChart 等)
- `src/router/core/` — 路由核心：ComponentLoader, MenuProcessor, RouteRegistry, RouteTransformer
- `src/router/guards/` — 全局路由守卫 (beforeEach, afterEach)
- `src/router/modules/` — 路由模块按功能划分 (dashboard, system, article 等)
- `src/store/modules/` — Pinia stores (user, menu, setting, table, worktab)，部分带持久化
- `src/utils/http/` — Axios 封装及错误处理
- `src/utils/storage/` — 本地存储封装

## Key Patterns

- **路由模式**: 支持后端驱动路由 (`VITE_ACCESS_MODE=backend`)，动态路由在 `router/routes/asyncRoutes.ts`
- **组件自动导入**: unplugin-auto-import + unplugin-vue-components (Element Plus resolver)
- **Vite 路径别名**: `@/` → `src/`, `@views/` → `src/views/`, `@utils/` → `src/utils/`, `@stores/` → `src/store/`
- **API 代理**: 开发环境 `/api` 代理到 `http://localhost:8080`
- **国际化**: vue-i18n，语言包在 `src/locales/langs/` (en.json, zh.json)

## Code Style

- ESLint + Prettier + StyleLint，Husky pre-commit hooks 自动执行
- CommitLint 校验提交信息格式: `<type>(<scope>): <description>`
- 允许的 type: feat, fix, docs, style, refactor, perf, test, build, ci, revert, chore, wip
- 单引号，无分号，2 空格缩进

## Key Libraries Reference

- [art-design-pro](https://github.com/Daymychen/art-design-pro) — 前端 UI 基础模板
