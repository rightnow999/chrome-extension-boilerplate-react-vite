# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Chrome/Firefox extension boilerplate using React + TypeScript + Vite + Turborepo monorepo architecture.
This is a fork of https://github.com/Jonghakseo/chrome-extension-boilerplate-react-vite

## Common Commands

```bash
# Development (Chrome)
pnpm dev

# Development (Firefox)
pnpm dev:firefox

# Production build
pnpm build
pnpm build:firefox

# Type checking
pnpm type-check

# Linting
pnpm lint
pnpm lint:fix

# Format code
pnpm format

# Create distribution zip
pnpm zip

# Run E2E tests
pnpm e2e

# Update extension version
pnpm update-version <version>

# Module manager (enable/disable pages)
pnpm module-manager
```

## Package Management

- **Manager**: pnpm (workspace protocol)
- **Install to root**: `pnpm i <package> -w`
- **Install to module**: `pnpm i <package> -F <module-name>`
- Module names use `@extension/*` prefix in package.json

## Architecture

### Monorepo Structure

- **`chrome-extension/`** - Extension core (manifest.ts, background script, public assets)
- **`pages/`** - Extension pages (each is a separate Vite app)
  - `popup/`, `options/`, `new-tab/` - UI pages
  - `content/`, `content-ui/`, `content-runtime/` - Content scripts
  - `devtools/`, `devtools-panel/` - DevTools integration
  - `side-panel/` - Chrome side panel
- **`packages/`** - Shared workspace packages
- **`tests/e2e/`** - End-to-end tests with WebdriverIO

### Key Packages

- **`@extension/storage`** - Chrome storage API wrappers (local/session)
- **`@extension/i18n`** - Type-safe i18n with `t()` function, manages `locales/`
- **`@extension/shared`** - Shared types, constants, hooks, components
- **`@extension/hmr`** - Custom HMR plugin for content script hot-reloading
- **`@extension/env`** - Environment variable management (`.env` support)
- **`@extension/vite-config`** - Shared Vite configuration
- **`@extension/tsconfig`** - Shared TypeScript configs
- **`@extension/ui`** - Shared UI components with Tailwind
- **`zipper`** - Creates distribution zips with timestamp
- **`module-manager`** - Enable/disable unused pages (archives to `/archive`)

### Build System

- **Turborepo** - Orchestrates builds with task dependencies
- **Vite** - Bundles each page module independently
- **Output**: All bundles go to `dist/` directory
- **Manifest**: Generated from `chrome-extension/manifest.ts`

### Manifest Configuration

- **File**: `chrome-extension/manifest.ts`
- **Content scripts**: Auto-injected based on enabled modules
- **Permissions**: Defined in manifest, parsed by build system
- **Firefox support**: Set `CLI_CEB_FIREFOX=true` env var

## Development Notes

- **Hot Reload**: Custom HMR for content scripts uses WebSocket connection
- **Environment**: Use `.env` file, accessed via `@extension/env`
- **i18n**: Add keys to all locale files in `packages/i18n/locales/`
- **Module Manager**: Archives unused pages to `/archive`, can recover later
- **Node version**: Must match `.nvmrc` (>=22.15.1)

## TypeScript Configuration

- **Shared configs**: `packages/tsconfig/` (base, module, app)
- **Project references**: Enabled for faster builds
- **Type imports**: Enforced via ESLint (`@typescript-eslint/consistent-type-imports`)

## Testing

- **E2E**: WebdriverIO in `tests/e2e/`
- **Run**: `pnpm e2e` (builds zip first)
- **Target**: Tests run on actual browsers via the built zip
