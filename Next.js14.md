# Next.js Setup Template

# Next.js14(App router)のセットアップテンプレート

## Next.jsプロジェクトの作成

```bash
bunx create-next-app@latest
```

## ESLintとPrettierの設定

最小限のリンター設定を行う

### ライブラリのインストール

```bash
bun add -D @typescript-eslint/eslint-plugin eslint-config-prettier eslint-plugin-simple-import-sort prettier npm-run-all prettier-plugin-tailwindcss
```

### ESLintの設定

`.eslintrc.json`
```json
{
  "extends": ["next/core-web-vitals", "prettier"],
  "plugins": ["@typescript-eslint", "simple-import-sort", "import"],
  "rules": {
    "simple-import-sort/imports": "error",
    "simple-import-sort/exports": "error",
    "import/first": "error",
    "import/newline-after-import": "error",
    "import/no-duplicates": "error",
    "@typescript-eslint/consistent-type-imports": "error"
  }
}
```

### Prettierの設定
`.prettierignore`
```
node_modules
.next
bun.lockb
public
```

`.prettierrc`
```json
{
  "semi": false,
  "singleQuote": true,
  "plugins": ["prettier-plugin-tailwindcss"]
}
```

### **npm-scripts**の設定

```json
{
  ...

  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    /// ▼ ▼ ▼
    "lint": "run-p -l -c --aggregate-output lint:*",
    "lint:eslint": "next lint",
    "lint:prettier": "prettier --check .",
    "format": "run-s format:prettier format:eslint",
    "format:eslint": "bun run lint:eslint --fix",
    "format:prettier": "bun run lint:prettier --write",
    "typecheck": "tsc"
    /// ▲ ▲ ▲
  },

  ...
}
```

## Huskyの設定

### インストール

Husky

```bash
bunx husky-init && bun install
```

lint-staged

```bash
bun i -D lint-staged
```

commitlint

```bash
bun i -D  @commitlint/cli @commitlint/config-conventional
```

```bash
echo "module.exports = {extends: ['@commitlint/config-conventional']}" > commitlint.config.js
```

### 設定

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

bun commitlint --edit "$1"
```

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

bun lint-staged
bun run build
```

`.lintstagedrc`
```bash
{
  "**/*.{js,jsx,ts,tsx}": ["prettier --write", "eslint --fix"]
}
```

## Storybookの設定

### Storybookインストール

```bash
bunx storybook@next init
```

### 設定

```bash
bun add -D tsconfig-paths-webpack-plugin
```

```tsx
import type { StorybookConfig } from '@storybook/nextjs'
import TsconfigPathsPlugin from 'tsconfig-paths-webpack-plugin'

const config: StorybookConfig = {
  stories: ['../src/**/*.mdx', '../src/**/*.stories.@(js|jsx|mjs|ts|tsx)'],
  addons: [
    '@storybook/addon-onboarding',
    '@storybook/addon-links',
    '@storybook/addon-essentials',
    '@chromatic-com/storybook',
    '@storybook/addon-interactions',
  ],
  framework: {
    name: '@storybook/nextjs',
    options: {},
  },
  docs: {
    autodocs: 'tag',
  },
  webpackFinal(baseConfig) {
    baseConfig.resolve!.alias = {
      ...baseConfig.resolve!.alias,
    }
    baseConfig.resolve!.plugins = [
      ...(baseConfig.resolve!.plugins || []),
      new TsconfigPathsPlugin(),
    ]
    return baseConfig
  },
  staticDirs: ['../public'],
}
export default config
```
