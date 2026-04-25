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
| **Merge Conflict **(Strategy) | Simultaneous changes on same files | Summarize conflicts and present options; **NEVER** apply `ours`/`theirs` blindly.|
| **Detached/Orphan** | `HEAD` pointing to an unanchored commit | Bind to a new feature branch or **reset** (Restricted-Auto). |

## 🛠 Core Features

### 1. Automated Branch Flow Diagnosis
By analyzing `git status`, `git log`, and remote tracking states, the agent determines the current "flow health" of the repository.

### 2. Smart Merge/Rebase Execution
When a merge or rebase causes a bottleneck, the agent automatically decides the next step:
* **Conflict**: Identify conflicting files and **summarize the conflict to present options**. **NEVER** apply `ours`/`theirs` unconditionally — the user **must** decide.
* **Abort & Reset**: If a workflow state is unrecoverable, it automatically aborts the operation and suggests a safe rollback.

### 3. Workflow Normalization & PR Generation
Instead of fixing code, this skill fixes the **flow**. Once the flow is normalized:
* **Action**: Commit the normalized state with a workflow-specific label.
* **Report**: Generate a PR to document how the workflow was successfully restored or optimized.

## 🛡️ Safety Rules (MUST follow)

### [Caution] Conflict Resolution
* **NEVER** apply `ours` or `theirs` unconditionally.
* When conflicts arise, **summarize the conflict** and **present options** to the user.
* Let the user decide the resolution strategy.

### [Restricted-Auto] Reset Family (git reset, git revert)
* `git reset` (hard/mixed/soft), `git revert` are **destructive** operations that rewrite history.
* **Must present**: the reflog rollback point (where to restore from), the exact command, and the scope of loss.
* **Requires explicit user confirmation** before execution.

### [Restricted-Auto] Force Push (git push --force, --force-with-lease)
* `--force` **destroys remote commits** and must never be automated.
* **Must present**: (1) Diverged commits to be lost, (2) Safe alternative (e.g., `--force-with-lease`, `--force-with-lease=<ref>`), (3) Pre-push backup branch.
* **Requires explicit user confirmation** before execution.

### 📢 Automated Workflow & Commit Labels

When normalizing a Git Flow, use these tags in commit titles and PR bodies:

| Tag | Purpose |
|:---|:-|
| `[feat]` | Add new workflow heuristics or automated recovery patterns |
| `[fix]` | Fix tangled histories, orphaned branches, or broken rebase chains |
| `[docs]` | Update documentation (`SKILL.md`, `README.md`, etc.) |
| `[style]` | Code/style changes that do NOT affect logic (whitespace, formatting) |
| `[refactor]` | Refactor workflow logic without changing behavior |
| `[perf]` | Improve performance of workflow heuristics or scripts |
| `[test]` | Add or update test cases for workflow logic |
| `[chore]` | Maintenance tasks, tooling, repository management |
| `[ci]` | CI/CD pipeline or automation script updates |
| `[workflow]` | Changes to branch strategy, merge patterns, or core flow rules (GitFlow-specific) |
| `[optimize]` | Improve flow efficiency (e.g., convert merge to squash, rebase automation) — GitFlow-specific |
| `[revert]` | Revert a previous commit or workflow change |

**PR Title Format**: `[tag] workflow change summary` (e.g., `[workflow] rebase feature/x onto main`, `[fix] resolved tangled rebase chain involving branches A & B`).

## 📋 Agent Execution Flowchart

```
Diagnose Current State (status + log + reflog)
  ↓
Is the flow tangled or optimized?
  ↓
[1] If Optimized & Clean: Recommend auto-merge if pending.
  ↓ [2] If Tangled/Broken:
     a. Identify root branch via reflog.
     b. Execute recovery (rebase/checkout); **reset requires explicit consent** (Restricted-Auto).
     c. Verify flow integrity.
  ↓
Commit with Workflow Label ([fix]/[workflow])
  ↓
Generate PR via `gh pr create` to document the workflow correction.
```

*Last verified: 2026-04-25*
