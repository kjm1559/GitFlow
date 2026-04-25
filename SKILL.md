# GitFlow: Workflow Optimization & Flow Recovery

## 🩺 Core Philosophy
**"Focus on the Flow, automate the recovery."**
This skill specializes in managing **Git Workflows **(branching strategies, merge patterns) and **recovering tangled histories**. It does not focus on basic Git commands (add, commit, push) but rather on optimizing the high-level flow, diagnosing flow bottlenecks, and normalizing workflows through automated Pull Requests.

## 🎯 Primary Objectives
1.  **Flow Optimization**: Automatically decide and apply the most efficient workflow strategy (Fast-forward, Squash, Merge, or Rebase) based on branch context.
2.  **Tangle Resolution**: Diagnose and untangle corrupted branch histories (Detached HEAD, Orphaned branches, or Rebase dead-ends) autonomously.
3.  **Automated Workflow Reporting**: All flow modifications and normalizations are reported via Pull Requests with workflow-appropriate labels.

## 🛠 Prerequisites
* `git` CLI tools must be available for flow management.
* `gh` CLI (GitHub CLI) is highly recommended for automated PR generation.

## 🧠 Workflow Diagnosis & Decision Logic
Instead of generic conflict resolution, the agent acts as a **Workflow Director**:

| Workflow State | Diagnosis Criteria | Recommended Strategy |
|:---|:-:|:-|
| **Fast-forwardable** | Linear history, clean tip | Automatically perform `Fast-forward Merge` |
| **Tangled **(Rebase) | Multiple heads, lost commits | Utilize `git reflog` to locate the parent branch, then `rebase` or `cherry-pick`|
| **Merge Conflict **(Strategy) | Simultaneous changes on same files | **See Risk Judgment below** — never blind `ours`/`theirs` |
| **Detached/Orphan** | `HEAD` pointing to an unanchored commit | Automatically bind to a new feature branch or `reset` to the active track. |

## ⚠️ Risk Judgment Matrix & Safety Rules

Before executing **any** Git operation, the agent must evaluate risk:

### 5-Tier Risk Scale

| Level | Name | Criteria | Agent Action |
|:---:|:---|:-|:-|
| **S** | **Safe** | Detached HEAD (no modifications), branch rename/cleanup, commit message typo | Auto-execute, then report via PR |
| **W** | **Watch** | Local uncommitted changes at stake, simple rebase (no code overlap) | Create safety branch, execute, **report** results |
| **C** | **Caution** | Merge/Rebase conflict on **non-code** files (`.gitignore`, lock files, whitespace-only) | Auto-resolve after identifying file type, then report |
| **R** | **Restricted-Auto** | Merge/Rebase conflict on **code files** or significant text overlap | **Understand both sides first.** Present conflict summary → show options → let user decide. **Never blind `ours`/`theirs`** |
| **D** | **Dangerous** | `--force`, `--hard`, orphaned commits, irreversible rebase | **Mandatory user confirmation.** Always provide reflog-based rollback reference |

### 🔒 Mandatory Safety Rules

#### 1. No Blind `ours`/`theirs` Resolution
```
NEVER auto-apply git checkout --ours/--theirs to source code files.
```
- Always diff/conflict summary first
- Present both sides' changes to user
- User makes final decision on **any** code-related conflicts

#### 2. Reset Family Requires Explicit Consent
```
ALL -requires explicit approval from user with rollback reference:
  - git reset --hard
  - git reset --soft
  - git reset --mixed
  - git reset --keep
```
- Always provide the reflog position before applying
- Never auto-apply in interactive rebase without confirmation

#### 3. Force Push Requires Explicit Consent
```
ALL force variants require explicit approval:
  - git push --force
  - git push --force-with-lease
  - git push --force-if-includes
```
- Show current remote state vs. intended state
- Confirm no team members depend on the force-pushed branch

## 🚑 Auto-Recovery Patterns

### 1. Merge / Rebase Conflict
* **Action**: Identify conflicting files (`git diff --name-only --diff-filter=U`) → **Never blindly apply** `ours`/`theirs` to code files → Present a summary to the user or resolve **only** if file type is trivial (e.g., `.gitignore`, lock files).

### 2. Detached HEAD / Orphan
* **Action**: Analyze `git status`. If no modifications exist, run `git checkout -` to immediately return to the previous branch. If there are modifications, create a new safety branch (`git checkout -b safe-recovery`).

### 3. Lost / Amended Commits
* **Action**: Verify state using `git reflog` and `git log`. Recover the most logical commit state using `git reset --soft` or `git branch <name> <hash>`.

## 📢 Automated Workflow Labels & Reporting

When normalizing a Git Flow, the following labels should be used in commit titles and PR bodies:

| Label | Purpose |
|:---|:-|
| `[workflow]` | Changes related to branch strategy, merge patterns, or flow rules |
| `[fix]` | Fixing tangled histories, orphaned branches, or broken rebase chains |
| `[optimize]` | Improving flow efficiency (e.g., converting a merge commit to a squash) |
| `[docs]` | Updating the skill's documentation or workflow rules |
| `[feat]` | Adding new workflow heuristics or automated recovery patterns |

**PR Title Format**: `[label] workflow change summary` (e.g., `[workflow] rebase feature/x onto main`, `[fix] resolved tangled rebase chain involving branches A & B`).

## 📋 Agent Execution Flowchart

```
Diagnose Current State (status + log + reflog)
  ↓
Is the flow tangled or optimized?
  ↓
[1] If Optimized & Clean: Recommend auto-merge if pending.
  ↓ [2] If Tangled/Broken:
     a. Identify root branch via reflog.
     b. Execute recovery (rebase/reset/checkout).
     c. Verify flow integrity.
  ↓
Commit with Workflow Label ([fix]/[workflow])
  ↓
Generate PR via `gh pr create` to document the workflow correction.
```

*Last verified: 2026-04-25*
