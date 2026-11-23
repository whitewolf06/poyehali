---
name: poyehali
description: Set up agent instructions and engineering practices in a new or existing code repository, with optional tooling configuration or a minimal local application. Use when asked to apply Poyehali, onboard coding agents, or establish a project's architecture, coding rules, and verification baseline. Routine feature work follows the target repository's own instructions.
license: MIT
---

# Poyehali

The outcome is a target repository whose next agent can find its rules, boundaries, and checks locally. Read this file directly when the user shares the Poyehali URL or a local checkout. Installation is optional. If reading a remote source, resolve the requested tag or branch to one commit and read references at that commit. Keep the Poyehali checkout separate from the target workspace.

## Workflow

1. **Inspect the target.** Check its [Git status and root](references/git.md#check-git-when-starting) first. Identify whether it has application code, the relevant subprojects, and the files and changes already present. Read applicable agent instructions, README and canonical docs, manifests and lockfiles, CI, test configs, and a representative implementation. Identify the existing stack, boundaries, and real check commands. An initial README or license alone is compatible with a new project. If the target is ambiguous, ask which folder to use.
2. **Resolve pending decisions.** Use [the interview](references/interview.md). Prefer explicit user answers and evidence from the project. Treat the profiles as recommendations for open decisions. Retain existing architecture and tools during adoption, unless the user explicitly scopes a change to them. Surface material conflicts between the request, existing rules, and the code.
3. **Select the depth.** Reuse an explicit choice. Otherwise offer `rules` (recommended), `configs`, and `app`, explaining what each adds. A request to start with the agent rules selects `rules`. A fully specified request can go straight to the proposed changes.
4. **Prepare a file-level preview.** Use [the document formats](references/documents.md) and [the application protocol](references/apply.md). Show the resolved architecture and scope, files to create or amend, commands to run, and any known gap or conflict. Make the size and responsibility review part of the target project's local rules so it applies in future coding sessions. If the user asked to apply that scope, proceed after the preview. A link alone or a request for advice authorizes inspection and a proposal. Apply when the user requests changes.
5. **Apply and verify.** Recheck the affected paths and preserve unrelated changes, then follow the verification steps in [the application protocol](references/apply.md). Report the files changed, actual command results, and outstanding checks or conflicts. If the update is ready for a local commit, follow the [Git workflow](references/git.md#finish-the-task-and-offer-a-commit).

## References to load as needed

| Project choice | Reference |
| --- | --- |
| Selecting scope, rendering, stack, or topology | [Interview](references/interview.md) |
| Writing or adopting the local documentation | [Document formats](references/documents.md) |
| Applying any mode, verifying it, or rerunning adoption | [Application protocol](references/apply.md) |
| Git discovery, diff review, and commits | [Git workflow](references/git.md) |
| Vue, Legato UI, Vite, Nuxt, or Nuxt server routes | [Vue profile](references/vue.md) |
| Separate Python API | [Python profile](references/python.md) |
| Separate Go API | [Go profile](references/go.md) |

For other stacks, follow the same interview and documentation workflow. Use the repository's tools and layout. For a new stack without a profile, verify the official tooling and record the selected commands and boundaries before writing configuration or application code.

Keep all changes within the authorized target and mode. Commits, pushes, releases, deployment, global setup, and writes to another repository require their own scope. A local smoke test uses a local process and synthetic inputs.
