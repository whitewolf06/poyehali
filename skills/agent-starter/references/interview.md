# Project interview

Read existing rules and implementation before asking. Keep a working list of facts with file evidence, user choices, unresolved decisions, and recommendations. Existing decisions carry forward on adoption and on repeated runs. Ask about an inconsistency when resolving it would affect the work.

## How to ask

- Start each round with the known decisions and the number of questions in that round. Ask at most three independent or related questions at a time. Wait for the answer where the next decision depends on it.
- Give two or three plausible options with consequences, identify the recommended one, and accept free-form answers. Use an interactive question tool when available. A numbered chat message works in any client.
- Recalculate after each round. Drop a question once the prompt, the repository, or an answer settles it. A user can accept the recommendations for the remaining decisions.
- An optional choice can remain deferred if it affects no files or commands in the selected mode. Label it as open in the proposed architecture. Resolve scope, the relevant stack and topology, and other choices that affect the planned writes before applying.

## Decision order

1. **Target and outcome.** Infer new setup or adoption from the workspace. Ask what the project does and who uses it if unclear. Ask whether it needs a browser UI, an API, both, or another surface such as a library or CLI. A brief description on top of a repository with code is usually enough.
2. **Depth.** Offer the modes below if the user has left the deliverable open.
3. **UI and rendering.** Determine which routes are public and need indexing or link previews. For public routes, determine whether the initial HTML can be built ahead of time or needs request-time data. Use Vue 3 and Legato UI as the default option for a new Vue app. Recommend Vite SPA for a closed UI, Nuxt prerender for build-time public content, Nuxt SSR for request-time public content, and route-specific rendering when both types coexist. Ask about another UI kit only if its selection is open and affects the plan. A static-only host constrains rendering and server routes.
4. **Backend and layout.** Determine whether server logic exists, whether the UI and API need independent delivery, and where data and contracts belong. For a small Vue fullstack with one runtime, propose Nuxt server routes. For a separate API, offer Python/FastAPI first and Go/`net/http` as a full alternative. A Python-dependent workload favors FastAPI. An existing Go team or a self-contained Go service favors Go. Choose repository layout separately from the runtime layout. Default to one repository for a new split UI/API owned by the same team, with separate app folders. Accept separate repositories when the user chooses them and name the target of each change.
5. **Verification.** Derive the baseline from real scripts and CI. Determine the first critical user or API flow, plus any DB, migration, contract, or SSR check that changes the requirements. Use the profile defaults for a new project. Ask for an explicit requirement only when the answer affects the selected mode or a rule the agent must follow.

| Mode | Result |
| --- | --- |
| `rules` (recommended) | Write or extend the project-local agent and engineering docs. Document unavailable checks as planned. |
| `configs` | Add the docs plus agreed tooling and CI configuration where applicable. Use tooling-only manifests and lockfiles when needed. Leave application source and new test cases for a separate task. |
| `app` | Add the docs, applicable tooling, and a minimal runnable local UI, API, or UI/API pair. Verify one observable smoke scenario. In an established project, scaffold only a named new app or service the user requested in an unused location. |

## Example

> I found a Vue/Vite app using pnpm, ESLint, and Vitest. Two decisions remain in this round.
>
> 1. How far should I go? **Agent rules** (recommended), rules plus configs, or a new local app scaffold.
> 2. Which path should be the first browser smoke check? **Sign-in to the dashboard** (recommended), the public search path, or another flow you specify.

A prompt such as “Rules only for a new internal inventory SPA using Vue 3, Vite, Legato UI, pnpm, and no API” settles the stack, rendering, and scope. Derive the quality recommendations and proceed without a questionnaire.
