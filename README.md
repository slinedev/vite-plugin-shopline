# vite-plugin-shopline

`vite-plugin-shopline` aims to bring the modern frontend toolchain of [Vite](https://vitejs.dev/) seamlessly into your SHOPLINE theme development workflow. This plugin is dedicated to providing SHOPLINE developers with the same excellent Developer Experience (DX).

It's not meant to replace the official **SHOPLINE CLI**, but rather to act as its powerful **companion tool**. By combining Vite's lightning-fast Hot Module Replacement (HMR) with the robust theme serving capabilities of the SHOPLINE CLI, you can say goodbye to slow compilations and manual refreshes, and focus on creating outstanding e-commerce experiences.

## Core Features

This plugin includes all the out-of-the-box features provided by Vite, plus deep optimizations for the SHOPLINE theme development environment (based on the Sline template engine):

  - **⚡️ The Ultimate Vite Experience**: Enjoy Vite's lightning-fast startup speed and instant Hot Module Replacement (HMR), with support for modern frontend technologies like TypeScript, JSX, and Sass.
  - **🤝 Seamless Integration with SHOPLINE CLI**: Designed to work in tandem with `sl theme serve`, allowing you to enjoy Vite's speed while retaining access to real store data .
  - **🤖 Automatic Entrypoint Detection**: No manual configuration needed. The plugin automatically recognizes all JavaScript and CSS files in the `frontend/entrypoints` directory as build entrypoints for Vite.
  - **🏷️ Smart Sline Component**: Automatically generates a `vite-tag.html` component in your `components` directory . It intelligently detects development and production environments to correctly load your scripts and styles using Sline's native tags .
  - **🌎 Full SHOPLINE CDN Support**: In production builds, all assets are loaded from the official CDN via Sline's `asset_url` filter, ensuring global access speeds and optimal performance .
  - **👌 Zero-Config Startup**: Comes with sensible default configurations that work for the vast majority of SHOPLINE theme projects, allowing you to get started "out-of-the-box" with no extra setup.
  - **🔩 Highly Extensible**: Provides a complete set of configuration options, allowing you to flexibly customize the plugin's behavior to meet your project's specific needs .

## Installation & Setup

Before you begin, please ensure your development environment meets the following requirements.

### Prerequisites

  - Install [Node.js](https://nodejs.org/) (version 16 or higher is recommended).
  - Install and configure the([https://developer.shopline.com/docs/themes/os-3-0/get-started/build-a-custom-theme/?lang=en](https://developer.shopline.com/docs/themes/os-3-0/get-started/build-a-custom-theme/?lang=en)) .
  - Have an existing SHOPLINE theme project. If your project root doesn't have a `package.json` file, run `npm init -y` to create one.

### Install the Plugin

In your SHOPLINE theme's root directory, install `vite` and `vite-plugin-shopline` as development dependencies using `npm`, `yarn`, or `pnpm`.

```bash
# Using npm
npm install -D vite vite-plugin-shopline

# Using yarn
yarn add -D vite vite-plugin-shopline

# Using pnpm
pnpm add -D vite vite-plugin-shopline
```

### Configure Vite

1.  In your project's root directory (at the same level as `package.json`), create a file named `vite.config.js`.

2.  Copy the following minimal configuration into the file:

    ```javascript
    // vite.config.js
    import { defineConfig } from 'vite'
    import shopline from 'vite-plugin-shopline'

    export default defineConfig({
      plugins: [
        shopline()
      ]
    })
    ```

> **Important Note**: This plugin is an ESM-only package. You may need to add `"type": "module"` to your `package.json` or rename your Vite config file with an `.mjs` or `.mts` extension to ensure compatibility.[8]

## Quick Start: A Modern Dual-Terminal Workflow

To get the best development experience, we highly recommend adopting a parallel dual-terminal workflow.[5, 9, 10] This workflow perfectly combines Vite's frontend capabilities with the theme serving power of the SHOPLINE CLI.

  - **Vite's Role**: Compiles your modern frontend assets (like JS/TS, CSS/SCSS), provides lightning-fast Hot Module Replacement (HMR), and serves these assets via a local development server (defaults to `http://localhost:5173`).
  - **SHOPLINE CLI's Role**: Runs your theme, parses Sline templates, provides SHOPLINE's global objects and data, and acts as the bridge to your store.[7, 11]

### Step 1: Define Your Entrypoints

By convention, the plugin automatically scans the `frontend/entrypoints/` directory. Place your main JavaScript and style files here.

A typical directory structure looks like this:

```
your-shopline-theme/
├── assets/
├── config/
├── components/   <-- Sline component directory
├── layout/
├── sections/
├── templates/
├── frontend/
│   └── entrypoints/
│       ├── theme.scss
│       └── theme.js
├── vite.config.js
└── package.json
```

### Step 2: Include the Sline Component in Your Theme

The plugin automatically generates two key files in your `components` directory: `vite-client.html` and `vite-tag.html`.

1.  Open your main layout file, which is typically `layout/theme.html`.

2.  Inside the `<head>` tag, use Sline's `component` tag to include your entrypoints .

    ```sline
    {{! File path: layout/theme.html }}

    <!DOCTYPE html>
    <html>
      <head>
        {{!... other head tags... }}

        {{! In development mode, this component includes the Vite client for HMR }}
        {{#component 'vite-client' /}}

        {{! Use the vite-tag component to load your CSS and JS entrypoints }}
        {{! The path is relative to the entrypointsDir option }}
        {{#component 'vite-tag' entry='theme.scss' /}}
        {{#component 'vite-tag' entry='theme.js' /}}

        {{!... other head tags... }}
      </head>
      <body>
        {{!... page content... }}
      </body>
    </html>
    ```

**How it works**:

  - In **development mode**, `vite-tag.html` generates `<script>` and `<link>` tags pointing to the Vite dev server (`http://localhost:5173/...`). Meanwhile, `vite-client.html` injects the HMR client script.
  - In **production mode**, `vite-tag.html` uses Sline's `{{#script}}` and `{{#stylesheet}}` tags , combined with the `asset_url` filter , to load version-hashed static assets from the CDN.

### Step 3: Start the Development Servers

Now, open two terminal windows and run the following commands in parallel:

**Terminal 1: Start the Vite Dev Server**

```bash
npm run dev
```

**Terminal 2: Start the SHOPLINE Theme Preview**

```bash
sl theme serve
```

*(Ensure you are logged into your store via `sl login`)*

The SHOPLINE CLI  will provide a local preview URL (e.g., `http://127.0.0.1:8282`). Open this URL in your browser to start developing. When you modify files in the `frontend/` directory, the page content will update instantly via hot reload, no manual refresh required.

For convenience, we recommend adding the following scripts to your `package.json`:

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build"
  }
}
```

## Plugin Configuration

While `vite-plugin-shopline` is designed for zero-config startup, you can customize its behavior by passing an options object in `vite.config.js`. Here is a detailed reference of all available options.

| Option | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `themeRoot` | `string` | `'./'` | The path to your SHOPLINE theme's root directory, which should contain folders like `layout`, `components`, and `config`. |
| `sourceCodeDir` | `string` | `'frontend'` | The root directory for your frontend source code. This path is used as the base for `@/` and `~/` import aliases. |
| `entrypointsDir` | `string` | `'frontend/entrypoints'` | The directory where Vite will look for entrypoint files (e.g., JS, CSS, TS, SCSS). |
| `additionalEntrypoints` | `string` | \`\` | An array of glob patterns for specifying additional entrypoints. These files can be located outside of `entrypointsDir` and are useful for page-specific assets. |
| \`componentFile\` | \`string\` | \`'vite-tag.html'\` | Specifies the filename of the generated Sline component used for loading assets. |
| \`tunnel\` | \`boolean | string\` | \`false\` | Enables a Cloudflare tunnel during development, allowing you to preview your theme on any device. Set to \`true\` for automatic creation or provide a custom tunnel URL. |
| \`themeHotReload\` | \`boolean\` | \`true\` | Specifies whether to enable hot reloading for theme files (like \`.html\` and \`.json\`). This feature depends on the capabilities of the SHOPLINE CLI. |

## Deployment Workflow

A clear distinction between development and production workflows is key to efficiency.

### Development vs. Production

  - **Development (`npm run dev` + `sl theme serve`)**: In this mode, Vite processes assets in memory and serves them from its dev server. The focus is on **speed** and **HMR**.
  - **Production (`npm run build`)**: In this mode, Vite bundles, minifies, code-splits, and hashes your code, generating optimized static assets in the theme's `assets` directory. The focus is on **performance optimization**.

### Deployment Steps

When you're ready to deploy your theme, follow these steps:

1.  **Build Production Assets**: Run the build command locally.

    ```bash
    npm run build
    ```

    This command generates the final JS and CSS files from your code and places them in the `assets/` directory.

2.  **Push Theme to Store**: Use the SHOPLINE CLI to upload the entire theme, including the newly generated files in `assets/`, to your store.

    ```bash
    sl theme push
    ```

    You can add extra flags as needed, such as `--theme <THEME_ID>` to target a specific theme or `--publish` to publish the theme immediately after pushing .

### Ignoring Source Files

A crucial best practice is to **never upload your frontend source code (i.e., the `frontend/` directory) to your SHOPLINE theme**. These are development-time files, not the final assets needed for production.

To prevent accidental uploads, create a `.shoplineignore` file in your theme's root directory and add the following content. This practice helps avoid version control and deployment conflicts.

```
#.shoplineignore

# Ignore Node.js dependencies and local config files
node_modules/
vite.config.js
package.json
package-lock.json
pnpm-lock.yaml
yarn.lock

# Ignore all frontend source code
frontend/

# Ignore IDE and system files
.vscode/
.idea/
.DS_Store
```

## Advanced Usage & Tips

### Using JavaScript Frameworks

`vite-plugin-shopline` works seamlessly with major frontend frameworks.

#### React

1.  Install the React plugin:
    ```bash
    npm i -D @vitejs/plugin-react
    ```
2.  Update your `vite.config.js`:
    ```javascript
    // vite.config.js
    import { defineConfig } from 'vite'
    import shopline from 'vite-plugin-shopline'
    import react from '@vitejs/plugin-react'

    export default defineConfig({
      plugins:
    })
    ```
    You can now use `.jsx` and `.tsx` files as entrypoints and enjoy the development experience of React Fast Refresh .

#### Vue

1.  Install the Vue plugin:
    ```bash
    npm i -D @vitejs/plugin-vue
    ```
2.  Update your `vite.config.js`:
    ```javascript
    // vite.config.js
    import { defineConfig } from 'vite'
    import shopline from 'vite-plugin-shopline'
    import vue from '@vitejs/plugin-vue'

    export default defineConfig({
      plugins: [
        shopline(),
        vue() // Add the Vue plugin
      ]
    })
    ```
    Now you can directly import `.vue` Single-File Components in your entrypoints.[12, 13, 14]

### Preloading Stylesheets

For critical, render-blocking CSS, you can enable preloading by passing a parameter to the `vite-tag` component. This will leverage Sline's `{{#preload_tag}}` functionality . Use this feature sparingly, only to optimize First Contentful Paint.

```sline
{{! Preload a critical CSS file }}
{{#component 'vite-tag' entry='critical.scss' preload=true /}}
```

### Import Aliases

To simplify module import paths, the plugin configures two aliases by default: `@/` and `~/`. Both point to your configured `sourceCodeDir` (which defaults to `frontend`).

```javascript
// No need for relative paths like../../
import MyComponent from '@/components/MyComponent.vue';
import utils from '~/lib/utils.js';

// Also works for style imports
@import '@/styles/main.scss';
```

## Troubleshooting / FAQ

### CORS error during development

  - **Issue**: The browser console reports a Cross-Origin Resource Sharing (CORS) error, failing to load resources from `http://localhost:5173`.
  - **Cause**: This happens because the browser's security policy blocks requests from the SHOPLINE preview domain (e.g., `your-store.myshopline.com`) to your local Vite server.
  - **Solution**: Explicitly allow your store's preview domain in `vite.config.js` .
    ```javascript
    // vite.config.js
    export default {
      //... plugins
      server: {
        cors: {
          origin: ["https://your-store.myshopline.com"]
        }
      }
    }
    ```
    Replace `your-store.myshopline.com` with your actual store domain.

### Files in `assets/` directory are unexpectedly deleted

  - **Issue**: After running `npm run build`, files in the `assets` directory that were added by third-party apps have disappeared.
  - **Cause**: Vite's default behavior is to clear the output directory (`outDir`) before a build.
  - **Solution**: Disable this behavior in `vite.config.js` and consider using a cleanup plugin to precisely remove only the old files generated by Vite .
    ```javascript
    // vite.config.js
    import shopline from 'vite-plugin-shopline'
    // Assuming a similar cleanup plugin exists, e.g., vite-plugin-shopline-clean
    // import cleanup from 'vite-plugin-shopline-clean'

    export default {
      build: {
        // Disable clearing the output directory
        emptyOutDir: false
      },
      plugins:
    }
    ```
    *Note: The `vite-plugin-shopline-clean` plugin is hypothetical.*

### Port is already in use

  - **Issue**: Starting Vite shows the error `Error: listen EADDRINUSE: address already in use :::5173`.
  - **Cause**: Port 5173 is already occupied by another application.
  - **Solution**: Vite is smart enough to automatically try the next available port (e.g., 5174, 5175). `vite-plugin-shopline` will automatically detect and use this new port.[6, 7] You just need to check the terminal output when Vite starts to see the actual port being used.
