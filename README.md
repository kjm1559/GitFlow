# GitFlow 🩺

**"Diagnose, auto-resolve, and normalize via PR & Docs."**
**The Git Agent for Automated Conflict Resolution and Workflow Normalization**

## 📖 Overview
This skill enables the agent to automatically diagnose and resolve Git conflicts, tangled branches, and erroneous commits.

Instead of manual intervention, the core purpose is: **"Auto-Recover → Labeled PR Generation → Documentation Sync"**.

## 🎯 Primary Objectives
1.  **Automated Conflict & Flow Resolution**: Self-diagnose and resolve Git flow bottlenecks (conflicts, detached HEAD, lost commits).
2.  **Automated PR Reporting**: All fixes and workflow changes are reported via Pull Requests with proper labels (`[feat]`, `[fix]`).
3.  **Continuous Documentation Sync**: `README.md` and `SKILL.md` are kept in sync whenever core logic changes.

## ✨ Core Features

### 1. Auto-Diagnosis
Comprehensively analyzes `git status`, `git log`, and `git reflog` to identify the root cause of Git flow issues and determine risk levels.

### 2. Risk-Based Automated Recovery
| Risk Level | Example Situation | Agent Action |
|:---|:-:|:-:|
| **Safe** (Auto) | Detached HEAD, Standard Merge/Rebase conflict | **Execute immediate recovery & report via PR.** |
| **Caution** (Report) | Risk of losing local uncommitted changes | Create a safety branch, execute the fix, and notify the user. |
| **Dangerous** (Ask) | Force Push, permanent Commit deletion | **Strictly prompt the user for confirmation** (provide options). |

### 3. Automated PR Creation & Labeling
Upon completing an automated task, the agent MUST generate a Pull Request using the `gh` CLI:
* **Title Format**: `[label] Summary of automated fix` (e.g., `[feat]`, `[fix]`, `[docs]`)
* **PR Body**: Includes the objective of the fix, changed files, and key automated actions performed.

### 4. Documentation Sync
When the skill's core logic or philosophy changes, `README.md` and `SKILL.md` are updated immediately to maintain accuracy.

## 🚀 Installation

Clone or link this repository into your `.hermes/skills` directory:

```bash
cd ~/.hermes/skills
ln -sf ~/project/GitFlow git-flow-recovery
```

## 📝 Commit & PR Convention (Labeling Rules)

This repository adheres strictly to the following label rules for all commits and PRs:

| Label | Purpose |
|:---|:-|
| `[feat]` | New automated features, recovery patterns, or PR generation logic |
| `[fix]` | Bug fixes, improvements to conflict resolution logic, typo corrections |
| `[style]` | Code formatting, whitespace changes (no logic changes) |
| `[docs]` | Updates to `SKILL.md` or `README.md` documentation |
| `[refactor]` | Code refactoring (no functional changes) |
| `[chore]` | Build processes, scripts, and other tooling updates |
| `[ci]` | CI/CD pipeline changes, automated workflow updates |

**PR Title Examples**:
- `[feat] add automated merge conflict resolution strategy`
- `[fix] correct dangerous action judgment rules`
- `[docs] update workflow and add quick reference to SKILL.md`

## ⚠️ Usage Notes
- This skill is designed for **auto-recovery**. All normalizations are reported through PRs.
- A safety backup branch is created automatically before all tasks.
- **Force Push** and other remote-altering operations require explicit user confirmation.

---

### 🛠 Tech Stack
*   **Base**: Python (hermes_tools), Bash, Git
*   **Reporting**: GitHub CLI (`gh`)
*   **Core Logic**: Pattern Matching based on `git reflog`, `status` & `log` outputs.
