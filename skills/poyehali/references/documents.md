# Project documents

Use this reference after resolving the project scope, stack, and setup mode. Write in the language already used by the project's engineering docs, or the language of the onboarding request for a new project. Keep the rules specific enough that an agent can follow them in a new session using only the target checkout.

## Choose the canonical files

| Subject | Default path for a new project | Look for in an existing project |
| --- | --- | --- |
| Agent instructions and reading order | `AGENTS.md` | Root and scoped agent instructions |
| Responsibilities and architecture decisions | `docs/architecture.md` | Architecture overview, module map, or active decision records |
| Implementation conventions | `docs/coding-standards.md` | Development guide, contributing guide, or stack-specific rules |
| Commands and acceptance criteria | `docs/quality.md` | Test guide, contributor checks, or CI documentation |

Keep an existing document as the canonical source when it already serves the purpose. One existing guide may cover several subjects. Link directly to its relevant sections and add missing information there. Use the default path when the subject has no suitable home and the path is available. Change only the sections relevant to the agreed scope.

Use repository-relative links to real files and headings. Record facts from the repository as current. In an empty repository, record agreed architecture as planned and label proposed paths and commands accordingly. Omit unused headings, sample text, and placeholder paths from generated files. When an unresolved decision affects how agents should work, record the open question and what it blocks.

## Agent instructions

In an existing `AGENTS.md`, put Poyehali's additions in a single section bounded by `<!-- poyehali:start -->` and `<!-- poyehali:end -->` on separate lines. An existing pair of `<!-- agent-starter:start -->` and `<!-- agent-starter:end -->` marks the same section. Reuse that pair in place when present. Treat mixed or duplicated pairs as a conflict. Preserve instructions outside the section and omit rules already covered elsewhere in the file. In a new project put the section under a single `# Agent instructions` heading.

Adapt this section to the actual project. Its example links assume the default paths.

```markdown
<!-- poyehali:start -->
## Working in this project

- Read [architecture](docs/architecture.md) before changing a module boundary, [coding standards](docs/coding-standards.md) for the code you touch, and [quality checks](docs/quality.md) before reporting results. Read scoped instructions for the directories you edit.
- Keep changes within the requested feature and preserve unrelated work. Agree changes to shared interfaces, ownership, or the requested scope before implementing them.
- Run the available checks that cover the changed behavior. In your summary name the actual commands and results, and explain any required check you could not run.
<!-- poyehali:end -->
```

Add one or two essential project-specific rules when an agent needs them on every task. Keep details and examples in the owning document. A scoped `AGENTS.md` makes sense when a service or package has a distinct owner, constraints, or check commands. Link it from the root when needed for discovery. In the scoped file, name its directory and add only the relevant differences, using links relative to that file. Check how the selected client loads nested instructions and have the root direct agents to read them before editing when needed.

For Claude Code, leave a working instruction setup in place. When the selected client needs an adapter and a root `CLAUDE.md` is absent, create it with `@AGENTS.md` as its only line. If a committed Claude instructions file is already used, add the appropriate relative `@` import only if it does not already load the same rules. Preserve the file's existing guidance and surface incompatible instructions before adding an import. Treat user-local configuration as read-only. [Claude Code imports](https://code.claude.com/docs/en/memory#import-additional-files)

## Architecture

Use the following headings where the corresponding decisions apply.

- **Purpose and scope** — State the outcome and audience in one or two sentences. Name important external dependencies or existing interfaces.
- **Current or planned shape** — Record the selected components, stack, and repository layout. For UI projects, name the routes that need indexing and the selected client, static, server, or hybrid rendering mode. For UI plus API, distinguish repository layout from runtime boundaries.
- **Module and data boundaries** — List the main components with their responsibilities, allowed dependencies, shared interfaces, and data owners. For split projects, spell out which side owns API validation and which side consumes the contract. Name the code directories only when known or explicitly planned.
- **Decisions** — Record choices and why they matter. Link to any existing decision records. Mark each relevant planned decision or undecided boundary explicitly. Add the source tag or commit of Poyehali, or `local checkout` if the source has no known revision. Keep the version stable on a rerun with unchanged decisions unless the user requested a source update.

## Coding standards

Include rules an agent can apply to a concrete edit. Choose the sections and rules that match the actual stack. Point to existing representative code, conventions, and tool configuration where available.

- **Location and ownership** — Explain where a route, reusable UI component, state transition, request handler, or persistence change belongs. Name the established interface used to cross a module boundary.
- **Change conventions** — Cover typing and validation at boundaries, error handling, dependency additions, and behavior changes. Add relevant stack-specific expectations such as accessible UI states, SSR-safe code, isolated domain logic, or migrations.
- **Interfaces and compatibility** — State how API or data contract changes get versioned, tested, or agreed when the project has that contract. Keep executable commands in the quality guide, linking to it from here as needed.

Use the selected stack references as a starting point and preserve verified local conventions. Where the project is still planned, express selected decisions as rules for the first implementation and avoid invented examples of existing code.

## Quality guide

Record the exact command, its working directory, what it verifies, and any prerequisites. If CI already invokes it, identify the workflow and job. Document the project's criteria for focused unit tests, integration tests across real boundaries, and E2E for critical user flows. An API-only project can exercise its public HTTP surface without a browser. Record database migration or rendering checks when those features exist or are planned.

Use this form for each applicable check.

```markdown
| Check | Command and working directory | Availability | When required |
| --- | --- | --- | --- |
```

- `existing` — A check already had the required command, configuration, and test surface in the project.
- `configured` — This setup made the check runnable through the listed command. Name any known tool or runtime prerequisites.
- `planned` — A required setup, test surface, or implementation is still missing. Name what is missing and label any suggested command as planned.

Availability describes the project. Execution results belong in the setup report as `passed`, `failed`, `blocked`, or `not run`, supported by commands actually attempted and the relevant output. Preserve existing failing checks and report them separately from any regression caused by setup. Keep transient logs and run results out of the standing rules.

## Maintain the rules

When a later change affects a boundary, convention, or check, update its canonical document in the same change. Update the links in `AGENTS.md` when relevant routes move. Convert planned instructions to current after the capability exists and its command has been verified. Reuse verified decisions and inspect the existing managed section before proposing any new edits.
