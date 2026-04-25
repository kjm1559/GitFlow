# GitFlow: Automated Conflict Resolution & PR Workflow

## 🩺 Core Philosophy
**"Diagnose, auto-recover, and report through PRs & Docs."**
Instead of mere conflict resolution, this skill enables the agent to **self-diagnose**, **auto-recover** Git flow issues (merge conflicts, detached HEAD, orphaned commits), and **automatically report** all fixes and changes through labeled pull requests and synchronized documentation.

## 🎯 Primary Objectives
1.  **Auto-Conflict & Flow Resolution**: Automatically resolve all Git flow bottlenecks (conflicts, detached HEAD, lost commits).
2.  **Automated Workflow Reporting**: All automated fixes, logic additions, and patches are reported via PRs with proper labels (`[feat]`, `[fix]`, `[docs]`).
3.  **Continuous Documentation Sync**: When the core logic or philosophy changes, `README.md` and `SKILL.md` are updated immediately.

## 🛠 Prerequisites
* `git` CLI tools must be available.
* `gh` CLI (GitHub CLI) must be available for automated PR creation.

## ⚠️ Automated Risk Judgment Matrix
Before executing any recovery task, the agent must self-evaluate the risk level:

| Risk Level | Example Situation | Agent Action |
|:---|:-:|:-:|
| **Safe** (Auto) | Detached HEAD, Standard Merge/Rebase conflict | **Execute immediate recovery, then report via PR.** |
| **Caution** (Report) | Local uncommitted changes at risk | Create a safety branch, execute the fix, and report results. |
| **Dangerous** (Must Ask) | Force Push, permanent Commit deletion | **Strictly prompt the user for confirmation** (provide options). |

## 🚑 Auto-Recovery Patterns

### 1. Merge / Rebase Conflict
* **Action**: Identify conflicting files (`git diff --name-only --diff-filter=U`) → Apply `ours`/`theirs` strategy based on context → `git add` → Run `git commit` or `git rebase --continue` automatically.

### 2. Detached HEAD / Orphan
* **Action**: Analyze `git status`. If no modifications exist, run `git checkout -` to immediately return to the previous branch. If there are modifications, create a new safety branch (`git checkout -b safe-recovery`).

### 3. Lost / Amended Commits
* **Action**: Verify state using `git reflog` and `git log`. Recover the most logical commit state using `git reset --soft` or `git branch <name> <hash>`.

## 📢 Auto-Reporting & Workflow (PR & Docs Automation)

When all technical tasks are completed, the agent MUST execute the following automation workflow:

### 1. Commit & Pull Request Convention
All changes and fixes must be committed with a specific prefix:
* `[feat]`: New automated recovery features, patterns, or logic additions.
* `[fix]`: Fixes for bugs, logic improvements in conflict handling.
* `[docs]`: Updates to `SKILL.md` or `README.md`.
* `[refactor]`: Code refactoring with no functional changes.

**PR Title Format**: `[label] description of automated fix` (e.g., `[fix] resolved merge conflict in component X`, `[feat] added auto-detached-head recovery`).

**PR Body Template**:
> ## Summary of Changes
> ### Primary Objective: (Problem description & auto-fix purpose)
> ### File Setup: (Summary of modified key files)
> ### Key Actions: (Summary of automated actions performed)

### 2. Documentation Update Triggers
When the skill's core logic or philosophy changes, the agent MUST update `README.md` and `SKILL.md` immediately to prevent stale documentation.

* **Update triggers**: "Philosophy definition changed", "New risk label added", "Core automation function expanded"

## 📋 Execution Flowchart (Agent's Mental Model)

```
User Reports Git Issue
  ↓
[1] Auto-Diagnosis (status + reflog) → Determine Risk Matrix
  ↓
[2] Automated Recovery (Conflict/Rebase/Head fixes)
  ↓
[3] Create Safety Branch (if Risk is Caution/Dangerous)
  ↓
[4] Commit with Label ([fix]/[feat]/[docs])
  ↓
[5] Generate Auto PR (gh pr create) & Update README.md/SKILL.md
```

*Last verified: 2026-04-25*
