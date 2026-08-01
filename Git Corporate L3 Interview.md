# Git Corporate L3 — Interview Questions and Detailed Answers

> **Target audience:** Senior Linux/DevOps/SRE/Platform engineers with approximately 8–12+ years of experience  
> **Interview level:** Corporate L3 / Senior Administrator / DevOps Lead / Platform Engineer  
> **Scope:** Git internals, enterprise workflows, branching, merging, rebasing, recovery, security, automation, performance, governance, migration, and production troubleshooting  
> **Document date:** 2026-08-01

---

## Table of Contents

1. [Git Architecture and Internals](#1-git-architecture-and-internals)
2. [Working Tree, Index, Commits, and References](#2-working-tree-index-commits-and-references)
3. [Branching, Merging, and Rebasing](#3-branching-merging-and-rebasing)
4. [Remote Repositories and Distributed Workflows](#4-remote-repositories-and-distributed-workflows)
5. [Undo, Recovery, and Incident Handling](#5-undo-recovery-and-incident-handling)
6. [Repository Security and Governance](#6-repository-security-and-governance)
7. [Large Repository Performance and Maintenance](#7-large-repository-performance-and-maintenance)
8. [Submodules, Subtrees, Worktrees, and Monorepos](#8-submodules-subtrees-worktrees-and-monorepos)
9. [Git Hooks, CI/CD, and Automation](#9-git-hooks-cicd-and-automation)
10. [Migration, Backup, and Disaster Recovery](#10-migration-backup-and-disaster-recovery)
11. [Corporate L3 Scenario-Based Questions](#11-corporate-l3-scenario-based-questions)
12. [Rapid-Fire Command Reference](#12-rapid-fire-command-reference)
13. [Senior-Level Interview Checklist](#13-senior-level-interview-checklist)
14. [Official References](#14-official-references)

---

# 1. Git Architecture and Internals

## Q1. What is Git, and how is it different from a centralized version-control system?

### Answer

Git is a **distributed version-control system**. Each normal clone contains the project history and an object database, not merely a writable copy of the latest files.

In a centralized system, the central server is normally required for most history operations. In Git, operations such as `log`, `diff`, `branch`, `commit`, `merge`, and many forms of recovery are local.

A corporate L3 answer should mention the following:

- Git stores snapshots of project state rather than a simple sequence of file deltas at the logical model level.
- Commits are connected through parent references, forming a directed acyclic graph.
- Branches and tags are references to objects.
- Most work is local; synchronization is explicit through fetch and push.
- The central repository used by a company is a collaboration and governance point, not a technical requirement of Git's data model.
- Distribution improves availability but does not replace enterprise backup, access control, branch protection, or disaster-recovery design.

### Interview follow-up

**Does every clone constitute a complete backup?**

Not always. A full non-shallow clone normally contains all reachable objects advertised and transferred by the server, but it may not contain:

- Server-only refs
- Reflogs
- Pull-request refs
- Hidden or restricted refs
- LFS objects unless downloaded
- Submodule repositories
- Server configuration, hooks, permissions, audit data, or issue-tracker data
- Objects intentionally omitted by shallow or partial clone

---

## Q2. Explain Git's object model.

### Answer

The central objects are:

| Object | Purpose |
|---|---|
| Blob | Stores file content |
| Tree | Represents a directory and maps names to blobs or subtrees |
| Commit | Points to a root tree, parent commit(s), author/committer metadata, and message |
| Annotated tag | Names another object and can contain a message and signature |

Objects are content-addressed. An object ID is calculated from the object type, length, and content. Identical content can therefore reuse the same object.

A commit does not contain a branch name. A branch name is only a reference that currently points to a commit.

### Useful commands

```bash
git cat-file -t HEAD
git cat-file -p HEAD
git ls-tree -r HEAD
git rev-parse HEAD
git count-objects -vH
```

### What the output tells you

- `git cat-file -t HEAD` identifies the type of the object referenced by `HEAD`.
- `git cat-file -p HEAD` prints the commit object in human-readable form.
- `git ls-tree -r HEAD` shows the files represented by the commit's tree.
- `git rev-parse HEAD` resolves a revision expression to an object ID.
- `git count-objects -vH` summarizes loose and packed objects.

---

## Q3. Why does modifying one line produce new Git objects?

### Answer

When a tracked file changes and is committed:

1. Git creates a new blob for the changed file content.
2. Git creates new tree objects for affected directory paths.
3. Git creates a new commit object pointing to the new root tree and its parent commit.
4. Unchanged blobs and unaffected trees can be reused.

Git does not modify the old commit or old blob. This immutability is why historical commits remain reproducible as long as the referenced objects are retained.

Storage remains efficient because:

- Unchanged objects are reused.
- Loose objects are compressed.
- Packfiles use delta compression internally.

---

## Q4. What is a Git commit ID, and why can it change after a rebase?

### Answer

A commit ID is derived from the complete commit object, including:

- Root tree ID
- Parent commit ID or IDs
- Author identity and timestamp
- Committer identity and timestamp
- Commit message
- Optional headers such as signatures

A rebase recreates commits with a different parent. Even when the final file contents are identical, the new parent and committer metadata change the commit object, producing a different ID.

This is why rebase is history rewriting and why rebasing already shared commits can disrupt collaborators.

---

## Q5. Explain SHA-1 and SHA-256 repositories in Git.

### Answer

Historically, Git object names use SHA-1. Git also supports repositories using the SHA-256 object format.

A senior engineer should avoid making object-hash assumptions in automation:

- Do not hard-code a 40-character object-ID length.
- Use Git commands such as `git rev-parse`, `git show`, and `git cat-file`.
- Treat object IDs as opaque identifiers.
- Test integrations, repository hosts, migration tools, and protocol compatibility before introducing SHA-256 repositories.

The object hash provides content identity and integrity checking. It is not a substitute for authenticated commit signing, authorization, or secure transport.

---

## Q6. What are loose objects and packfiles?

### Answer

New objects are commonly written as individual compressed files under `.git/objects`. These are called loose objects.

Git later consolidates objects into packfiles:

```text
.git/objects/pack/pack-<id>.pack
.git/objects/pack/pack-<id>.idx
```

Packfiles reduce storage and transfer size through compression and delta representation.

### Diagnostic commands

```bash
git count-objects -vH
git verify-pack -v .git/objects/pack/*.idx
git fsck --full
git gc
```

### L3 caution

Do not manually delete packfiles or loose objects to save space. Use supported maintenance operations and confirm backups before invasive repair.

---

## Q7. What is a ref in Git?

### Answer

A ref is a name that points to an object ID, normally a commit.

Examples:

```text
refs/heads/main
refs/remotes/origin/main
refs/tags/v2.4.0
```

Loose refs may be stored as files under `.git/refs`. Many refs can also be stored in `.git/packed-refs`.

Useful commands:

```bash
git show-ref
git for-each-ref
git rev-parse refs/heads/main
git update-ref refs/heads/main <new-id> <expected-old-id>
```

`git update-ref` is safer than manually editing ref files because it performs validation and supports compare-and-swap behavior using the expected old value.

---

## Q8. What is `HEAD`?

### Answer

`HEAD` identifies the current checkout position.

Normally, it is a symbolic reference:

```text
ref: refs/heads/main
```

In detached-HEAD state, `HEAD` directly contains an object ID.

Check it with:

```bash
git symbolic-ref -q HEAD
git rev-parse --abbrev-ref HEAD
git rev-parse HEAD
```

Detached HEAD is not an error. It is useful for inspection, builds, bisecting, or temporary work. The risk is that new commits may become unreachable after switching away unless a branch or tag is created.

Recovery:

```bash
git switch -c recovery/my-work <commit-id>
```

---

## Q9. Explain the commit graph.

### Answer

Git history is a directed acyclic graph:

- A normal commit has one parent.
- A merge commit normally has two or more parents.
- A root commit has no parent.
- References point to selected nodes in the graph.

Important graph queries:

```bash
git log --graph --oneline --decorate --all
git merge-base main feature/payment
git rev-list --left-right --count main...feature/payment
git show --first-parent
```

`git merge-base` is fundamental to merge, rebase, and changed-file calculations because it identifies the best common ancestor.

---

## Q10. What is the difference between porcelain and plumbing commands?

### Answer

Porcelain commands are user-facing operations such as:

```bash
git add
git commit
git switch
git merge
git rebase
```

Plumbing commands expose lower-level mechanisms:

```bash
git hash-object
git cat-file
git write-tree
git commit-tree
git update-ref
git rev-list
```

L3 engineers should understand plumbing commands for diagnostics and automation, but production scripts should generally use stable documented interfaces and strong error handling rather than manipulating `.git` internals directly.

---

# 2. Working Tree, Index, Commits, and References

## Q11. Explain the working tree, index, and `HEAD`.

### Answer

Git commonly compares three states:

| State | Meaning |
|---|---|
| `HEAD` | Snapshot in the current commit |
| Index | Proposed content for the next commit |
| Working tree | Files currently present for editing |

Useful comparisons:

```bash
git diff
git diff --cached
git diff HEAD
```

- `git diff` compares working tree to index.
- `git diff --cached` compares index to `HEAD`.
- `git diff HEAD` compares working tree to `HEAD`.

This model explains why a file can have both staged and unstaged changes.

---

## Q12. What is the Git index?

### Answer

The index, also called the staging area, is a structured binary file that records the paths and object IDs proposed for the next commit.

It is not merely a list of changed filenames. It can represent:

- A complete proposed tree
- File modes
- Intent-to-add entries
- Multiple stages during conflicts
- Skip-worktree and assume-unchanged-related flags

Inspect it with:

```bash
git ls-files --stage
git ls-files -v
git diff --cached
```

During a merge conflict, the index may contain:

- Stage 1: merge base
- Stage 2: ours
- Stage 3: theirs

```bash
git ls-files -u
```

---

## Q13. What is the difference between `git add`, `git commit -a`, and `git add -p`?

### Answer

`git add <path>` stages the current content of selected paths.

`git commit -a` automatically stages modifications and deletions of already tracked files, but does not include new untracked files.

`git add -p` interactively stages selected hunks. It is useful for creating focused commits when a working file contains multiple logical changes.

Corporate best practice is to create cohesive commits that are:

- Reviewable
- Testable
- Revertible
- Associated with a ticket or change
- Free of unrelated formatting or generated noise

---

## Q14. Explain `git reset`, `git restore`, and `git revert`.

### Answer

These commands solve different problems.

### `git restore`

Restores working-tree and/or index content from another source.

```bash
git restore file.conf
git restore --staged file.conf
git restore --source=HEAD~1 -- path/file
```

### `git reset`

Moves a branch/`HEAD` and optionally resets index and working tree.

```bash
git reset --soft HEAD~1
git reset --mixed HEAD~1
git reset --hard HEAD~1
```

| Mode | Moves ref | Resets index | Resets working tree |
|---|---:|---:|---:|
| `--soft` | Yes | No | No |
| `--mixed` | Yes | Yes | No |
| `--hard` | Yes | Yes | Yes |

### `git revert`

Creates a new commit that reverses an earlier commit:

```bash
git revert <commit>
```

For shared production branches, revert is usually safer because it preserves published history.

---

## Q15. What does `git status` actually evaluate?

### Answer

`git status` reports:

- Current branch or detached state
- Upstream tracking and ahead/behind state, when available
- Differences between `HEAD` and index
- Differences between index and working tree
- Untracked files
- Ongoing operations such as merge, rebase, cherry-pick, or revert

For automation, use stable machine-readable output:

```bash
git status --porcelain=v2 --branch
```

Do not parse human-formatted status output in production scripts.

---

## Q16. How does `.gitignore` work?

### Answer

Ignore rules affect untracked files. They do not automatically untrack files already committed.

Sources include:

- Repository `.gitignore`
- Per-repository `.git/info/exclude`
- Global excludes file configured with `core.excludesFile`
- Command-specific exclusions

Diagnostics:

```bash
git check-ignore -v path/to/file
```

To stop tracking an already tracked file while leaving the local file:

```bash
git rm --cached path/to/file
git commit -m "Stop tracking generated file"
```

Never use `.gitignore` as a security control. A secret committed before being ignored remains in history until properly removed and credentials are rotated.

---

## Q17. What is `.gitattributes`, and why is it important in enterprise repositories?

### Answer

`.gitattributes` assigns behavior to paths. Common uses include:

- Line-ending normalization
- Binary-file classification
- Custom diff drivers
- Merge drivers
- Export exclusions
- Git LFS filters
- Language-aware conflict handling

Example:

```gitattributes
* text=auto
*.sh text eol=lf
*.ps1 text eol=crlf
*.png binary
*.zip binary
/docs/private/** export-ignore
```

Line-ending policies must be designed carefully across Linux and Windows developer environments. Changing normalization rules may make many files appear modified and should be tested in a dedicated branch.

---

## Q18. What is the difference between author and committer?

### Answer

The author is the person who originally created the change. The committer is the person who created the current commit object.

They may differ after:

- Rebase
- Cherry-pick
- Applying emailed patches
- Maintainer integration
- Commit amendment

Inspect both:

```bash
git show --format=fuller <commit>
```

Corporate audit systems should not treat self-declared author fields as authenticated identity. Use platform identity, protected workflows, signed commits/tags, and audit logs.

---

# 3. Branching, Merging, and Rebasing

## Q19. Why are Git branches considered lightweight?

### Answer

A branch is primarily a movable reference to a commit. Creating a branch does not copy all project files or history.

```bash
git branch feature/payment
git switch feature/payment
```

The working tree changes when switching, but the branch itself is only a reference plus related metadata such as reflog and upstream configuration.

---

## Q20. Explain fast-forward and non-fast-forward merges.

### Answer

A fast-forward merge is possible when the target branch tip is an ancestor of the source tip. Git can move the target ref forward without creating a merge commit.

```bash
git merge --ff-only feature/x
```

A non-fast-forward merge joins histories and usually creates a merge commit:

```bash
git merge --no-ff feature/x
```

Enterprise policy choices:

- `--ff-only` prevents unintended merge commits and rejects diverged history.
- `--no-ff` preserves an explicit feature-integration boundary.
- Squash merge creates one new commit but does not record the feature branch as a parent.

There is no universally correct strategy; the organization should choose based on traceability, release management, revert behavior, and review tooling.

---

## Q21. What is a three-way merge?

### Answer

A three-way merge uses:

1. The current branch tip
2. The branch being merged
3. Their merge base

Git compares both sides against the base to determine independent changes and conflicts.

Useful diagnostics:

```bash
git merge-base HEAD origin/main
git diff $(git merge-base HEAD origin/main)..HEAD
git diff $(git merge-base HEAD origin/main)..origin/main
```

A conflict occurs when Git cannot safely combine changes automatically. A conflict is not proof that either side is incorrect; it requires a semantic decision.

---

## Q22. How do you resolve a merge conflict safely?

### Answer

Recommended process:

```bash
git status
git diff --name-only --diff-filter=U
git ls-files -u
```

Then:

1. Understand the common ancestor and both sides.
2. Resolve the file content.
3. Run relevant tests, not merely syntax checks.
4. Stage the resolved paths.
5. Complete the operation.

```bash
git add path/to/file
git merge --continue
```

Abort when the merge should not proceed:

```bash
git merge --abort
```

For complex conflicts:

```bash
git checkout --ours path
git checkout --theirs path
git mergetool
```

`ours` and `theirs` are context-sensitive, especially during rebase. Never select one side blindly.

---

## Q23. What is rebase?

### Answer

Rebase finds commits unique to a branch and reapplies equivalent changes on another base.

```bash
git switch feature/payment
git rebase origin/main
```

Benefits:

- Linear feature history
- Easier review of branch-specific commits
- Fewer merge commits

Risks:

- Recreated commits have new object IDs.
- Conflict resolution may be required for multiple commits.
- Rebasing published history can disrupt others.
- Signatures on recreated commits do not remain valid.

Safe rule: rebase private or explicitly coordinated branches; do not rewrite shared protected history without an approved procedure.

---

## Q24. Explain interactive rebase.

### Answer

Interactive rebase allows editing a sequence of commits:

```bash
git rebase -i HEAD~6
```

Common actions:

- `pick`: retain
- `reword`: change message
- `edit`: pause for amendment
- `squash`: combine and edit message
- `fixup`: combine and discard selected message
- `drop`: remove commit

Useful options:

```bash
git commit --fixup=<commit>
git rebase -i --autosquash <base>
```

Interactive rebase is excellent for cleaning a private review branch. It should not be used casually on shared release history.

---

## Q25. What is the difference between merge, rebase, squash, and cherry-pick?

### Answer

| Operation | Result |
|---|---|
| Merge | Combines histories and may create a merge commit |
| Rebase | Recreates commits on a new base |
| Squash merge | Creates one new commit representing the total branch change |
| Cherry-pick | Reapplies selected commit changes onto the current branch |

Selection criteria:

- Use merge when preserving topology matters.
- Use rebase to update and clean private feature history.
- Use squash for one-change-per-request history when intermediate commits have little long-term value.
- Use cherry-pick for controlled backports or selected changes, not as a default branch synchronization method.

---

## Q26. How do you revert a merge commit?

### Answer

A merge commit has multiple parents. Reverting it requires specifying the mainline parent:

```bash
git show --no-patch --pretty=%P <merge-id>
git revert -m 1 <merge-id>
```

`-m 1` usually means retain the first parent's line and reverse the net changes introduced from the other side.

This must be validated carefully. Reverting a merge does not erase history; it records that the changes should be negated. Re-merging the same branch later can be surprising because the original merge remains an ancestor. A fresh fix or revert-of-revert may be required.

---

## Q27. What is `git rerere`?

### Answer

`rerere` means reuse recorded resolution. Git can remember how a particular conflict was resolved and reuse that resolution when the same conflict reappears.

Enable it:

```bash
git config --global rerere.enabled true
git config --global rerere.autoupdate true
```

It is useful during:

- Long-running rebases
- Repeated integration testing
- Backport maintenance
- Recurring conflict patterns

The reused result must still be reviewed and tested.

---

## Q28. How do you compare two versions of a rebased patch series?

### Answer

Use `git range-diff`:

```bash
git range-diff origin/main...feature-v1 origin/main...feature-v2
```

It compares commit ranges as patch series and helps reviewers understand:

- Which commits correspond
- What changed between revisions
- Which commits were added or removed
- Whether feedback was addressed

This is superior to comparing only branch tips after a substantial rebase.

---

## Q29. Explain a safe force-push.

### Answer

After intentionally rewriting a private or coordinated branch:

```bash
git push --force-with-lease origin feature/payment
```

`--force-with-lease` refuses the update if the remote ref is not at the expected value, protecting against overwriting someone else's new push.

Plain `--force` bypasses this safety check.

Corporate policy should:

- Block force pushes to protected branches.
- Permit them only on controlled review branches.
- Require fetch/review before retry.
- Record exceptions through platform audit logs.

For stronger checking, explicitly lease the expected object ID where appropriate.

---

## Q30. Compare Git Flow, GitHub Flow, trunk-based development, and release-branch models.

### Answer

### Git Flow

Uses long-lived `develop`, release, and hotfix branches. It offers explicit release phases but can create heavy merge overhead and delayed integration.

### GitHub-style flow

Uses short-lived branches and pull requests into a deployable default branch. It is operationally simple but depends on strong automation and feature-control practices.

### Trunk-based development

Developers integrate frequently into one trunk, often with very short branches. It reduces divergence but requires excellent testing, feature flags, observability, and disciplined small changes.

### Release branches

A main development line is combined with supported release branches. Fixes may require backporting and clear ownership.

A senior candidate should not select a workflow by popularity. The choice must consider release cadence, compliance, support lifetime, deployment architecture, team size, and automation maturity.

---

# 4. Remote Repositories and Distributed Workflows

## Q31. What is the difference between `git fetch` and `git pull`?

### Answer

`git fetch` downloads objects and updates remote-tracking refs without integrating them into the current branch.

```bash
git fetch --prune origin
```

`git pull` performs a fetch and then integrates, normally by merge or rebase depending on configuration and arguments.

```bash
git pull --ff-only
git pull --rebase
```

L3 preference: fetch first during investigation so the engineer can inspect the graph before choosing merge, rebase, reset, or no action.

---

## Q32. What are remote-tracking branches?

### Answer

A remote-tracking ref such as `refs/remotes/origin/main` is a local record of the remote branch state observed during the last fetch or push.

It is not a live pointer to the remote server.

```bash
git fetch origin
git log --oneline main..origin/main
git log --oneline origin/main..main
```

A local branch can be configured to track an upstream:

```bash
git branch --set-upstream-to=origin/main main
```

---

## Q33. Explain refspecs.

### Answer

A refspec maps source refs to destination refs.

Default fetch example:

```text
+refs/heads/*:refs/remotes/origin/*
```

General form:

```text
[+]<source>:<destination>
```

The leading `+` allows non-fast-forward updates for the mapped remote-tracking refs.

Examples:

```bash
git fetch origin refs/heads/release:refs/remotes/origin/release
git push origin local-branch:refs/heads/remote-branch
git push origin :refs/heads/obsolete-branch
```

Refspec mistakes can publish to or delete the wrong ref. Production automation should explicitly validate source and destination.

---

## Q34. What happens during `git clone`?

### Answer

At a high level:

1. A new repository is initialized.
2. Git contacts the remote.
3. References and repository capabilities are discovered.
4. Required objects are negotiated and transferred.
5. Remote-tracking refs are created.
6. The default branch is checked out unless checkout is disabled.

Important variants:

```bash
git clone --bare URL repo.git
git clone --mirror URL repo-mirror.git
git clone --depth=1 URL
git clone --filter=blob:none URL
git clone --no-checkout URL
```

A clone does not copy server hooks, access-control policy, review metadata, issue data, CI secrets, or platform configuration.

---

## Q35. What is the difference between bare and non-bare repositories?

### Answer

A non-bare repository has a working tree and typically stores repository metadata in `.git`.

A bare repository has no normal working tree; its top-level directory contains the repository metadata. Server-side shared repositories are usually bare.

```bash
git init --bare project.git
```

Pushing into the currently checked-out branch of a non-bare repository is restricted by default because it could make the index and working tree inconsistent.

---

## Q36. What is the difference between `--bare` and `--mirror` clone?

### Answer

`git clone --bare` creates a bare repository and maps branch heads directly.

`git clone --mirror` is broader:

- It creates a bare repository.
- It copies all refs available to the client.
- It configures ref mirroring so updates and deletions can be reproduced.

Mirror push:

```bash
git push --mirror destination
```

This is powerful and dangerous because it can delete destination refs that do not exist in the source. Use an isolated validation destination and protect platform-managed refs.

---

## Q37. Explain shallow clone and its limitations.

### Answer

A shallow clone intentionally limits history depth:

```bash
git clone --depth=1 URL
git fetch --deepen=100
git fetch --unshallow
```

Benefits:

- Faster initial clone
- Lower transfer and storage use

Limitations may affect:

- `merge-base`
- History analysis
- `blame`
- release/version calculation
- bisect
- backporting
- tools expecting complete ancestry

Shallow clone is useful for disposable CI jobs when the pipeline's history requirements are known.

---

## Q38. What is a partial clone?

### Answer

A partial clone transfers a repository while omitting selected classes of objects initially, commonly file blobs:

```bash
git clone --filter=blob:none URL
```

Missing objects can be fetched on demand from the promisor remote.

Partial clone is useful for very large repositories, but enterprise adoption requires testing:

- Server support
- Build tools
- Offline operations
- CI caching
- Security controls
- Backup and mirror behavior
- Submodule interactions

Partial clone and sparse checkout solve different problems: partial clone reduces object transfer, while sparse checkout limits working-tree population.

---

## Q39. What is sparse checkout?

### Answer

Sparse checkout limits which paths are materialized in the working tree.

```bash
git sparse-checkout init --cone
git sparse-checkout set services/payment libs/common
```

It is useful in monorepos when developers need only a subset of directories.

It does not automatically remove unrelated history or objects from a normal clone. Combine it with partial clone when reducing both checkout size and object transfer is required.

---

## Q40. How do you troubleshoot authentication failures over SSH and HTTPS?

### Answer

### SSH checks

```bash
ssh -vvv git@host
ssh-add -l
git remote -v
GIT_SSH_COMMAND="ssh -vvv" git ls-remote origin
```

Validate:

- Correct remote hostname and user
- Key loaded in agent
- File permissions
- Host-key verification
- Server-side key association
- Corporate proxy or firewall
- SSH configuration precedence

### HTTPS checks

```bash
git remote -v
git config --show-origin --get-all credential.helper
GIT_TRACE=1 GIT_CURL_VERBOSE=1 git ls-remote origin
```

Validate:

- Token scope and expiration
- SSO authorization
- Proxy and certificate trust
- Credential helper
- Remote URL
- Host access policy

Trace output may contain sensitive headers or URLs. Sanitize it before sharing.

---

## Q41. How do you diagnose a slow fetch or clone?

### Answer

Use a controlled approach:

```bash
GIT_TRACE=1 \
GIT_TRACE_PACKET=1 \
GIT_TRACE_PERFORMANCE=1 \
git fetch origin
```

For HTTP:

```bash
GIT_CURL_VERBOSE=1 git fetch origin
```

Investigate:

- DNS, latency, packet loss, proxy, TLS inspection
- Server CPU, memory, disk, and pack generation
- Repository size and object count
- Large or incompressible blobs
- Excessive refs
- Negotiation behavior
- Client and server Git versions
- CI concurrency
- Shallow or partial clone suitability
- Alternates, caches, and repository maintenance

Do not leave verbose tracing permanently enabled in shared logs.

---

# 5. Undo, Recovery, and Incident Handling

## Q42. What is the reflog?

### Answer

A reflog records local updates to references. The `HEAD` reflog also records branch switches and detached-HEAD movement.

```bash
git reflog
git reflog show main
git log -g --oneline
```

It is extremely useful for recovering from:

- Accidental reset
- Failed rebase
- Deleted local branch
- Amended commit
- Detached work

Reflogs are local and expire according to policy. They are not a substitute for remote backups.

---

## Q43. How do you recover after `git reset --hard`?

### Answer

Immediately avoid cleanup operations and inspect the reflog:

```bash
git reflog --date=iso
```

Identify the old tip, then create a recovery branch:

```bash
git switch -c recovery/pre-reset <old-id>
```

Validate:

```bash
git log --graph --oneline --decorate recovery/pre-reset
git diff main...recovery/pre-reset
```

`reset --hard` destroys uncommitted working-tree changes unless those contents exist elsewhere, such as editor history, stash, filesystem snapshot, or objects previously written by Git.

---

## Q44. How do you recover a deleted branch?

### Answer

Deleting a branch removes the ref; it does not immediately delete the commit objects.

```bash
git reflog --all
git fsck --full --no-reflogs --unreachable
git branch recovered/feature <commit-id>
```

Validate commit ancestry and contents before republishing.

If another developer or CI system still has the branch, fetching or requesting its object ID may be safer than forensic recovery.

---

## Q45. How do you recover from a bad rebase?

### Answer

Options:

```bash
git rebase --abort
```

If the rebase already completed:

```bash
git reflog
git switch -c backup/rebased-state HEAD
git reset --hard <pre-rebase-id>
```

Git may also preserve the original position in `ORIG_HEAD`, but reflog inspection is more general:

```bash
git show ORIG_HEAD
```

Before a risky rewrite:

```bash
git branch backup/feature-before-rebase
```

---

## Q46. What is `ORIG_HEAD`?

### Answer

`ORIG_HEAD` is a pseudoref used by some operations to record the previous tip before a potentially disruptive action such as merge or reset.

It can help with recovery:

```bash
git show ORIG_HEAD
git diff ORIG_HEAD..HEAD
```

Do not rely on it as a long-term backup because later commands can overwrite it. Create named backup refs for critical work.

---

## Q47. How do you find which commit introduced a regression?

### Answer

Use `git bisect` to perform binary search:

```bash
git bisect start
git bisect bad
git bisect good <known-good-tag>
```

Test each selected commit:

```bash
git bisect good
git bisect bad
```

Finish:

```bash
git bisect reset
```

Automate with a deterministic test:

```bash
git bisect run ./test-regression.sh
```

The script should return:

- `0` for good
- `1–127`, except `125`, for bad
- `125` to skip an untestable commit

Build failures, schema transitions, and environmental dependencies must be handled explicitly.

---

## Q48. What is the difference between `git fsck` and `git gc`?

### Answer

`git fsck` checks object connectivity and validity:

```bash
git fsck --full
```

`git gc` performs repository housekeeping such as packing objects and pruning according to retention policy:

```bash
git gc
```

Do not run aggressive garbage collection during a recovery incident. Unreachable objects that could be recovered may eventually be pruned.

---

## Q49. How do you handle a corrupted repository?

### Answer

Recommended sequence:

1. Stop writes and preserve a filesystem copy.
2. Capture error output and environmental information.
3. Run non-destructive inspection:

```bash
git fsck --full
git count-objects -vH
```

4. Compare with known-good clones, replicas, or backups.
5. Restore missing objects from a trusted source when possible.
6. Re-clone when local-only work is not at risk.
7. Validate refs, tags, signatures, and build results.
8. Document root cause: disk fault, interrupted copy, manual deletion, storage corruption, malware, or faulty backup.

Do not copy random `.git/objects` files into production without understanding object trust and repository ancestry.

---

## Q50. How do you remove a secret committed to Git?

### Answer

The incident has two separate requirements:

1. **Credential response:** revoke or rotate the secret immediately.
2. **History remediation:** remove the sensitive data from relevant history and storage.

Typical process:

- Identify affected repositories, refs, forks, caches, CI artifacts, release archives, and logs.
- Pause automated deployments if necessary.
- Rewrite history using an approved tool and reviewed mapping.
- Force-update authorized refs.
- Coordinate all clones and forks.
- Expire caches and old artifacts where possible.
- Add preventive scanning and secret-management controls.

Deleting the latest file or adding it to `.gitignore` does not remove earlier copies.

A rewritten repository cannot guarantee deletion from already copied clones. Treat the secret as compromised regardless of history cleanup.

---

## Q51. When should you use revert rather than reset?

### Answer

Use revert when the commit is already shared, especially on a protected production branch.

```bash
git revert <bad-commit>
```

Use reset for local or explicitly coordinated ref movement.

A practical corporate rule:

- Published immutable branch: revert
- Private feature branch: reset/rebase may be acceptable
- Emergency release: revert first to stabilize; investigate and redesign later
- Legal or audit hold: follow retention and evidence-preservation requirements before rewriting anything

---

# 6. Repository Security and Governance

## Q52. How do you secure an enterprise Git workflow?

### Answer

Use layered controls:

- Central identity provider and MFA
- Least-privilege repository permissions
- Protected default and release branches
- Required pull-request reviews
- CODEOWNERS or equivalent ownership rules
- Required CI checks
- Signed release tags or commits where required
- Secret scanning and dependency scanning
- Restricted force-push and deletion
- Short-lived credentials
- Audit-log retention
- Backup and restore testing
- Separation of duties for sensitive repositories
- Controlled deployment credentials
- Repository archival and deletion policy

Git itself does not provide the complete corporate governance layer. Hosting-platform and identity controls are essential.

---

## Q53. What is commit signing, and what does it prove?

### Answer

A cryptographic signature can attest that a holder of a signing key signed a specific commit or tag object.

It can help establish:

- Object integrity after signing
- Association with a trusted key under an organization's trust model

It does not automatically prove:

- The code is safe
- The author identity field is truthful
- The signer personally wrote every line
- The build artifact came from that source
- The key was uncompromised

Verification:

```bash
git verify-commit <commit>
git verify-tag <tag>
git log --show-signature
```

Enterprise implementation requires key lifecycle, revocation, identity binding, policy enforcement, and build provenance.

---

## Q54. Lightweight tag versus annotated tag?

### Answer

A lightweight tag is a ref directly pointing to an object.

```bash
git tag v2.1.0
```

An annotated tag creates a tag object with tagger metadata and a message:

```bash
git tag -a v2.1.0 -m "Production release 2.1.0"
```

A signed tag adds a cryptographic signature:

```bash
git tag -s v2.1.0 -m "Production release 2.1.0"
git tag -v v2.1.0
```

For enterprise releases, annotated or signed tags are normally more suitable.

---

## Q55. How should Git credentials be managed?

### Answer

Avoid credentials in:

- Repository files
- Remote URLs
- shell history
- build logs
- global plain-text configuration
- container images

Preferred controls:

- SSH keys protected by passphrases or hardware-backed keys
- Short-lived tokens
- Workload identity for CI
- Approved credential helpers
- Secret vaults
- Least scope
- Expiration and rotation
- Immediate revocation on role change or incident

Inspect configuration origin:

```bash
git config --show-origin --get-all credential.helper
git config --show-origin --list
```

---

## Q56. What security risks exist in Git hooks?

### Answer

Hooks can execute arbitrary commands. Risks include:

- Exfiltration of source code or credentials
- Modification of commits or build output
- Destructive filesystem actions
- Inconsistent enforcement across developer machines
- Bypass with `--no-verify` for some client-side hooks

Client-side hooks are not copied by a normal clone and should not be the only enforcement mechanism.

Use:

- Server-side validation
- Protected branches
- Mandatory CI
- Reviewed hook distribution
- Controlled hook path
- Least-privileged runtime
- Logging and change control

---

## Q57. How do you enforce commit-message and branch policies?

### Answer

Possible layers:

- Client tooling for developer feedback
- Commit-msg hook for local validation
- CI checks
- Server-side receive hooks
- Hosting-platform rules
- Merge queues
- Protected-branch policy

Examples of validated metadata:

- Ticket identifier
- Conventional commit type
- Signed-off-by line
- Change-risk classification
- Release-note annotation

Policy should permit controlled exceptions and avoid blocking emergency remediation due to fragile tooling.

---

## Q58. How do branch protection and CODEOWNERS improve governance?

### Answer

Branch protection can require:

- Pull requests
- Minimum approvals
- Review from designated owners
- Passing checks
- Up-to-date branch
- Signed commits
- Linear history
- Restricted push or deletion

CODEOWNERS-like mechanisms route changes to responsible teams. They are useful for sensitive areas such as:

- IAM policy
- CI configuration
- Infrastructure code
- Database migrations
- Security controls
- Release workflows

Review ownership is not a substitute for automated testing or separation of deployment permissions.

---

## Q59. How do you protect CI/CD from untrusted pull requests?

### Answer

Key controls:

- Do not expose production secrets to untrusted code.
- Separate pull-request validation from privileged deployment.
- Use read-only tokens where possible.
- Require approval before running privileged jobs.
- Pin third-party actions or dependencies according to policy.
- Isolate runners.
- Clean workspaces and credentials.
- Validate artifacts before promotion.
- Use immutable artifacts between stages.
- Restrict who can modify pipeline definitions.
- Review changes to build scripts with elevated scrutiny.

The code under test can attempt to read every secret and resource available to its runner.

---

# 7. Large Repository Performance and Maintenance

## Q60. How do you analyze repository size?

### Answer

```bash
git count-objects -vH
git rev-list --objects --all > all-objects.txt
git verify-pack -v .git/objects/pack/*.idx
```

Investigate:

- Pack size
- Number of objects
- Largest blobs
- Ref count
- Commit-graph availability
- Generated files
- Binary history
- CI-created refs
- Unreachable-object retention
- LFS usage
- Clone and checkout timings

Repository size is not the same as working-tree size. A deleted 2-GB file may remain in history.

---

## Q61. What is `git maintenance`?

### Answer

`git maintenance` coordinates repository optimization tasks.

```bash
git maintenance run
git maintenance start
git maintenance stop
```

Potential tasks can include operations related to:

- Commit graphs
- Prefetching
- Loose-object cleanup
- Incremental repacking
- Reference packing

Production use should be tested for repository size, filesystem performance, CI concurrency, and server-hosting architecture. Avoid launching heavy maintenance simultaneously across many repositories.

---

## Q62. What does `git gc` do, and when can it be risky?

### Answer

Garbage collection consolidates repository storage and may eventually prune unreachable objects according to configured expiration.

Risks:

- High CPU, memory, and I/O load
- Lock contention
- Loss of recoverable unreachable objects after pruning
- Excessive downtime if run aggressively
- Interference with high-concurrency server workloads

Before manual aggressive maintenance:

- Confirm backups
- Check active operations
- Capture size and health baselines
- Use a maintenance window
- Avoid immediate pruning during incident recovery
- Validate the repository afterward

---

## Q63. How do commit graphs and multi-pack indexes help?

### Answer

A commit graph accelerates ancestry and graph traversal by storing precomputed commit metadata.

A multi-pack index allows objects across multiple packfiles to be indexed together, which can reduce the need for frequent full repacking.

These mechanisms are valuable for large repositories with heavy `log`, merge-base, fetch, and reachability operations.

Use supported maintenance commands rather than manually altering internal files.

---

## Q64. What is Git LFS, and when should it be used?

### Answer

Git LFS stores small pointer files in Git while large content is managed in an LFS object store.

Appropriate uses:

- Large media
- Design assets
- ML models
- Other large binaries that must be versioned

Considerations:

- LFS server availability
- Quotas and bandwidth
- Authentication
- Backup and restore
- Migration of existing history
- CI checkout
- Locking for non-mergeable assets
- Disaster recovery

A Git repository backup without corresponding LFS objects is incomplete.

---

## Q65. How do you identify large historical blobs?

### Answer

One approach:

```bash
git rev-list --objects --all |
git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' |
awk '$1=="blob" {print $3, $2, $4}' |
sort -n |
tail -20
```

Review paths and object IDs carefully. A large blob may appear under multiple filenames or no currently reachable path.

Before rewriting history:

- Determine business and legal requirements.
- Coordinate forks and clones.
- Back up refs.
- Test rewritten history.
- Rotate exposed secrets separately.
- Validate release tags and build reproducibility.

---

## Q66. How do you improve monorepo performance?

### Answer

Possible controls:

- Partial clone
- Sparse checkout
- Commit graph
- Multi-pack index
- Scheduled maintenance
- Build-system caching
- Path-aware CI
- Reduced generated content
- LFS for suitable large assets
- Controlled ref lifecycle
- Efficient hooks
- Local object caches where supported
- Worktrees for parallel branches

Do not split a repository solely because it is large. Consider dependency boundaries, atomic change requirements, release ownership, authorization, build architecture, and developer experience.

---

# 8. Submodules, Subtrees, Worktrees, and Monorepos

## Q67. What is a Git submodule?

### Answer

A submodule records a specific commit of another repository using a gitlink entry plus configuration in `.gitmodules`.

Common commands:

```bash
git submodule add <url> path/to/component
git submodule update --init --recursive
git submodule status
```

The parent repository records the submodule commit, not the submodule files as normal blobs.

Risks:

- Detached HEAD in submodule checkout
- Missing access rights
- Forgotten recursive update
- Mismatched URL
- CI complexity
- Need to publish submodule commit before updating parent

---

## Q68. How do you troubleshoot a broken submodule?

### Answer

Check:

```bash
git submodule status --recursive
git config --file .gitmodules --get-regexp 'submodule\..*\.url'
git ls-tree HEAD path/to/submodule
```

Then:

```bash
git submodule sync --recursive
git submodule update --init --recursive
```

Validate that:

- The recorded submodule commit exists on an accessible remote.
- Credentials allow access.
- The URL is correct.
- Nested submodules are initialized.
- The submodule change was pushed before the parent gitlink update.

---

## Q69. Submodule versus subtree?

### Answer

### Submodule

- Preserves separate repository identity
- Parent points to a child commit
- Requires explicit initialization and access
- Suits separately governed components

### Subtree-style integration

- Copies component content into the parent repository
- Easier for consumers to clone
- Can increase repository size and synchronization complexity
- Requires disciplined import/export process

The decision should be driven by ownership, permissions, release coupling, contribution direction, and operational tooling.

---

## Q70. What is `git worktree`?

### Answer

Worktrees allow multiple working directories attached to one repository object database.

```bash
git worktree add ../project-hotfix hotfix/incident-123
git worktree list
git worktree remove ../project-hotfix
git worktree prune
```

Benefits:

- Work on a hotfix without stashing current work
- Compare branches
- Run parallel builds
- Maintain release branches

Each worktree has separate `HEAD` and index, while objects are shared.

Do not copy or move linked worktree metadata manually without using supported repair/move procedures.

---

## Q71. How do you decide between a monorepo and multiple repositories?

### Answer

Evaluate:

- Need for atomic cross-component changes
- Shared build and test system
- Ownership and access boundaries
- Release independence
- Repository performance
- Dependency visibility
- Regulatory segregation
- Developer tooling
- Disaster-recovery scope
- Change-review volume

A monorepo can simplify atomic changes and dependency visibility. Multiple repositories can improve independent ownership and authorization. Both require mature automation.

---

# 9. Git Hooks, CI/CD, and Automation

## Q72. Name important client-side and server-side hooks.

### Answer

Client-side examples:

- `pre-commit`
- `prepare-commit-msg`
- `commit-msg`
- `post-commit`
- `pre-rebase`
- `post-checkout`
- `pre-push`

Server-side receive examples:

- `pre-receive`
- `update`
- `post-receive`
- `proc-receive`

A senior answer must state that client hooks are advisory unless controlled through managed tooling; server-side rules and protected branches provide authoritative enforcement.

---

## Q73. How would you design a reliable pre-receive policy?

### Answer

The hook receives proposed ref updates and should:

1. Read all updates.
2. Validate ref names and protected namespaces.
3. Determine new commits without repeatedly scanning the entire repository.
4. Check policy such as signatures, file restrictions, secret detection, or ticket metadata.
5. Produce actionable errors.
6. Fail safely.
7. Complete within a bounded time.
8. Log decisions without leaking secrets.
9. Handle branch creation and deletion correctly.
10. Be version-controlled, tested, reviewed, and deployable with rollback.

Expensive analysis is often better in CI plus protected-branch checks than inside a synchronous receive transaction.

---

## Q74. How do you make Git automation safe?

### Answer

Use:

```bash
set -euo pipefail
```

Also:

- Quote all paths and variables.
- Use `--` before untrusted path arguments.
- Prefer NUL-delimited output.
- Use machine-readable Git formats.
- Validate expected repository and branch.
- Avoid parsing localized human output.
- Use compare-and-swap ref updates.
- Do not use `eval`.
- Handle filenames containing spaces, tabs, newlines, and leading hyphens.
- Use temporary directories and cleanup traps.
- Never log credentials.

Example:

```bash
git diff --name-only -z "$base" "$head" |
while IFS= read -r -d '' path; do
    printf 'Changed: %q\n' "$path"
done
```

---

## Q75. How do you calculate changed files correctly in pull-request CI?

### Answer

Do not assume `HEAD^` is the correct base. Determine the merge base against the target branch:

```bash
git fetch origin main
base=$(git merge-base HEAD origin/main)
git diff --name-only -z "$base" HEAD
```

In shallow CI, fetch enough target history for `merge-base` to succeed.

Hosted pull-request systems may provide a synthetic merge commit or explicit base/head SHAs. Use platform-provided immutable identifiers where available.

---

## Q76. How do you create reproducible release metadata from Git?

### Answer

Possible inputs:

```bash
git rev-parse HEAD
git describe --tags --always --dirty
git status --porcelain
git show -s --format='%H%n%aI%n%cI%n%D' HEAD
```

A robust release process records:

- Exact commit ID
- Source repository identity
- Tag and tag verification result
- Dirty-tree status
- Build system and dependency lock state
- Build timestamp policy
- Builder identity
- Artifact digest
- CI run ID
- Provenance/attestation where applicable

A tag name alone is insufficient because tags can be moved unless protected and verified.

---

## Q77. How should CI clone repositories efficiently and safely?

### Answer

Recommendations:

- Use immutable commit IDs for checkout.
- Fetch only required refs.
- Use shallow clone only when history-dependent jobs do not require more history.
- Use partial clone or caches only after compatibility testing.
- Isolate credentials.
- Clean reused workspaces.
- Verify submodules and LFS.
- Do not run privileged deployment logic from untrusted changes.
- Record the resolved commit and remote URL.
- Protect cache integrity.
- Keep fetch logic explicit and observable.

---

# 10. Migration, Backup, and Disaster Recovery

## Q78. How do you migrate a Git repository between servers?

### Answer

A generic Git-data migration:

```bash
git clone --mirror <source-url> repo.git
cd repo.git
git remote set-url --push origin <destination-url>
git push --mirror
```

But a corporate migration also covers:

- User and team permissions
- Branch protection
- Hooks
- Deploy keys
- CI variables
- LFS objects
- Wikis
- Issues and pull requests
- Webhooks
- Release assets
- Audit requirements
- Default branch
- Signed-tag verification
- DNS and redirect plan
- Freeze/cutover window
- Rollback

Validate ref counts and selected object IDs on both sides.

---

## Q79. What is `git bundle`?

### Answer

A bundle packages Git refs and objects into a single file for offline transfer or backup use.

```bash
git bundle create project.bundle --all
git bundle verify project.bundle
git clone project.bundle project
```

Incremental bundles are possible but require correct prerequisite management.

A bundle does not include:

- Working-tree uncommitted changes
- Repository host configuration
- Hooks
- LFS objects unless handled separately
- Issues, pull requests, or CI configuration stored outside Git

---

## Q80. What should an enterprise Git backup include?

### Answer

At minimum, depending on architecture:

- Repository objects and all required refs
- LFS objects
- Server configuration
- Access-control data
- Hooks
- Platform database
- Pull requests/issues/wiki if in scope
- CI configuration and metadata
- Encryption keys and secrets through approved backup systems
- Audit logs where required
- Restore documentation

Backups must be:

- Encrypted
- Access-controlled
- Versioned or immutable according to policy
- Protected from deletion and ransomware
- Tested through restoration
- Monitored for completeness

---

## Q81. How do you validate a Git disaster-recovery restore?

### Answer

Validation includes:

```bash
git fsck --full
git show-ref
git count-objects -vH
```

Also verify:

- Default and release refs
- Selected known commit IDs and signed tags
- LFS availability
- Submodule accessibility
- Clone/fetch/push
- Branch protection
- Identity integration
- Hooks and webhooks
- CI execution
- Deployment permissions
- Audit logging
- Performance under expected load

A restore is not successful merely because repository files exist.

---

# 11. Corporate L3 Scenario-Based Questions

## Q82. A developer force-pushed and removed several commits from a shared branch. What do you do?

### Answer

1. Freeze further pushes to the branch.
2. Capture the current remote tip and audit data.
3. Identify the previous tip from:
   - Hosting-platform audit logs
   - Another clone's remote-tracking reflog
   - CI checkout metadata
   - Local reflogs
   - Backup or replica
4. Create protected recovery refs before moving anything.
5. Compare removed and current commits.
6. Confirm whether any removed commits contained security-sensitive changes.
7. Restore with a controlled push:

```bash
git push --force-with-lease=<branch>:<current-remote-id> \
  origin <recovered-id>:refs/heads/<branch>
```

8. Notify affected teams and require fresh synchronization.
9. Review branch protection and force-push permissions.
10. Document root cause and corrective action.

Do not immediately overwrite the branch based on one engineer's reflog without verifying that it is the authoritative lost tip.

---

## Q83. A production deployment points to the wrong commit even though the branch looks correct. How do you investigate?

### Answer

Collect immutable identifiers:

```bash
git rev-parse HEAD
git show -s --format=fuller HEAD
git remote -v
git status --porcelain=v2 --branch
```

Then correlate:

- CI checkout SHA
- Artifact digest
- Container image labels
- Release tag object ID
- Deployment manifest
- Environment promotion record
- Cached workspace state
- Submodule SHAs
- LFS content
- Build provenance

Likely causes:

- Deployment used branch name at different time.
- Stale artifact was promoted.
- Tag moved.
- CI built a merge ref rather than head commit.
- Repository workspace was dirty.
- Submodule or dependency was not pinned.
- Deployment metadata recorded source incorrectly.

Fix the pipeline to promote immutable artifacts, not rebuild mutable branches per environment.

---

## Q84. Two teams repeatedly overwrite each other's generated file. What is the L3 solution?

### Answer

Do not solve only through repeated conflict resolution. Determine ownership and source of truth.

Options:

- Stop tracking generated output and generate it in CI.
- Track the source definition, not the generated artifact.
- Assign one owner and provide a deterministic generator.
- Validate generator version and reproducibility.
- Separate outputs by environment.
- Use a semantic merge driver only when correct and maintainable.
- Add CI to detect manual modifications.

The durable fix is workflow and architecture, not a Git alias.

---

## Q85. A repository suddenly grows by 8 GB. How do you investigate?

### Answer

1. Stop unnecessary clones and fetches.
2. Capture before/after metrics if available.
3. Inspect object and pack size:

```bash
git count-objects -vH
```

4. Identify large blobs and introducing commits.
5. Determine whether the content is:
   - Required binary data
   - Build output
   - Log/archive/database dump
   - Secret material
   - Duplicate vendor package
6. If sensitive, rotate credentials and start incident response.
7. If non-sensitive but inappropriate:
   - Remove from current tree.
   - Decide whether history rewrite is justified.
   - Consider LFS or artifact storage.
8. Coordinate forks, CI caches, mirrors, and backups.
9. Add size-policy checks.

A normal delete commit does not shrink existing history.

---

## Q86. A merge conflict was resolved incorrectly and already released. What do you do?

### Answer

1. Stabilize production according to severity:
   - Roll back immutable artifact, or
   - Revert the faulty integration/fix.
2. Identify the exact release commit.
3. Compare merge parents and merge base.
4. Reproduce the semantic conflict.
5. Add a test that detects the defect.
6. Create a focused corrective commit.
7. Validate downstream branches and backports.
8. Review why code review and testing did not catch the issue.

Do not rewrite the released branch merely to make history look clean.

---

## Q87. A developer says “Git lost my work.” How do you triage?

### Answer

Ask for exact commands and current repository state, then preserve evidence:

```bash
git status
git branch --show-current
git reflog --date=iso
git stash list
git worktree list
git fsck --full --no-reflogs --unreachable
```

Check:

- Wrong branch or worktree
- Detached HEAD
- Stash
- Untracked files
- Reset/rebase/amend
- Editor backup/history
- Case-only filename changes
- Sparse-checkout patterns
- Clean command
- Submodule directory
- Filesystem or IDE actions

Create recovery branches for candidate commits before further cleanup.

---

## Q88. A pull request reports hundreds of unrelated commits. Why?

### Answer

Common causes:

- Wrong base branch
- Feature branch created from obsolete release branch
- Target branch was rebased or force-pushed
- Merge commits imported another branch
- Branch was based on a fork with different history
- Unrelated histories were combined
- Comparison uses two-dot when three-dot semantics were intended, or vice versa

Diagnostics:

```bash
git merge-base feature target
git log --graph --oneline --decorate --all
git log --left-right --cherry-pick --oneline target...feature
git range-diff target...old-feature target...new-feature
```

Resolution may be rebase, selective cherry-pick onto a clean branch, or correcting the PR base.

---

## Q89. A cherry-pick says “empty commit.” What does it mean?

### Answer

Possible reasons:

- The change already exists in the target history.
- Conflict resolution removed the net change.
- An equivalent patch was applied under a different commit ID.
- The original commit contained changes no longer applicable.

Investigate:

```bash
git show <commit>
git log --cherry-mark --left-right target...source
git diff <commit>^ <commit>
```

Then choose:

```bash
git cherry-pick --skip
```

or create an explicit empty commit only when audit requirements justify it:

```bash
git commit --allow-empty
```

Do not mechanically force an empty backport without understanding why it is empty.

---

## Q90. A branch is “ahead 5, behind 3.” Explain and resolve it.

### Answer

The local branch and its upstream have diverged:

- Five commits are reachable only from local.
- Three are reachable only from upstream.

Inspect:

```bash
git fetch origin
git rev-list --left-right --count HEAD...@{upstream}
git log --graph --oneline --decorate --left-right HEAD...@{upstream}
```

Resolution depends on policy:

```bash
git rebase @{upstream}
```

or:

```bash
git merge @{upstream}
```

or, when local commits should be discarded after confirmation:

```bash
git branch backup/local-before-reset
git reset --hard @{upstream}
```

Never choose based only on the ahead/behind count; inspect commit ownership and published status.

---

## Q91. A release tag was moved. Why is this serious?

### Answer

A moved tag can cause different users or systems to resolve the same release name to different objects.

Actions:

1. Capture old and new tag IDs.
2. Freeze further modification.
3. Check whether the tag is annotated/signed.
4. Identify builds and deployments produced from each object.
5. Restore or create a new immutable tag according to policy.
6. Invalidate affected artifacts if required.
7. Protect release tags and restrict deletion/update.
8. publish the exact object IDs and artifact digests in the incident record.

Release systems should use verified immutable object IDs, not trust a mutable tag name alone.

---

## Q92. A CI job works locally but fails in a shallow clone. Why?

### Answer

Likely causes:

- Required parent commit is outside shallow boundary.
- `merge-base` fails.
- Version calculation cannot find tags.
- Submodule history is incomplete.
- Diff logic assumes complete ancestry.
- Bisect or blame requires older commits.

Fix options:

```bash
git fetch --deepen=200
git fetch --unshallow
git fetch --tags
git fetch origin <base-sha>
```

Design each job's fetch depth according to its actual history needs rather than using one shallow setting for every pipeline.

---

## Q93. A repository has thousands of stale remote-tracking branches. What do you do?

### Answer

First distinguish local remote-tracking refs from actual server branches.

Preview:

```bash
git remote show origin
git fetch --prune --dry-run origin
```

Prune local stale refs:

```bash
git fetch --prune origin
git remote prune origin
```

For server-side branches, establish lifecycle policy:

- Delete merged short-lived branches
- Exempt release and audit-retained branches
- Require owner/age metadata
- Notify before deletion
- Protect active deployments
- Preserve tags/releases
- Review platform pull-request references separately

---

## Q94. Git reports “refusing to merge unrelated histories.” What does that mean?

### Answer

The two histories have no common ancestor. Causes include:

- Independent repository initialization
- Incorrect remote
- Migration error
- Replaced `.git` directory
- Import of different project
- History rewrite creating a new root

Do not immediately use `--allow-unrelated-histories`.

First verify:

```bash
git remote -v
git show --summary --oneline --decorate --all
git merge-base HEAD origin/main
```

If combining independent histories is intentional, perform it in a reviewed migration branch and document the new repository lineage.

---

## Q95. A file is modified locally but `git diff` shows nothing. What do you check?

### Answer

Check:

```bash
git status --porcelain=v2
git diff --cached
git ls-files -v -- path
git check-attr -a -- path
git config --show-origin --get core.autocrlf
```

Possible causes:

- Change is staged, so it appears in `git diff --cached`.
- Path is untracked or ignored.
- Line-ending conversion.
- Clean/smudge filter.
- Assume-unchanged or skip-worktree behavior.
- File mode configuration.
- Sparse checkout.
- IDE changed a different file or symlink target.

Do not use assume-unchanged as a general solution for locally customized tracked configuration.

---

## Q96. How do you safely backport a fix to multiple release branches?

### Answer

1. Identify the minimal fixing commit and dependencies.
2. Ensure the fix is merged in the primary development line.
3. Create a branch from each supported release.
4. Cherry-pick with origin reference:

```bash
git cherry-pick -x <fix-commit>
```

5. Resolve conflicts semantically.
6. Run release-specific tests.
7. Review API/schema/config compatibility.
8. Create separate pull requests.
9. Record which releases contain the fix.
10. Avoid merging entire newer development history into an old release.

`-x` records the source commit ID in the backport message and improves traceability.

---

## Q97. How do you manage emergency hotfixes without creating branch chaos?

### Answer

A controlled hotfix process:

1. Branch from the exact production release commit.
2. Keep the change minimal.
3. Associate it with an incident/change record.
4. Require expedited but independent review.
5. Run focused and regression tests.
6. Build an immutable artifact.
7. Tag and deploy under controlled approval.
8. Merge or cherry-pick the fix into all relevant forward branches.
9. Verify no branch missed the fix.
10. Conduct post-incident cleanup and root-cause analysis.

The hotfix should not become a permanent alternative development line.

---

## Q98. How do you handle binary merge conflicts?

### Answer

Git generally cannot semantically merge arbitrary binary formats.

Options:

- Select one version after owner review.
- Recreate the binary from mergeable source.
- Use a domain-specific merge tool.
- Use LFS locking where supported.
- Store build artifacts outside Git.
- Define ownership and serialization for non-mergeable assets.

Validate the result using the application that understands the format, not merely file size or checksum.

---

## Q99. What would you check before deleting a remote branch?

### Answer

Check:

```bash
git fetch --prune origin
git log --oneline origin/main..origin/feature
git branch -r --merged origin/main
git worktree list
```

Also verify:

- Open pull request
- Deployment or environment linked to branch
- Unmerged commits
- Release-support requirement
- Legal/audit hold
- Other teams consuming the ref
- CI or automation dependencies
- Protection rules

Delete:

```bash
git push origin --delete feature
```

Deletion policy should be automated carefully and support recovery windows.

---

## Q100. What distinguishes an L3 Git engineer from a command-only user?

### Answer

An L3 engineer can:

- Explain the object graph, refs, index, and reflog.
- Select merge/rebase/revert based on shared-history risk.
- Recover lost refs without making the incident worse.
- Design branch and release governance.
- Secure credentials, hooks, CI, and protected branches.
- Diagnose transport and repository performance.
- Plan migrations and validate disaster recovery.
- Handle monorepos, LFS, submodules, and worktrees.
- Automate Git safely with machine-readable interfaces.
- Correlate source commits with immutable build artifacts.
- Communicate blast radius, rollback, audit evidence, and preventive actions.

The senior answer is not merely a command. It explains **why**, **risk**, **validation**, **rollback**, and **organizational control**.

---

# 12. Rapid-Fire Command Reference

## Repository and Configuration

```bash
git version
git config --list --show-origin
git rev-parse --show-toplevel
git rev-parse --is-inside-work-tree
git status --porcelain=v2 --branch
```

## History and Graph

```bash
git log --graph --oneline --decorate --all
git show --format=fuller <commit>
git merge-base <branch1> <branch2>
git rev-list --left-right --count <a>...<b>
git range-diff <old-range> <new-range>
```

## Objects and Integrity

```bash
git cat-file -t <object>
git cat-file -p <object>
git ls-tree -r <commit>
git count-objects -vH
git fsck --full
git verify-pack -v <pack-index>
```

## Branch and Remote

```bash
git branch -vv
git remote -v
git remote show origin
git fetch --all --prune
git ls-remote origin
git push --force-with-lease
```

## Recovery

```bash
git reflog --date=iso
git log -g --oneline
git fsck --full --no-reflogs --unreachable
git branch recovery/<name> <commit-id>
```

## Conflict Investigation

```bash
git status
git diff --name-only --diff-filter=U
git ls-files -u
git merge --abort
git rebase --abort
git cherry-pick --abort
```

## Search and Debugging

```bash
git grep -n 'pattern'
git log -S'text'
git log -G'regex'
git blame -L 20,40 file
git bisect start
```

## Worktrees and Sparse Checkout

```bash
git worktree list
git worktree add ../hotfix hotfix/incident
git sparse-checkout init --cone
git sparse-checkout set path1 path2
```

## Bundle and Mirror

```bash
git clone --mirror <source>
git push --mirror <destination>
git bundle create repo.bundle --all
git bundle verify repo.bundle
```

## Tracing

```bash
GIT_TRACE=1 git fetch
GIT_TRACE_PERFORMANCE=1 git status
GIT_TRACE_PACKET=1 git fetch
GIT_CURL_VERBOSE=1 git ls-remote origin
GIT_SSH_COMMAND="ssh -vvv" git ls-remote origin
```

---

# 13. Senior-Level Interview Checklist

For each scenario, structure the response in this order:

1. **Clarify scope:** local branch, shared branch, protected release, or production.
2. **Preserve evidence:** current refs, reflog, audit log, CI SHA, filesystem copy.
3. **Assess blast radius:** users, releases, deployments, forks, caches, LFS, submodules.
4. **Choose safe action:** revert, recovery branch, controlled ref update, or restore.
5. **Validate:** graph, diff, tests, signatures, artifacts, and remote refs.
6. **Communicate:** owners, incident/change records, freeze window, user instructions.
7. **Prevent recurrence:** protection, policy, CI validation, training, monitoring.
8. **Document rollback:** exact object IDs and commands, not only branch names.

### Strong interview language

- “I will first capture the current and expected object IDs.”
- “I will avoid destructive garbage collection until recovery is complete.”
- “For a shared protected branch, I prefer a revert rather than rewriting history.”
- “I will correlate the source commit with the immutable artifact digest.”
- “I will use `--force-with-lease`, not an unconditional force push.”
- “I will verify the merge base instead of assuming `HEAD^` is the correct comparison.”
- “Rotating a leaked credential is mandatory even after removing it from history.”
- “A clone is not a complete backup of the Git hosting platform.”
- “The technical fix must be paired with branch protection, audit, and prevention.”

---

# 14. Official References

The following official Git documentation areas are recommended for continued study:

- Git command reference: https://git-scm.com/docs
- Pro Git book: https://git-scm.com/book/en/v2
- Git data model: https://git-scm.com/docs/gitdatamodel
- Revisions: https://git-scm.com/docs/gitrevisions
- Reflog: https://git-scm.com/docs/git-reflog
- Rebase: https://git-scm.com/docs/git-rebase
- Merge: https://git-scm.com/docs/git-merge
- Worktree: https://git-scm.com/docs/git-worktree
- Hooks: https://git-scm.com/docs/githooks
- Maintenance: https://git-scm.com/docs/git-maintenance
- Partial clone: https://git-scm.com/docs/partial-clone
- Sparse checkout: https://git-scm.com/docs/git-sparse-checkout
- Bundle: https://git-scm.com/docs/git-bundle
- Repository integrity: https://git-scm.com/docs/git-fsck
- Garbage collection: https://git-scm.com/docs/git-gc
- Attributes: https://git-scm.com/docs/gitattributes
- Ignore rules: https://git-scm.com/docs/gitignore
- Signing formats: https://git-scm.com/docs/gitformat-signature

---

## End of Document
