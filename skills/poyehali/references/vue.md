# Vue, Nuxt, and Legato UI

Use this profile when the project uses Vue or the user chooses the Vue preset. When extending a project, keep its established architecture, stack, styling, linting, and test runners unless the user asks to change them.

## Default preset

| Decision | Recommendation |
| --- | --- |
| Stack | Vue 3, Composition API, strict TypeScript, pnpm and [Legato UI](https://github.com/whitewolf06/legato-ui). Choose Vite or Nuxt from the rendering needs. |
| Lint and formatting | ESLint flat config with [`@antfu/eslint-config`](https://github.com/antfu/eslint-config). Run a separate typecheck. |
| Styles | Plain CSS, scoped styles for component-specific layout, and CSS custom properties for theme values. Use SCSS when the user chooses it, the codebase already uses it, or the project needs Sass features. |
| Architecture | Start with the classic layout for a small new app. Recommend feature modules if the project already has several independently evolving features. Reserve explicit domain layers for complex business rules. |
| State | Keep state local first. Add Pinia for client state shared across routes or independently mounted features when it has a clear owner. |

Show the resolved preset before asking the user to accept or customize it. For customization, offer the choices below for the decisions the user wants to change. Applying the preset does not resolve unknown project requirements such as which routes need indexing.

## Choose the architecture

| Layout | When it fits | Boundaries |
| --- | --- | --- |
| Classic, by file role | A small app with a few routes and limited business logic. | Use views or Nuxt pages for route composition, components for UI, composables for reusable reactive behavior, and services or an API client for I/O. |
| Feature modules | Several product areas need to evolve independently. | Keep each feature's UI, state, and logic together. Routes compose features through public entry points. Move code to a shared area when it has multiple consumers and a clear owner. |
| Domain modules with layers | Business rules need to be tested independently or used across multiple workflows. | Use DDD ideas such as explicit domain boundaries and shared business language where they help. Separate presentation, use cases, domain rules, and infrastructure where those roles exist. UI calls use cases. Domain rules stay independent of Vue, HTTP, and persistence. Infrastructure implements the interfaces used at those boundaries. |

Use SOLID where it helps clarify ownership and contracts. Start with single responsibility. Give a component, composable, service, class, or function one reason to change. A page composes a flow, a component renders and emits user intent, and an API client handles transport. Prefer small public props, events and interfaces. Extend behavior with composition or slots and depend on contracts at real boundaries. Extract a domain rule when it has its own meaning and tests. Apply the [decomposition review](documents.md#review-size-and-responsibility) even when a change fits the selected directory layout.

For state, prefer local refs for component state and composables for reusable behavior. Put route state that belongs in the URL in the router. Use Pinia for durable shared client state such as session or active workspace when appropriate. Keep fetching and remote data with the feature's data layer or the framework's data-fetching facilities.

## Lint, types, and styles

In modes that write config, use `eslint.config.mjs` with compatible versions of ESLint and the preset. A minimal Vue config is:

```js
import antfu from '@antfu/eslint-config'

export default antfu({ typescript: true, vue: true })
```

Use the preset's ESLint Stylistic rules for the JS, TS and Vue syntax it handles. Give each syntax one formatter. CSS formatting is an optional Antfu integration with its own dependency, so choose it separately if needed. For an existing project, keep its working formatter and lint rules unless a migration was requested. In rules-only mode record the selection as planned where the config is absent. [Antfu configuration and formatters](https://github.com/antfu/eslint-config#optional-configs)

Use `<script setup lang="ts">` for new Vue SFCs, type props, events and public contracts, and narrow external inputs before using them. For new TypeScript projects enable `strict` and `noUncheckedIndexedAccess` in the project TypeScript config or supported framework override. Keep the framework typecheck in addition to lint. Put design tokens in their owning theme and style the app with scoped CSS and custom properties. If choosing SCSS, record the choice and where it applies.

## Choose the rendering model

| Requirement | Default |
| --- | --- |
| Private interface or public interface where indexing and server HTML are unnecessary | Vue with Vite and client-side rendering. |
| Indexable pages with data available at build time | Nuxt with prerendered public routes. |
| Indexable pages whose HTML needs request-time data | Nuxt SSR on those routes. Confirm that a server runtime is available. |
| Public pages and a private app with different needs | Nuxt with explicit per-route prerender, SSR, or client rendering. |
| Small integrated fullstack app | Nuxt with `server/api` handlers and a server runtime. Select the rendering policy for pages independently. |

Record which routes are public, how their data arrives, and where any server code runs. Use [Vue's quick start](https://vuejs.org/guide/quick-start.html), [Nuxt rendering modes](https://learn.nuxt.com/en/concepts/rendering-modes), and [Nuxt server routes](https://nuxt.com/docs/4.x/directory-structure/server) for the selected setup.

## Rules for the project

- Keep API transport behind a typed client. The library owns its tokens and primitives. The app owns layouts, domain state and feature-specific composition. Check keyboard operation, accessible names, focus, and loading, error, and empty states when introducing the corresponding UI.
- In Legato consumers, install `legato-ui` and import controls such as `WlButton` from the package. The default theme uses `legato-ui/styles/reset.css`, `legato-ui/styles/base.css`, and `legato-ui/themes/legato.css` in that order. Load `legato-ui/styles/primitives.css` after the base stylesheet if using its primitives, and `legato-ui/styles/fonts/legato.css` for the bundled font if requested. Set `data-wl-theme="legato"` on `<html>`. [Vite example](https://github.com/whitewolf06/legato-ui/tree/main/examples/vite-starter) · [Nuxt example](https://github.com/whitewolf06/legato-ui/tree/main/examples/nuxt-starter)
- For Nuxt, put the stylesheet list in the Nuxt `css` option and the `data-wl-theme` attribute in `app.head.htmlAttrs`. If the app configures a locale, import `WlConfig` and the chosen locale from `legato-ui` and call `nuxtApp.vueApp.use(WlConfig, { locale })` inside `defineNuxtPlugin`. Keep the same initial locale, data, and theme on server and client. Defer DOM-dependent effects to the client lifecycle. [Legato SSR guide](https://github.com/whitewolf06/legato-ui/blob/main/docs/guide/ssr.md)
- Scope middleware, authorization, and validation to the server when the app owns an API. Keep server secrets in server runtime config. When the API is separate, document the base URL, contract owner, and local connection before changing either side.

## Checks and setup by mode

For the rules-only mode, record the project's actual commands and the ones still planned. For a new Vue setup use separate `lint`, `typecheck`, `test:unit`, `test:e2e`, and `build` scripts. The defaults are ESLint with Antfu's config, `vue-tsc --noEmit` for Vite or Nuxt's `nuxt typecheck`, Vitest, Playwright, and the framework build. Use Vue Test Utils or the matching Nuxt test utilities where a test needs the app context. Run smoke E2E against a locally started app with isolated test data. Verify indexable routes by inspecting the initial HTML. Also exercise an interactive control in the browser to verify hydration.

For config mode, add the missing tool configuration for the confirmed framework. In a new folder use a `package.json`, lockfile, ESLint flat config, strict TypeScript configuration, and runner configs as applicable. If SCSS is selected, configure the Sass implementation supported by the project's builder. For an existing or newly scaffolded Nuxt app, generate its TypeScript configuration with `nuxt prepare` before checks that read `.nuxt/tsconfig.*.json`. Keep any lint exceptions for Nuxt conventions scoped to the files that need them. Define CI steps only for checks that can run meaningfully against the files and scripts present. Record the remaining prerequisites in the quality guide.

For app mode:

1. In an empty project or an approved empty subdirectory, build the minimal TypeScript app. Use a reviewed release of `create-vue` for Vite or the [official Nuxt starter](https://nuxt.com/docs/4.x/getting-started/installation) for Nuxt. Run the generator in a scratch directory, review its output, and use only the files needed for the chosen rendering and checks. Verify that the project's Node version satisfies the selected framework and tooling. Resolve a compatible `legato-ui` version and keep the target package manager's lockfile.
2. Create one page and one observable interaction, such as an accessible button that updates in-page state. Use the chosen UI library for the control. When an API is selected, surface the result of `GET /api/health` through the typed client. For split local development, proxy `/api` to the API's loopback address through Vite or Nuxt, or use the project's existing local connection. For integrated Nuxt, the route can live at `server/api/health.get.ts` and return `{ status: 'ok' }`.
3. Add a small Vitest test for the interaction or pure behavior and a Playwright test for the page and interaction or API-backed state. Run lint, typecheck, unit tests, and the selected build. For a static Nuxt artifact, use `nuxt generate` and exercise its output with a local static server. If that artifact calls a separate API, serve it behind a local proxy for `/api` or configure an explicit loopback API URL and a matching test origin. The Nuxt dev proxy is only available during development. For a Nuxt build with runtime handlers, use `nuxt build` and exercise the locally started server. For prerender and SSR, assert the public content in the initial HTML independently of the browser interaction. Keep external services out of this local smoke scenario.
