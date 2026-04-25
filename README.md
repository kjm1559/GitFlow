# GitFlow 🩺

**"Focus on the Flow, automate the recovery."**
**Smart Branch Flow Optimization & Tangle Resolution Agent**

## 📖 Overview
This skill specializes in **Git Workflows **(branching strategies, merge patterns)** and **recovering tangled branch histories**. 

Unlike generic Git skills that focus on basic commands (e.g., `add`, `commit`), GitFlow focuses on optimizing the high-level flow, diagnosing flow bottlenecks, and normalizing the development process through automated Pull Requests.

## 🎯 Primary Objectives
1.  **Flow Optimization**: Automatically decide and apply the most efficient workflow strategy (Fast-forward, Squash, Merge, or Rebase) based on branch context.
2.  **Tangle Resolution**: Diagnose and untangle corrupted branch histories (Detached HEAD, Orphaned branches, or Rebase dead-ends) autonomously.
3.  **Automated Workflow Reporting**: All flow modifications and normalizations are reported via Pull Requests with workflow-appropriate labels.

## ✨ Key Features

### 1. Workflow Health Diagnosis
By comprehensively analyzing `git status`, `git log`, and remote tracking states, the agent diagnoses the "health" of the repository's current workflow.

### 2. Smart Merge/Rebase Heuristics
When a merge or rebase creates a bottleneck, the agent automatically decides the next step:
* **Conflict Resolution**: Identifies conflicting files and applies the most logical resolution (`theirs` for feature merges, `ours` for hotfixes) based on the active workflow.
* **Abort & Rollback**: If a workflow state becomes unrecoverable, it automatically aborts the operation and creates a clean rollback point.

### 3. Flow Normalization & PR Automation
Instead of fixing code, this skill fixes the **flow**. Once the flow is normalized, the agent commits the normalized state with a workflow-specific label and initiates an automated PR.

### 4. Safe Branch Management
Always ensures workflow integrity by creating safety tracking branches before performing destructive workflow changes (Force Push, Hard Reset).

## 🚀 Installation

Clone or link this repository into your `.hermes/skills` directory:

```bash
cd ~/.hermes/skills
ln -sf ~/project/GitFlow git-flow-recovery
```

## 📝 Commit & Workflow Convention (Labeling Rules)

This repository strictly adheres to the following label rules for workflow management and PRs:

| Label | Purpose |
|:---|:- |
| `[workflow]` | Changes to branch strategy, merge patterns, or core flow rules |
| `[fix]` | Fixing tangled histories, orphaned branches, or broken rebase chains |
| `[optimize]` | Improving flow efficiency (e.g., converting merge commits to squash, rebasing) |
| `[docs]` | Updates to `SKILL.md` or `README.md` |
| `[feat]` | Adding new workflow heuristics or automated branch detection patterns |
| `[chore]` | Maintenance tasks, scripts, and repository management |
| `[ci]` | CI/CD pipeline updates, automated workflow scripts |

**PR Title Examples**:
- `[workflow] rebase feature/x onto main for linear history`
- `[optimize] squash merge commit history on develop branch`
- `[fix] resolved tangled rebase chain involving branches A & B`

## ⚠️ Usage Notes
- This skill focuses on the **workflow layer**, not low-level Git object repair.
- All normalizations are reported through Pull Requests to maintain a traceable audit trail.
- Automatic safety tracking branches are created before any destructive operations.

---

### 🛠 Tech Stack
*   **Base**: Python (hermes_tools), Bash, Git
*   **Reporting**: GitHub CLI (`gh`)
*   **Core Logic**: Workflow heuristics based on `git reflog`, `status`, and `log` lineage analysis.
