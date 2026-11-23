# Agent Starter

Give a coding agent this repository's link while it is working in a new or existing project. It will inspect the project, ask about the decisions that are still open, and write the instructions the next agent will use.

[Быстрый старт на русском](docs/quick-start.ru.md)

## Use it

Open the **target project** in your agent. Paste this message, optionally followed by the project brief and any choices you have already made.

> Set up this project using https://github.com/whitewolf06/agent-starter. Start with the agent rules.

**Agent entry point:** read [`skills/agent-starter/SKILL.md`](skills/agent-starter/SKILL.md) and load the relevant references from the same Git revision. Apply them in the open project. If the agent cannot fetch GitHub, point it to a local checkout of this repository instead. For reproducible runs, use a tag or commit URL.

## Choose the result

| Mode | Result in your project |
| --- | --- |
| **Agent rules** (default) | Project-local instructions, architecture, coding standards, and a verification guide. |
| **Rules + configs** | Rules plus applicable lint, type-check, test, and CI configuration. |
| **Rules + local app** | Rules and tooling plus a minimal runnable UI, API, or pair, with a local smoke test. |

The agent previews the edits, preserves existing project conventions and working changes, and reports the checks it actually ran. For continuing work, the target's `AGENTS.md` routes agents to the local architecture, coding standards, and verification guide. It can point to documentation that already exists.

For a new Vue project, the recommended UI kit is [Legato UI](https://github.com/whitewolf06/legato-ui). The [Vue profile](skills/agent-starter/references/vue.md) covers Vite SPA, Nuxt prerendering and SSR, and Nuxt server routes. For an independent API, choose [Python/FastAPI](skills/agent-starter/references/python.md) or [Go](skills/agent-starter/references/go.md). Existing or explicitly selected stacks use their own tools and conventions.

## Optional skill installation

Copy the entire [`skills/agent-starter`](skills/agent-starter) directory from a chosen revision to the skill directory of your target project. A new session is a reliable way to check that your client picked it up.

| Client | Target directory | Link, adoption and follow-up |
| --- | --- | --- |
| [Codex](https://learn.chatgpt.com/docs/build-skills) | `.agents/skills/agent-starter/` | Local desktop rehearsal passed. Standalone CLI 0.159.1 blocked at Windows shell launch. Public URL pending. |
| [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness/blob/master/docs/subsystems/skills.md) | `.agents/skills/agent-starter/` | Pending |
| [Antigravity](https://antigravity.google/docs/skills) | `.agents/skills/agent-starter/` | Pending |
| [Claude Code](https://code.claude.com/docs/en/skills) | `.claude/skills/agent-starter/` | Pending |

For Claude sessions that load `CLAUDE.md`, request an import of the shared rules into that file. This keeps Claude-specific guidance in place and uses one copy of the project rules. [Claude project instructions](https://code.claude.com/docs/en/memory#share-one-file-with-other-coding-tools)

See [release acceptance](docs/acceptance.md) for the test cases and evidence. [MIT license](LICENSE).
