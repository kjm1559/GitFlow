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
| **Merge Conflict **(Strategy) | Simultaneous changes on same files | Auto-resolve via `ours`/`theirs` or `theirs` based on the active workflow branch. |
| **Detached/Orphan** | `HEAD` pointing to an unanchored commit | Automatically bind to a new feature branch or `reset` to the active track. |

## 🛠 Core Features

### 1. Automated Branch Flow Diagnosis
By analyzing `git status`, `git log`, and remote tracking states, the agent determines the current "flow health" of the repository.

### 2. Smart Merge/Rebase Execution
When a merge or rebase causes a bottleneck, the agent automatically decides the next step:
* **Conflict**: Identify conflicting files and apply the most logical resolution (`theirs` for feature merges, `ours` for hotfixes).
* **Abort & Reset**: If a workflow state is unrecoverable, it automatically aborts the operation and suggests a safe rollback.

### 3. Workflow Normalization & PR Generation
Instead of fixing code, this skill fixes the **flow**. Once the flow is normalized:
* **Action**: Commit the normalized state with a workflow-specific label.
* **Report**: Generate a PR to document how the workflow was successfully restored or optimized.

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
