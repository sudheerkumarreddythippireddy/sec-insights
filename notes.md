####.eslintrc.cjs
-----------------------------------


This ESLint configuration file is set up to enforce coding standards and catch errors in a TypeScript project, specifically for a Next.js application. Here's a detailed explanation of each part:

1. **Require and Path Modules**:
   ```javascript
   // eslint-disable-next-line @typescript-eslint/no-var-requires
   const path = require("path");
   ```
   This line imports the `path` module to handle and transform file paths. The `eslint-disable-next-line` comment is used to ignore a specific ESLint rule for the next line.

2. **Config Object Declaration**:
   ```javascript
   /** @type {import("eslint").Linter.Config} */
   const config = {
     overrides: [
       {
         extends: [
           "plugin:@typescript-eslint/recommended-requiring-type-checking",
         ],
         files: ["*.ts", "*.tsx"],
         parserOptions: {
           project: path.join(__dirname, "tsconfig.json"),
         },
       },
     ],
   ```
   - **Type Annotation**:
     ```javascript
     /** @type {import("eslint").Linter.Config} */
     ```
     This is a JSDoc comment that provides type information to editors and tools, indicating that `config` is of type `import("eslint").Linter.Config`.
   
   - **Overrides**:
     ```javascript
     overrides: [
       {
         extends: [
           "plugin:@typescript-eslint/recommended-requiring-type-checking",
         ],
         files: ["*.ts", "*.tsx"],
         parserOptions: {
           project: path.join(__dirname, "tsconfig.json"),
         },
       },
     ],
     ```
     The `overrides` array is used to specify different ESLint configurations for certain files. Here, it applies the `@typescript-eslint/recommended-requiring-type-checking` rules for TypeScript files (`*.ts`, `*.tsx`). The `parserOptions` section specifies the path to the `tsconfig.json` file, enabling type-aware linting.

3. **Parser Configuration**:
   ```javascript
   parser: "@typescript-eslint/parser",
   parserOptions: {
     project: path.join(__dirname, "tsconfig.json"),
   },
   ```
   - **Parser**:
     ```javascript
     parser: "@typescript-eslint/parser",
     ```
     This sets the parser to `@typescript-eslint/parser`, which allows ESLint to understand TypeScript syntax.
   
   - **Parser Options**:
     ```javascript
     parserOptions: {
       project: path.join(__dirname, "tsconfig.json"),
     },
     ```
     This specifies the path to the `tsconfig.json` file, enabling ESLint to parse the TypeScript files correctly.

4. **Plugins and Extends**:
   ```javascript
   plugins: ["@typescript-eslint"],
   extends: ["next/core-web-vitals", "plugin:@typescript-eslint/recommended"],
   ```
   - **Plugins**:
     ```javascript
     plugins: ["@typescript-eslint"],
     ```
     This includes the `@typescript-eslint` plugin, which provides linting rules specific to TypeScript.
   
   - **Extends**:
     ```javascript
     extends: ["next/core-web-vitals", "plugin:@typescript-eslint/recommended"],
     ```
     This extends the base ESLint configuration with recommended settings for Next.js (`next/core-web-vitals`) and TypeScript (`plugin:@typescript-eslint/recommended`).

5. **Rules**:
   ```javascript
   rules: {
     "@typescript-eslint/consistent-type-imports": [
       "warn",
       {
         prefer: "type-imports",
         fixStyle: "inline-type-imports",
       },
     ],
     "@typescript-eslint/no-unused-vars": ["warn", { argsIgnorePattern: "^_" }],
   },
   ```
   - **Consistent Type Imports**:
     ```javascript
     "@typescript-eslint/consistent-type-imports": [
       "warn",
       {
         prefer: "type-imports",
         fixStyle: "inline-type-imports",
       },
     ],
     ```
     This rule enforces the consistent use of type imports. It issues a warning (`"warn"`) and prefers the use of type imports (`"prefer": "type-imports"`). The `fixStyle` option specifies the style for fixing the import, here set to `inline-type-imports`.
   
   - **No Unused Variables**:
     ```javascript
     "@typescript-eslint/no-unused-vars": ["warn", { argsIgnorePattern: "^_" }],
     ```
     This rule issues a warning (`"warn"`) for unused variables. The `argsIgnorePattern` option allows unused variables that start with an underscore (`^_`).

6. **Export Configuration**:
   ```javascript
   module.exports = config;
   ```
   This line exports the configuration object, making it available for ESLint to use.


####next.config.mjs

This code snippet configures a Next.js application to integrate with Sentry for error tracking and performance monitoring. Here's a detailed explanation of each part:

1. **Importing withSentryConfig**:
   ```javascript
   import { withSentryConfig } from "@sentry/nextjs";
   ```
   This line imports the `withSentryConfig` function from the `@sentry/nextjs` package, which is used to configure Sentry within a Next.js application.

2. **Environment Validation**:
   ```javascript
   await import("./src/env.mjs");
   ```
   This line dynamically imports an `env.mjs` file from the `src` directory, likely used for environment validation and configuration. The comment above it suggests that setting the `SKIP_ENV_VALIDATION` environment variable can bypass this validation, which can be useful for Docker builds.

3. **Next.js Configuration Object**:
   ```javascript
   /** @type {import("next").NextConfig} */
   const config = {
     reactStrictMode: true,

     /**
      * If you have `experimental: { appDir: true }` set, then you must comment the below `i18n` config
      * out.
      *
      * @see https://github.com/vercel/next.js/issues/41980
      */
     i18n: {
       locales: ["en"],
       defaultLocale: "en",
     },
   };
   ```
   - **Type Annotation**:
     ```javascript
     /** @type {import("next").NextConfig} */
     ```
     This JSDoc comment provides type information, indicating that `config` is of type `import("next").NextConfig`.
   
   - **Configuration Settings**:
     ```javascript
     reactStrictMode: true,
     ```
     Enables React's strict mode to help identify potential problems in the application.
     
     ```javascript
     i18n: {
       locales: ["en"],
       defaultLocale: "en",
     },
     ```
     Configures internationalization (i18n) settings with English as the default and only locale. The comment suggests that if `experimental: { appDir: true }` is enabled, this i18n configuration should be commented out due to an issue tracked on GitHub.

4. **Exporting Configuration with Sentry Integration**:
   ```javascript
   export default withSentryConfig(config, {
     // For all available options, see:
     // https://github.com/getsentry/sentry-webpack-plugin#options

     // Suppresses source map uploading logs during build
     silent: true,

     org: "llama-test",
     project: "javascript-nextjs",
   }, {
     // For all available options, see:
     // https://docs.sentry.io/platforms/javascript/guides/nextjs/manual-setup/

     // Upload a larger set of source maps for prettier stack traces (increases build time)
     widenClientFileUpload: true,

     // Transpiles SDK to be compatible with IE11 (increases bundle size)
     transpileClientSDK: true,

     // Routes browser requests to Sentry through a Next.js rewrite to circumvent ad-blockers (increases server load)
     tunnelRoute: "/monitoring",

     // Hides source maps from generated client bundles
     hideSourceMaps: true,

     // Automatically tree-shake Sentry logger statements to reduce bundle size
     disableLogger: true,
   });
   ```
   - **withSentryConfig Function**:
     ```javascript
     export default withSentryConfig(config, { ... }, { ... });
     ```
     This function wraps the Next.js configuration (`config`) with Sentry-specific settings. It takes three arguments:
     - The Next.js configuration object (`config`).
     - Sentry Webpack plugin options (first object).
     - Sentry Next.js SDK options (second object).

   - **Sentry Webpack Plugin Options**:
     ```javascript
     {
       silent: true,
       org: "llama-test",
       project: "javascript-nextjs",
     }
     ```
     These options configure the Sentry Webpack plugin:
     - `silent`: Suppresses source map uploading logs during the build process.
     - `org`: Specifies the Sentry organization.
     - `project`: Specifies the Sentry project.

   - **Sentry Next.js SDK Options**:
     ```javascript
     {
       widenClientFileUpload: true,
       transpileClientSDK: true,
       tunnelRoute: "/monitoring",
       hideSourceMaps: true,
       disableLogger: true,
     }
     ```
     These options configure the Sentry Next.js SDK:
     - `widenClientFileUpload`: Uploads a larger set of source maps for better stack traces but increases build time.
     - `transpileClientSDK`: Ensures the SDK is compatible with IE11 but increases bundle size.
     - `tunnelRoute`: Routes browser requests to Sentry through a Next.js rewrite to avoid ad-blockers, which increases server load.
     - `hideSourceMaps`: Hides source maps from generated client bundles.
     - `disableLogger`: Tree-shakes Sentry logger statements to reduce bundle size.

###package.json
-----------------------------------------
This `package.json` file specifies the configuration for a Next.js frontend project named `llama-app-frontend`. It includes information about the project's dependencies, devDependencies, scripts, and metadata. Here's a detailed breakdown:

### Project Information

- **name**: The name of the project is `llama-app-frontend`.
- **version**: The current version of the project is `0.1.0`.
- **private**: This is set to `true`, indicating that the project is not intended to be published as a package to a registry like npm.

### Scripts

The `scripts` section defines custom commands that can be run using `npm run <script-name>`:

- **build**: Runs the Next.js build command to create an optimized production build.
  ```json
  "build": "next build"
  ```
- **dev**: Starts the Next.js development server.
  ```json
  "dev": "next dev"
  ```
- **lint**: Runs the Next.js lint command to lint the codebase.
  ```json
  "lint": "next lint"
  ```
- **start**: Starts the Next.js production server.
  ```json
  "start": "next start"
  ```

### Dependencies

These are the packages required for the project to run in production:

- **@headlessui/react**: A set of completely unstyled, fully accessible UI components.
- **@heroicons/react**: React components for Heroicons, a set of free, MIT-licensed high-quality SVG icons.
- **@sentry/nextjs**: Sentry SDK for error tracking and performance monitoring in Next.js.
- **@t3-oss/env-nextjs**: Environmental validation for Next.js projects.
- **@tailwindcss/forms**: A plugin that provides a basic reset for form styles with Tailwind CSS.
- **@wojtekmaj/react-hooks**: A collection of useful React hooks.
- **classnames**: A utility for conditionally joining classNames together.
- **downshift**: Primitives to build simple, flexible, WAI-ARIA compliant React autocomplete/dropdown/select/combobox components.
- **fuse.js**: A lightweight fuzzy-search library.
- **lodash**: A modern JavaScript utility library delivering modularity, performance, and extras.
- **lodash.debounce**: A debounce method from lodash.
- **md5**: A JavaScript function for hashing messages with MD5.
- **next**: The Next.js framework.
- **react**: The React library.
- **react-dom**: React package for working with the DOM.
- **react-ga4**: A library for Google Analytics 4 integration with React.
- **react-github-btn**: A React component for GitHub buttons.
- **react-icons**: Include popular icons in your React projects easily.
- **react-intersection-observer**: React implementation of the Intersection Observer API to monitor the visibility of elements.
- **react-pdf**: Display PDFs in your React app.
- **react-select**: A flexible and beautiful Select Input control for React.
- **react-use-intercom**: A React hook for Intercom integration.
- **react-window**: React components for efficiently rendering large lists and tabular data.
- **uuid**: A library for generating unique identifiers.
- **zod**: A TypeScript-first schema declaration and validation library.

### DevDependencies

These are packages required for the development and build process:

- **@tailwindcss/forms**: The same plugin listed in dependencies; having it in both is redundant.
- **@types/eslint**: TypeScript definitions for ESLint.
- **@types/lodash**: TypeScript definitions for lodash.
- **@types/lodash.debounce**: TypeScript definitions for lodash's debounce method.
- **@types/md5**: TypeScript definitions for md5.
- **@types/node**: TypeScript definitions for Node.js.
- **@types/prettier**: TypeScript definitions for Prettier.
- **@types/react**: TypeScript definitions for React.
- **@types/react-dom**: TypeScript definitions for React DOM.
- **@types/react-window**: TypeScript definitions for react-window.
- **@types/uuid**: TypeScript definitions for uuid.
- **@typescript-eslint/eslint-plugin**: ESLint plugin for TypeScript-specific linting rules.
- **@typescript-eslint/parser**: ESLint parser for TypeScript.
- **autoprefixer**: A PostCSS plugin to parse CSS and add vendor prefixes.
- **eslint**: A tool for identifying and fixing problems in JavaScript code.
- **eslint-config-next**: ESLint configuration for Next.js projects.
- **eslint-config-prettier**: Configures ESLint to work with Prettier.
- **postcss**: A tool for transforming CSS with JavaScript plugins.
- **prettier**: An opinionated code formatter.
- **prettier-plugin-tailwindcss**: A Prettier plugin for automatically sorting Tailwind CSS classes.
- **tailwindcss**: A utility-first CSS framework for rapidly building custom designs.
- **typescript**: The TypeScript language.

### Metadata

- **ct3aMetadata**: Metadata for internal use, likely to track initial versions or configurations.
  ```json
  "initVersion": "7.13.1"
  ```

This `package.json` file sets up a comprehensive development environment for a Next.js application with TypeScript, Sentry integration, and a range of utilities and tools to enhance development and production workflows.

###postcss.config.cjs
----------------------------------
This code defines and exports a configuration object for PostCSS, a tool for transforming CSS with JavaScript plugins. Here's a detailed explanation of each part:

1. **Configuration Object**:
   ```javascript
   const config = {
     plugins: {
       tailwindcss: {},
       autoprefixer: {},
     },
   };
   ```
   This object defines the PostCSS configuration, specifying which plugins to use.

2. **Plugins**:
   - **tailwindcss**:
     ```javascript
     tailwindcss: {},
     ```
     This line adds the `tailwindcss` plugin to the PostCSS configuration. The empty object (`{}`) indicates that default settings will be used. The `tailwindcss` plugin is used for incorporating Tailwind CSS, a utility-first CSS framework, into the project. Tailwind CSS provides a wide range of utility classes that help in rapidly building custom designs without leaving the HTML.

   - **autoprefixer**:
     ```javascript
     autoprefixer: {},
     ```
     This line adds the `autoprefixer` plugin to the PostCSS configuration. Again, the empty object (`{}`) indicates that default settings will be used. `autoprefixer` is a PostCSS plugin that automatically adds vendor prefixes to CSS rules to ensure compatibility with different browsers. This helps in writing standard CSS without worrying about cross-browser issues.

3. **Exporting the Configuration**:
   ```javascript
   module.exports = config;
   ```
   This line exports the `config` object, making it available for PostCSS to use. When PostCSS runs, it will look for this configuration file and apply the specified plugins (`tailwindcss` and `autoprefixer`) to the CSS files it processes.

### Summary

This configuration file sets up PostCSS with two plugins:
- **Tailwind CSS** for using utility-first CSS classes in the project.
- **Autoprefixer** for adding vendor prefixes to CSS rules to ensure compatibility across different browsers.

By exporting this configuration, you enable PostCSS to apply these transformations to your CSS during the build process.

####prettier.config.cjs
------------------------------------
This code defines and exports a configuration object for Prettier, an opinionated code formatter, and includes a plugin for Tailwind CSS. Here's a detailed explanation of each part:

1. **Type Annotation**:
   ```javascript
   /** @type {import("prettier").Config} */
   ```
   This JSDoc comment provides type information for the `config` object, indicating that it adheres to the Prettier configuration type. This helps with editor support and ensures that the configuration object is correctly structured according to Prettier's expected format.

2. **Configuration Object**:
   ```javascript
   const config = {
     plugins: [require.resolve("prettier-plugin-tailwindcss")],
   };
   ```
   This object defines the Prettier configuration:

   - **plugins**:
     ```javascript
     plugins: [require.resolve("prettier-plugin-tailwindcss")],
     ```
     This line specifies the plugins to be used by Prettier. The `require.resolve("prettier-plugin-tailwindcss")` call dynamically resolves the path to the `prettier-plugin-tailwindcss` module. Including this plugin allows Prettier to understand and format Tailwind CSS classes in a specific order, which can help maintain a consistent style across your project.

3. **Exporting the Configuration**:
   ```javascript
   module.exports = config;
   ```
   This line exports the `config` object, making it available for Prettier to use. When Prettier runs, it will apply the configuration specified in this file, including any plugins listed.

### Summary

This Prettier configuration file does the following:
- Declares a configuration object with a type annotation to ensure proper structure and type support.
- Specifies the `prettier-plugin-tailwindcss` plugin, which helps in formatting Tailwind CSS classes in a consistent order.
- Exports the configuration object so that Prettier can use it when formatting code.

By including the `prettier-plugin-tailwindcss` plugin, you enhance Prettier's capabilities to better handle Tailwind CSS, ensuring that your utility classes are ordered consistently according to Tailwind's recommended conventions.

####sentry.client.config.ts
-------------------------------------

This code snippet configures the initialization of Sentry for a Next.js application on the client side. Sentry is used for error tracking and performance monitoring. Here's a detailed breakdown of each part:

1. **Imports**:
   ```javascript
   import * as Sentry from "@sentry/nextjs";
   import { SENTRY_DSN } from "~/constants";
   ```
   - **Sentry**: The `@sentry/nextjs` package is imported, which provides tools and methods for integrating Sentry with a Next.js application.
   - **SENTRY_DSN**: This constant is imported from a local module (`~/constants`). It typically contains the Data Source Name (DSN) for the Sentry project, which is a unique identifier for your Sentry project.

2. **Sentry Initialization**:
   ```javascript
   Sentry.init({
     dsn: SENTRY_DSN,

     // Adjust this value in production, or use tracesSampler for greater control
     tracesSampleRate: 1,

     // Setting this option to true will print useful information to the console while you're setting up Sentry.
     debug: false,

     replaysOnErrorSampleRate: 1.0,

     // This sets the sample rate to be 10%. You may want this to be 100% while
     // in development and sample at a lower rate in production
     replaysSessionSampleRate: 0.1,

     // You can remove this option if you're not planning to use the Sentry Session Replay feature:
     integrations: [
       new Sentry.Replay({
         // Additional Replay configuration goes in here, for example:
         maskAllText: true,
         blockAllMedia: true,
       }),
     ],
   });
   ```
   - **dsn**:
     ```javascript
     dsn: SENTRY_DSN,
     ```
     The DSN (Data Source Name) is used to identify the project within Sentry and connect your application to the Sentry service.

   - **tracesSampleRate**:
     ```javascript
     tracesSampleRate: 1,
     ```
     This sets the rate at which transactions (performance monitoring) are sampled. A value of `1` means 100% of transactions are captured. This is typically adjusted in production to a lower value to reduce the volume of data.

   - **debug**:
     ```javascript
     debug: false,
     ```
     This option, when set to `true`, enables debug mode and prints useful information to the console. It is set to `false` here, meaning debug information will not be printed.

   - **replaysOnErrorSampleRate**:
     ```javascript
     replaysOnErrorSampleRate: 1.0,
     ```
     This sets the sample rate for session replays when an error occurs. A value of `1.0` means 100% of error sessions will have replays.

   - **replaysSessionSampleRate**:
     ```javascript
     replaysSessionSampleRate: 0.1,
     ```
     This sets the sample rate for session replays in general. A value of `0.1` means 10% of sessions will have replays. This can be adjusted to a higher rate in development and a lower rate in production.

   - **integrations**:
     ```javascript
     integrations: [
       new Sentry.Replay({
         // Additional Replay configuration goes in here, for example:
         maskAllText: true,
         blockAllMedia: true,
       }),
     ],
     ```
     This specifies additional integrations for Sentry. In this case, the `Replay` integration is added, which provides session replay functionality. The replay integration is configured with options:
     - **maskAllText**: When set to `true`, all text in the replay is masked to protect sensitive information.
     - **blockAllMedia**: When set to `true`, all media (like images and videos) is blocked in the replay to reduce data volume and protect privacy.

### Summary

This configuration file initializes Sentry for a Next.js application on the client side, setting up error tracking, performance monitoring, and session replay features. Key settings include:
- **dsn**: Connects the application to the specific Sentry project.
- **tracesSampleRate**: Controls the sampling rate for performance monitoring.
- **debug**: Enables or disables debug mode.
- **replaysOnErrorSampleRate** and **replaysSessionSampleRate**: Control the sampling rates for session replays.
- **integrations**: Adds the session replay feature with specific configurations to mask text and block media.

By adjusting these settings, you can control the amount of data sent to Sentry, enabling efficient monitoring and debugging of your application in both development and production environments.

####sentry.edge.config.ts
-------------------------------

This code snippet configures the initialization of Sentry for edge features in a Next.js application. Edge features include middleware and edge routes, which can run at the network edge for improved performance and lower latency. Here’s a detailed explanation of each part:

1. **Imports**:
   ```javascript
   import * as Sentry from "@sentry/nextjs";
   import { SENTRY_DSN } from "~/constants";
   ```
   - **Sentry**: The `@sentry/nextjs` package is imported, which provides tools and methods for integrating Sentry with a Next.js application.
   - **SENTRY_DSN**: This constant is imported from a local module (`~/constants`). It typically contains the Data Source Name (DSN) for the Sentry project, which is a unique identifier for your Sentry project.

2. **Sentry Initialization**:
   ```javascript
   Sentry.init({
     dsn: SENTRY_DSN,

     // Adjust this value in production, or use tracesSampler for greater control
     tracesSampleRate: 1,

     // Setting this option to true will print useful information to the console while you're setting up Sentry.
     debug: false,
   });
   ```
   - **dsn**:
     ```javascript
     dsn: SENTRY_DSN,
     ```
     The DSN (Data Source Name) is used to identify the project within Sentry and connect your application to the Sentry service.

   - **tracesSampleRate**:
     ```javascript
     tracesSampleRate: 1,
     ```
     This sets the rate at which transactions (performance monitoring) are sampled. A value of `1` means 100% of transactions are captured. This is typically adjusted in production to a lower value to reduce the volume of data.

   - **debug**:
     ```javascript
     debug: false,
     ```
     This option, when set to `true`, enables debug mode and prints useful information to the console. It is set to `false` here, meaning debug information will not be printed.

### Summary

This configuration file initializes Sentry for edge features in a Next.js application, setting up error tracking and performance monitoring. Key settings include:
- **dsn**: Connects the application to the specific Sentry project.
- **tracesSampleRate**: Controls the sampling rate for performance monitoring.
- **debug**: Enables or disables debug mode.

By including this configuration, Sentry will be initialized whenever an edge feature (such as middleware or edge routes) is loaded, both in production and when running locally. This ensures consistent monitoring and error tracking across all parts of the application.


