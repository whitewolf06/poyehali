# Vue, Nuxt, and Legato UI

Use this profile when the project uses Vue or the user chooses the Vue preset. Keep the frontend's established framework, package manager, UI library, and test runners when extending a project. For a new frontend recommend Vue 3, strict TypeScript, pnpm, and [Legato UI](https://github.com/whitewolf06/legato-ui).

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

- Keep route-level composition in views or Nuxt pages, reusable UI in components, and reusable stateful behavior in composables. Keep API transport behind a typed client. Add Pinia only when app-wide client state has a clear owner and consumers.
- Use typed props and events, scoped styles and CSS custom properties. The library owns its tokens and primitives. The app owns layouts, domain state and feature-specific composition. Check keyboard operation, accessible names, focus, and loading, error, and empty states when introducing the corresponding UI.
- In Legato consumers, install `legato-ui` and import controls such as `WlButton` from the package. The default theme uses `legato-ui/styles/reset.css`, `legato-ui/styles/base.css`, and `legato-ui/themes/legato.css` in that order. Load `legato-ui/styles/primitives.css` after the base stylesheet if using its primitives, and `legato-ui/styles/fonts/legato.css` for the bundled font if requested. Set `data-wl-theme="legato"` on `<html>`. [Vite example](https://github.com/whitewolf06/legato-ui/tree/main/examples/vite-starter) · [Nuxt example](https://github.com/whitewolf06/legato-ui/tree/main/examples/nuxt-starter)
- For Nuxt, put the stylesheet list in the Nuxt `css` option and the `data-wl-theme` attribute in `app.head.htmlAttrs`. If the app configures a locale, import `WlConfig` and the chosen locale from `legato-ui` and call `nuxtApp.vueApp.use(WlConfig, { locale })` inside `defineNuxtPlugin`. Keep the same initial locale, data, and theme on server and client. Defer DOM-dependent effects to the client lifecycle. [Legato SSR guide](https://github.com/whitewolf06/legato-ui/blob/main/docs/guide/ssr.md)
- Scope middleware, authorization, and validation to the server when the app owns an API. Keep server secrets in server runtime config. When the API is separate, document the base URL, contract owner, and local connection before changing either side.

## Checks and setup by mode

For the rules-only mode, record the project's actual commands and the ones still planned. For a new Vue setup use separate `lint`, `typecheck`, `test:unit`, `test:e2e`, and `build` scripts. The defaults are ESLint, `vue-tsc --noEmit` for Vite or Nuxt's `nuxt typecheck`, Vitest, Playwright, and the framework build. Use Vue Test Utils or the matching Nuxt test utilities where a test needs the app context. Run smoke E2E against a locally started app with isolated test data. Verify indexable routes by inspecting the initial HTML. Also exercise an interactive control in the browser to verify hydration.

For config mode, add the missing tool configuration for the confirmed framework. In a new folder use a `package.json`, lockfile, ESLint flat config, strict TypeScript configuration, and runner configs as applicable. For an existing or newly scaffolded Nuxt app, generate its TypeScript configuration with `nuxt prepare` before checks that read `.nuxt/tsconfig.*.json`. Keep any lint exceptions for Nuxt conventions scoped to the files that need them. Define CI steps only for checks that can run meaningfully against the files and scripts present. Record the remaining prerequisites in the quality guide.

For app mode:

1. In an empty project or an approved empty subdirectory, build the minimal TypeScript app. Use a reviewed release of `create-vue` for Vite or the [official Nuxt starter](https://nuxt.com/docs/4.x/getting-started/installation) for Nuxt. Run the generator in a scratch directory, review its output, and use only the files needed for the chosen rendering and checks. Verify that the project's Node version satisfies the selected framework and tooling. Resolve a compatible `legato-ui` version and keep the target package manager's lockfile.
2. Create one page and one observable interaction, such as an accessible button that updates in-page state. Use the chosen UI library for the control. When an API is selected, surface the result of `GET /api/health` through the typed client. For split local development, proxy `/api` to the API's loopback address through Vite or Nuxt, or use the project's existing local connection. For integrated Nuxt, the route can live at `server/api/health.get.ts` and return `{ status: 'ok' }`.
3. Add a small Vitest test for the interaction or pure behavior and a Playwright test for the page and interaction or API-backed state. Run lint, typecheck, unit tests, and the selected build. For a static Nuxt artifact, use `nuxt generate` and exercise its output with a local static server. If that artifact calls a separate API, serve it behind a local proxy for `/api` or configure an explicit loopback API URL and a matching test origin. The Nuxt dev proxy is only available during development. For a Nuxt build with runtime handlers, use `nuxt build` and exercise the locally started server. For prerender and SSR, assert the public content in the initial HTML independently of the browser interaction. Keep external services out of this local smoke scenario.
