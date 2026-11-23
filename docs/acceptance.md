# Release acceptance

Onboarding depends on the coding client as well as the instructions. Test the [portable skill](../skills/poyehali/SKILL.md) in fresh chats and inspect the target worktree after each run.

## Prepare fixtures

- Use a fresh target for each case. For a local rehearsal, replace `<source>` with the absolute path to a separate copy of this repository. For the URL test, replace it with the public GitHub repository URL and record the revision the client opened.
- For a new project, start in an empty directory with `git init`. For shared-stack cases, install only the runtimes the chosen preset needs. Record their versions and leave packages or external test services unavailable if the case is meant to exercise a blocked check.
- For the existing-project cases, use a disposable Git repository with the files below. Commit the fixture, then append `// LOCAL: keep this edit` to `index.cjs` and create an untracked `local-note.txt`. Use a separate copy for the conflict case.

| File | Fixture content |
| --- | --- |
| `AGENTS.md` | `# Agent rules` and `Read docs/development.md before changing code.` |
| `docs/development.md` | `# Development`, `Run npm test from the repository root.`, and `Put new tests under test/ with a .test.cjs suffix.` |
| `docs/architecture.md` | `# Architecture` and `This repository contains a Node.js utility in index.cjs.` |
| `package.json` | `{"name":"poyehali-adoption-fixture","private":true,"scripts":{"test":"node --test"}}` |
| `index.cjs` | `exports.total = values => values.reduce((sum, value) => sum + value, 0);` |
| `test/total.test.cjs` | `require('node:test')('sums two numbers', () => require('node:assert/strict').equal(require('../index.cjs').total([1, 2]), 3));` |
| `.github/workflows/checks.yml` | A workflow with a pull request trigger and a job that checks out the repository, sets up Node.js, and runs `npm test`. |

Save `git status --short --untracked-files=all` before and after each run. For a fixture with a commit, save `git diff HEAD --` too. Inspect untracked files separately, including all generated files in a new project. Reset a case by opening a fresh copy of the fixture.

## Exercise the skill

Start each prompt with `Use Poyehali at <source> to configure this project.` Then add the text for the case. Use any questions the client asks as part of the test.

| Case | Prompt continuation | Pass when |
| --- | --- | --- |
| Default scope and complete inputs | `This is a new internal UI for viewing inventory. It uses Vite, has one browser-only app, an existing external HTTP API, and no indexed URLs. Use the default Vue frontend preset and the default setup mode. Apply it.` | The agent asks zero setup questions. It writes the local rules, architecture and checks guidance. App files, configs, dependencies and CI are absent. Checks that need an implementation are recorded as planned. |
| Preset or customization | `This is a small new internal frontend SPA with no API. Use the agent rules mode. Show your recommendation before applying it.` | The agent previews the entire Vue preset in one choice. Accept it in one run and verify there are no repeats about style, lint, architecture or store. In a fresh run ask to customize styling and architecture, choose SCSS and feature modules, and verify the rest of the preset carries forward. |
| Conditional interview | `This is a small Vue public catalog with the default frontend preset. Its public product URLs need search indexing, and there is also a private admin area. Use the agent rules mode. Ask only questions whose answers would change the recommendation.` | The agent uses the given UI, audience, SEO scope and mode. It announces the number of questions in the round, asks at most three, and recommends an answer to each. In one run answer `Product data is known at build time`. In a fresh run answer `Product data changes on each request, and a server runtime is allowed`. Verify prerender and SSR respectively, with an explicit rendering choice for admin. |
| Configs | `Set up a new internal Vue/Vite project for managing personal bookmarks with TypeScript, Legato UI and pnpm. It is an SPA with no backend. Choose the configs mode and apply it.` | The agent creates the docs and selected tooling configuration. There is no application entry point, feature, or synthetic passing test. The report distinguishes runnable checks from checks waiting for source or a browser app. |
| Local Vue scaffold | `Set up a new Vue/Vite and Legato UI SPA for personal bookmarks with TypeScript and pnpm. No public indexing and no backend. Choose the local application scaffold and apply it.` | A minimal Legato UI app installs and runs locally. Build, typechecking, the unit check and one Playwright smoke can be run from the documented commands. Report any unavailable tool or browser as blocked. |
| Local Nuxt scaffold | `Set up a new Vue/Nuxt app for a public catalog with Legato UI and pnpm. Product pages need HTML based on request-time data. The app has a small integrated API under server/api and can run on a Node server. Choose the local application scaffold and apply it.` | The local smoke verifies the integrated endpoint, public HTML on the initial response and a hydrated interaction. |
| Separate API | `Set up a personal bookmarks project with an internal Vite/Vue and Legato UI frontend, pnpm, and a separate HTTP backend in the same Git repository. Use [FastAPI / Go with net/http]. No database or indexable routes. Choose the local application scaffold and apply it.` | Run twice with separate fixtures and one explicit backend each time. The result includes only the selected backend, a documented API boundary, and a local smoke through that boundary. Checks use Ruff and pytest for Python or gofmt, vet and Go tests for Go. |
| Existing project and rerun | `This is an existing Node.js utility. Adopt the default agent rules mode and apply it. Keep my existing tools and local edits.` | The agent routes to existing architecture and development docs, retains the custom test command and existing instructions, and preserves the tracked edit and untracked note. Rerun the same task in a fresh chat. File contents and Git status are identical to the first result. |
| Changed file at apply | `Adopt the default agent rules mode for this existing project. Show the proposed file changes and pause before applying them.` | Once the agent shows its preview, append a distinctive line to a file it planned to edit and reply `Apply that plan, preserving the change I just made.` It reports the stale path, preserves the new line, and applies only independent changes that remain in scope. |

For an ambiguous scope in a new project, also try `Set up an internal inventory UI. It will use Vue/Vite, pnpm and Legato UI, with no backend or SEO. Show my options before applying.` The presented choices are agent rules, rules plus configs, and rules plus local scaffold. Agent rules are recommended. Reply `Use the recommended option` and verify the rules-only output.

In any case with a pre-existing failing command, capture the baseline and let the command fail. A reported existing failure must correspond to an observed run. An unrun command must remain unverified.

## Check decomposition in later code changes

Set up agent rules for a fixture with handwritten source. Start a fresh session with the target's local instructions and a normal feature request, without providing the Poyehali URL. Use independent fixtures for these cases.

| Case | Pass when |
| --- | --- |
| An edit exceeds 300 lines | The agent compares file size before and after. It flags the file and identifies a possible extraction by responsibility or explains why it is cohesive. |
| An edit reaches 500 lines | The agent decomposes the code if the task permits it, checks size and behavior again, and reports the result. If the split would expand scope, it explains why and names a concrete boundary. |
| A small fix in an existing 700-line module | The agent flags the module and its size as an existing issue. The diff preserves the scope of the fix. |

## Check the Git handoff

- Start once in a standalone folder without Git and once in a repository without an initial commit. Verify the agent identifies the two cases before editing and offers initialization only in the first. Open a target subfolder inside a parent repository and verify the agent identifies the real root.
- In a configured fixture, leave an unrelated file staged and make a small task change in a different file. After verification, expect an offer to commit or show the diff. Request the diff, then compare HEAD and the staged diff with the snapshots from before the offer. Both should match. Explicitly request the commit and inspect it. It should contain the task change, with the unrelated edit still staged.
- Run an independent task with explicit permission to commit when ready. Expect one verified local commit without a second question. For an unchanged rerun, expect no commit proposal.

## Run the four-client check

1. Open the existing-project fixture in a fresh client session with no Poyehali installation. Send only the public repository URL. Verify the client opens the README or skill entry and asks whether to configure the workspace if intent is unclear. If it cannot open the URL, record what it could access, then exercise the local-copy fallback separately.
2. In a separate fresh session on the same fixture, send the existing-project prompt with the public URL. Verify the resulting rules and diff. If testing a repo-local Claude adapter, inspect the active `CLAUDE.md` or native `AGENTS.md` loading for that version and record which path was actually used.
3. Start another fresh session in the configured project and send `Add a regression test that proves total([]) returns 0.` Confirm that the agent finds the local guidance without the Poyehali URL, puts the test under `test/`, runs `npm test`, and keeps prior edits.
4. Record the client version and surface, Poyehali revision, observed diff, command results and blockers. Use `pass`, `fail`, `blocked`, or `pending` for each scenario. Keep evidence free of credentials and machine-specific secrets.

| Client | Version tested | URL and onboarding | Follow-up task |
| --- | --- | --- | --- |
| Codex | CLI 0.159.1 attempted | Public URL pending. Local CLI blocked at Windows shell launch. | Local desktop-agent rehearsal passed. Fresh standalone client pending. |
| DeepSeek Harness | Pending | Pending | Pending |
| Antigravity | Pending | Pending | Pending |
| Claude Code | Pending | Pending | Pending |

Before tagging a release, validate the skill frontmatter and local Markdown references, run the fixture cases, and carry the observed client results into the README. Keep an untested client or scenario marked as pending.

## Local rehearsal on 2026-11-23

| Scenario | Observed result |
| --- | --- |
| Skill manifest and references | The skill validator passed. Internal Markdown links resolved. |
| Rules only, new Vue project | The desktop agent worked from the local checkout. It asked zero questions for complete input and created exactly the four rule documents. Checks that needed app code were marked planned. |
| Vue/Vite configs only | The agent generated a pnpm lockfile and installed with a frozen lockfile. ESLint and the configuration type check passed, and Vite loaded the Vue plugin. The fixture contains no app entry point, tests, or CI jobs that depend on them. |
| Conditional rendering interview | A separate desktop agent reused the supplied scope, Vue stack, and public routes. Build-time data and static hosting led to Nuxt prerendering. Request-time data and a server runtime led to SSR. Both previews kept the private admin behind the existing access boundary. |
| Adoption and rerun | With a dirty Node.js fixture, the agent reused the existing architecture and development docs, kept custom commands and user edits, and produced identical file hashes and Git status on repeat. `npm test` passed. |
| Stale planned path | A line was appended to `AGENTS.md` after the preview. The agent preserved the file and applied independent doc edits. Existing instruction links still resolved. |
| Follow-up on local rules | A separate desktop agent received only the target folder and test request. It followed `AGENTS.md` and the development guide, added one test, and ran the prescribed `npm test` successfully. |
| FastAPI app recipe | `uv sync --locked` produced an isolated environment. Ruff lint and format checks on `app/` and `tests/` passed, the pytest module invocation passed, and a local Uvicorn server returned HTTP 200 and the expected JSON at `GET /api/health`. |
| Go app recipe | Go 1.27.4 ran the formatting, vet, and test checks and built the standalone API. The built process returned HTTP 200 and the expected JSON at `GET /api/health`. |
| Nuxt SSR with Legato UI | With cached dependencies, the app passed the production build, typecheck, ESLint, and two unit tests. The built Node artifact served server-rendered content and the health response. Two Playwright tests passed in Microsoft Edge, including an interaction with the hydrated Legato control. The clean generator and lockfile path remains blocked by npm download and cache errors. |

The standalone Vite app and combined split UI/API cases are pending. The four-client public URL check is tracked separately above.

## Frontend preset and decomposition rehearsal

In a read-only onboarding rehearsal, the agent offered one choice between the Vue preset and customization. Accepting the preset or choosing only SCSS and feature modules led directly to a four-document preview.

In a separate read-only review, the agent used the fixture's local instructions to count three changed source files. It flagged 299 to 305 lines with a named extraction, required an in-scope split at 495 to 505, and reported an existing 700-line file with a one-line fix without adding a refactor to that task.

## Git handoff rehearsal

The local checks distinguished a folder without Git, a repository without an initial commit, and a subfolder of an existing repository. In the task fixture, a separate agent ran the target's test, then offered a commit or diff review. Showing the diff preserved HEAD and the existing staged change. After approval, the local commit contained only the two task files, and the unrelated change remained staged.
