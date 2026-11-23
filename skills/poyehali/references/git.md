# Git workflow

## Check Git when starting

- In the intended target, use `git rev-parse --show-toplevel` to find the worktree root. Compare it with the requested scope. A target subfolder can belong to an existing repository, and a worktree can have a `.git` file.
- In a worktree, use `git status --short --branch --untracked-files=all` to see the branch and existing staged, unstaged, and untracked changes. Remember the baseline for paths you will touch. An initialized repository without a first commit is valid. If the root or branch is ambiguous, resolve the scope before changing Git state.
- If Git reports that the target is outside a repository, propose `git init` for a new standalone project and run it when authorized. If Git itself is unavailable or Git status fails for another reason, report the limitation and continue setup that does not depend on Git.

## Finish the task and offer a commit

Follow the existing project's Git rules. For a new project or one with no policy, use this workflow.

1. Complete the agreed work. When the required checks pass and there is a task diff, summarize the result and checks, and propose one local commit for the logical change. Report failed or blocked required checks as such.
2. Ask a direct question such as **“The task is ready. Commit it now? I can show the diff first.”** Keep the index untouched while the user decides. Do not commit until the user approves. If they ask to review, show the proposed changes including new files and wait for a subsequent commit command. Approval to apply the setup or to show the diff does not approve a commit.
3. If the user already asked to commit this task when ready, proceed after verification without asking again. A push requires separate authorization.

## Prepare the authorized commit

- Recheck the intended branch and the task diff before staging. If the proposed content changed since review, show the updated diff and get approval for that version. Preserve pre-existing staged changes and unrelated work. When edits share a file, isolate the task's hunks. If you cannot isolate them safely, report the path and wait for direction.
- Review the exact content to be committed. For a normal staged commit use `git diff --cached` and `git diff --cached --check`. If the index also contains other work, use a scoped commit only when you can verify it will contain only the agreed content and preserve that work. A whole-file commit is only appropriate when the whole file change belongs to the task.
- Use the project's message format, including its ticket references where required. When none exists, use Conventional Commits, for example `feat(frontend): add Vue preset`. After committing, report the short commit ID. [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/)
