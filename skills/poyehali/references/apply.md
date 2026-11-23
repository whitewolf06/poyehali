# Apply Poyehali

## Prepare and preview

- Render the result for the decisions already made. Name the target, mode, and each intended create or edit. List the checks you can actually run and any prerequisites. Mark already-satisfied items as unchanged and omit them from the writes.
- Find the project-owned document serving each purpose in [the document formats](documents.md). In an existing `AGENTS.md`, add or amend at most one section using the markers defined there. Keep existing content and ordering outside it. When the agreed update adds a new review requirement, include it in the existing local guidance. Keep an already complete file byte-for-byte.
- Treat an existing managed section as project content. Preserve additions and update only text affected by a resolved decision. Reuse the recorded Poyehali version on identical reruns. Record an upgrade only when the user requests it or has accepted the resulting substantive change. Avoid timestamps, generated progress logs, and formatting churn.
- Work in the authorized directory. For a split project with more than one Git root, change each explicitly authorized location only when it is available. Record the other side as planned when it is outside the available workspace.

## Write safely

1. Keep the exact contents or a digest of each file when preparing the preview. For a new file, record that the path was absent.
2. Before writing, compare the targeted file to that baseline and confirm the path is still inside the intended project. Use the editor's matching-content or patch-context support where available. Recheck absence before creation and use exclusive creation when supported. Preserve newline style and encoding for edits.
3. Treat an unexpected change, path collision, ambiguous marker pair, or irreconcilable instruction as a conflict. Keep the current contents. Continue independent changes only, and list the blocked paths and consequences. If an operation affects one file that is necessary for a coherent group of changes, skip the dependent group too.
4. Make the smallest edit. Use existing configuration, scripts, and CI jobs where applicable. Review the resulting diff, including new files, against the preview and the original dirty state. Verify internal links resolve. For a rerun, compare the working tree before and after the invocation, including untracked files and their contents. Existing dirt is compatible with a zero-diff rerun.

## Tooling and validation by mode

- **Rules.** Modify documentation and agreed instruction adapters. Verify the routes to project docs, verify references to existing commands against their manifests or CI, and label future checks `planned`. Document the relevant check for each kind of normal task.
- **Configs.** Prefer check-only lint, format, and type-check commands. Keep the existing package manager and unrelated dependency ranges. Update or create the lockfile through that manager for approved dependency changes. In a new project with no source or test target, configure only tooling that can be validated as configuration, record dependent gates as planned, and skip activating jobs that would fail or pass without doing meaningful work. When code and tests exist, link applicable CI checks to the same commands a developer can run locally.
- **App.** For a new app, use the selected profile or official tooling. If a generator requires an empty directory, generate separately and review the files before merging them into the target. Keep sample behavior small and deterministic. Where the architecture calls for an API, verify its response through HTTP. Where the UI calls the API, verify the request and user-visible result together. For SSR, verify initial HTML and hydration. Record the actual checks with their status.

When a prerequisite is missing, preserve valid independent work. Record a check that needs further project setup as `planned` in the quality guide, and use `blocked` in the execution report when a missing prerequisite prevents required verification. Run checks in the relevant package directory and isolate test inputs from production. Report local runs, CI runs, and any future deployment validation as distinct evidence.
