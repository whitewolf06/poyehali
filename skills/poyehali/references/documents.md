# Project documents

Use this reference after resolving the project scope, stack, and setup mode. Write in the language already used by the project's engineering docs, or the language of the onboarding request for a new project. Keep the rules specific enough that an agent can follow them in a new session using only the target checkout.

## Choose the canonical files

| Subject | Default path for a new project | Look for in an existing project |
| --- | --- | --- |
| Agent instructions and reading order | `AGENTS.md` | Root and scoped agent instructions |
| Responsibilities and architecture decisions | `docs/architecture.md` | Architecture overview, module map, or active decision records |
| Implementation conventions | `docs/coding-standards.md` | Development guide, contributing guide, or stack-specific rules |
| Commands, acceptance criteria, and Git workflow | `docs/quality.md` | Test guide, contributor guide, or CI documentation |

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
- Before finishing, review the responsibilities of the changed files. Use the coding standards to apply size thresholds to executable code, including backend modules and scripts, and to UI components. Review static HTML, standalone styles, and declarative data or content for structure and cohesion.
- Run the available checks that cover the changed behavior. In your summary name the actual commands and results, and explain any required check you could not run.
- Check Git status before editing and follow the [local Git workflow](docs/quality.md#git-workflow) before making a commit.
<!-- poyehali:end -->
```

Add one or two essential project-specific rules when an agent needs them on every task. Keep details and examples in the owning document. A scoped `AGENTS.md` makes sense when a service or package has a distinct owner, constraints, or check commands. Link it from the root when needed for discovery. In the scoped file, name its directory and add only the relevant differences, using links relative to that file. Check how the selected client loads nested instructions and have the root direct agents to read them before editing when needed.

For Claude Code, leave a working instruction setup in place. When the selected client needs an adapter and a root `CLAUDE.md` is absent, create it with `@AGENTS.md` as its only line. If a committed Claude instructions file is already used, add the appropriate relative `@` import only if it does not already load the same rules. Preserve the file's existing guidance and surface incompatible instructions before adding an import. Treat user-local configuration as read-only. [Claude Code imports](https://code.claude.com/docs/en/memory#import-additional-files)

## Architecture

Use the following headings where the corresponding decisions apply.

- **Purpose and scope** — State the outcome and audience in one or two sentences. Name important external dependencies or existing interfaces.
- **Current or planned shape** — Record the selected stack and layout. For UI projects, name the roles of routes, UI, state, application or domain code, and I/O. Record why shared state needs a store when one is chosen. Name the routes that need indexing and their rendering mode. For UI plus API, distinguish repository layout from runtime boundaries.
- **Module and data boundaries** — List the main components with their responsibilities, allowed dependencies, shared interfaces, and data owners. For split projects, spell out which side owns API validation and which side consumes the contract. Name the code directories only when known or explicitly planned.
- **Decisions** — Record choices and why they matter. Link to any existing decision records. Mark each relevant planned decision or undecided boundary explicitly. Add the source tag or commit of Poyehali, or `local checkout` if the source has no known revision. Keep the version stable on a rerun with unchanged decisions unless the user requested a source update.

## Coding standards

Include rules an agent can apply to a concrete edit. Choose the sections and rules that match the actual stack. Point to existing representative code, conventions, and tool configuration where available.

- **Location and ownership** — Explain where a route, reusable UI component, state transition, request handler, or persistence change belongs. Name the established interface used to cross a module boundary.
- **Change conventions** — Cover typing and validation at boundaries, error handling, dependency additions, and behavior changes. Record the lint preset, styling approach, and any stack-specific expectations such as accessible UI states, SSR-safe code, isolated domain logic, or migrations.
- **Interfaces and compatibility** — State how API or data contract changes get versioned, tested, or agreed when the project has that contract. Keep executable commands in the quality guide, linking to it from here as needed.

Use the selected stack references as a starting point and preserve verified local conventions. Where the project is still planned, express selected decisions as rules for the first implementation and avoid invented examples of existing code.

### Review size and responsibility

Put these checks in the project's coding standards, with a short instruction in the relevant `AGENTS.md`. The default line thresholds apply across stacks to handwritten executable code and UI components. Executable code includes backend handlers and services, workers, command-line tools, and build or automation scripts. Review a file according to its role, including when code and data share an extension. Use established project thresholds for eligible code and components where they exist.

- Give each component, class, function, and module a single responsibility. Look for independently changing concerns such as rendering, orchestration, validation, domain decisions and I/O. Preserve readable whitespace and formatting in code and styles. Treat compression solely to reduce line counts as a readability problem.
- Review static HTML pages, including FAQ and JSON-LD, standalone stylesheets such as CSS and SCSS, and purely declarative data or content for structure, readability, and cohesion. This includes dictionaries, locales, lesson catalogs, and data-only fixtures, even when exported from JS or TS. Keep these files outside the line thresholds. Flag or split them when a specific structural change makes them easier to maintain.
- In a document or data file with embedded behavior, review and count executable units separately, such as an inline JavaScript block. Treat JSON-LD and similar declarative payloads as data. Count an authored Vue SFC as a UI component, including its template, script, and style blocks.
- Before editing, note the size of affected code or components that fall under the thresholds. Compare their physical lines before and after, including comments and blank lines. Skip generated, vendor, bundle, and lock files. Measure large changed components, classes, functions, and methods from their first to last line. When one contributes to a finding, give its name and size. Apply any project-specific limits for those units.
- **301–499 lines of applicable code or component source.** Flag it in the task summary with its before and after counts. Name a responsibility that could move into its own unit, or explain why keeping this cohesive code together serves the current task.
- **500 lines or more of applicable code or component source.** Flag this as a stronger issue. If the task added a file or executable unit at this size, took it across the threshold, or substantially grew an already oversized one, decompose it by responsibility before finishing when the split fits the task. If a split is blocked or requires expanding scope, report the constraint and the concrete boundary to extract. For a narrow fix in an already oversized file or unit, report the existing size and concern without turning the fix into a broad refactor.
- Extract a named unit with a clear owner, interface, or reason to change. Keep nearby code together when it serves that same purpose. Avoid numbered fragments, pass-through helpers, or moving styles simply to lower the count. Recheck the size and affected behavior after a split.

These thresholds guide code review. Add an automated size gate only if the project has one or the user chooses it.

## Quality guide

Record the exact command, its working directory, what it verifies, and any prerequisites. If CI already invokes it, identify the workflow and job. Include the responsibility review and applicable code or component size thresholds as a manual completion check. Point to the local coding standards for how to review static content, styles, and declarative data. Document the project's criteria for focused unit tests, integration tests across real boundaries, and E2E for critical user flows. An API-only project can exercise its public HTTP surface without a browser. Record database migration or rendering checks when those features exist or are planned.

Use this form for each applicable check.

```markdown
| Check | Command and working directory | Availability | When required |
| --- | --- | --- | --- |
```

- `existing` — A check already had the required command, configuration, and test surface in the project.
- `configured` — This setup made the check runnable through the listed command. Name any known tool or runtime prerequisites.
- `planned` — A required setup, test surface, or implementation is still missing. Name what is missing and label any suggested command as planned.

Availability describes the project. Execution results belong in the setup report as `passed`, `failed`, `blocked`, or `not run`, supported by commands actually attempted and the relevant output. Preserve existing failing checks and report them separately from any regression caused by setup. Keep transient logs and run results out of the standing rules.

### Git workflow

Write the project-specific [Git workflow](git.md) into the local quality or contributor guide and link to it from `AGENTS.md`. Reuse established project rules. For the default workflow, persist the initial worktree check, the choice to commit or review the diff, explicit approval after review, and the exception for a task that already includes commit authorization. Record selective staging, the message format, and separate authorization for push. The local rules must be usable without reading Poyehali again.

## Maintain the rules

When a later change affects a boundary, convention, or check, update its canonical document in the same change. Update the links in `AGENTS.md` when relevant routes move. Convert planned instructions to current after the capability exists and its command has been verified. Reuse verified decisions and inspect the existing managed section before proposing any new edits.
