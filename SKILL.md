# GitFlow: Automated Recovery & Conflict Resolution

## 🩺 Core Philosophy
**"진단하고, 판단하고, 자동복구한다."**
Git 흐름이 꼬이거나 충돌이 발생하면, 사용자에게 일일이 묻지 않고 에이전트가 직접 상태의 **'안전성'**을 판단하여 자동 복구합니다. 오직 **위험성이 매우 높은 경우만** 사용자에게 확인을 요구합니다.

## ⚠️ Automated Judgment Rules (핵심 흐름)

에이전트는 모든 작업 전에 다음의 **위험도 매트릭스**를 따라 스스로 판단해야 합니다.

| 위험도 | 상황 예시 | 에이전트 행동 |
|:---|---|:---|
| **Safe **(자동처리) | Detached HEAD, Merge Conflict 파일 처리, Local Rebase 실패, 브랜치 이름 오타 | **즉시 진단 후 자동 명령어 실행 및 정상화** |
| **Caution **(자동+보고) | Losing local uncommitted changes, Rebase에서 커밋 메시지 자동 수정 | 백업(Stash 또는 임시 브랜치) 후 자동 처리하고 결과를 알립니다. |
| **Dangerous (필수 확인)** | `git push --force` (원격 기준), `reset --hard` (활용 중 브랜치 대상), Merge가 불가능한 양상 | **반드시 사용자에게 상태를 보고하고 해결책(Option)을 제시하여 확인** 받기 |

## 🧠 Automated Workflow Logic

### 1. 상황 진단 (Auto-Diagnosis)
사용자가 Git 관련 문제를 언급하거나 충돌 파일이 감지되면, 즉시 다음을 실행하여 현황 파악:
1. `git status`: 현재 작업 트리 및 충돌 파일 확인.
2. `git log --oneline -5`: 현재 HEAD 위치 검증.
3. `git reflog --oneline -10`: 마지막 정상 포인트 확인.

### 2. 충돌 자동 처리 (Auto-Conflict Resolution)
Standard Merge/Rebase 충돌이 발생했을 때, 에이전트는 다음 로직으로 자동 처리해야 합니다:
- **충돌 파일 식별**: `git diff --name-only --diff-filter=U`
- **전략 자동 선택**: 
  - 코드 충돌 시: `ours` (현재 브랜치를 우선) 또는 `theirs` (수입된 브랜치를 우선) 중 문맥에 따라 가장 안전한 것 선택.
  - 파일 불필요 충돌 시: 파일 삭제 또는 `ignore-attrs` 처리.
- **완료 처리**: 모든 충돌 해결 후 자동으로 `git add .` 및 `git commit` 또는 `git rebase --continue`를 이어갑니다.

### 3. 꼬인 흐름 자동 복구 (Auto-Flow Recovery)
브랜치가 꼬인 상태(Detached HEAD, Orphan, Lost Commit)를 판단하고 자동 정상화:
- **Detached HEAD**: 
  - 파일 수정이 없으면: `git checkout -` (직전 브랜치로 바로 복귀).
  - 파일 수정이 있다면: `git checkout -b <restored_name>` (새로운 브랜치로 안전하게 분리).
- **실수한 커밋 **(Wrong Commit) 
  - `reflog`에서 마지막 합리적 포인트를 찾아 `git reset --soft` (커밋 기록만 살리고 코드는 그대로 둔 채) 후, 메시지를 `amend`하여 자동 수정합니다.

## 📋 Operational Constraints
- **Safety First**: 무조건 작업 전 `git branch safety-backup-$(date)`: 로 임시 브랜치를 만들어두고 작업을 시작합니다.
- **Atomicity**: 충돌과 복구는 항상 하나의 논리적 플로우로 처리하며, 중간에 중간 상태가 남지 않도록 합니다.

## 📝 Recovery Patterns

### 1. Standard Merge Conflict (자동 처리)
1. 충돌 파일 목록을 추출합니다.
2. 파일의 충돌 마커(`<<<<<<`, `======`, `>>>>>>>`)를 제거합니다.
3. 문맥을 파악하여 가장 논리적인 결과로 코드를 정리합니다.
4. `git add <resolved_files>` 를 실행합니다.
5. `git commit -m "Auto-resolved merge conflicts"**` 로 작업을 완료합니다.</s>

### 2. Rebase Failed / Conflicts (자동 처리)
1. 충돌이 발생한 파일들을 확인합니다.
2. `git checkout --ours <file>` 또는 `git checkout --theirs <file>` 중 해당 상황(예: feature branch 병합)에 맞는 전략으로 처리합니다.
3. 모든 충돌이 해결되면 `git rebase --continue`를 실행하여 자동으로 이어갑니다. 

### 3. Dangerous Action Triggered (사용자 확인 필요)
1. **Force Push가 필요한 상황**: 원격 저장소의 변경 사항을 로컬에서 무시해야 하는 경우.
   - 행동: "이 작업은 원격의 기존 히스토리를overwrite합니다. 진행하시겠습니까? (Y/N)"
2. **Irreversible Reset**: 히스토리가 완전히 지워지는 `reset --hard`.
   - 행동: "이 작업은 최근 커밋을 복구할 수 없게 만듭니다. 진행하시겠습니까? (Y/N)"

## 🎯 Quick Reference

### Auto-Process Commands (Behind the Scenes)

| Situation | Primary Command | Fallback |
|:-:|:-|:-|
| Merge Conflict | `git checkout --theirs` or `ours` | `git merge --abort` |
| Rebase Conflict | `git rebase --continue` | `git rebase --abort` |
| Detached HEAD | `git checkout -` | `git checkout -b <name>` |
| Lost Commit | `git reflog` → `git branch <name> <hash>` | `git fsck --lost-found` |
| Wrong Commit | `git reset --soft @{-1}` | `git reset --hard` |

## 📋 Decision Flowchart (Mental Model)

```
User Reports Issue
  ↓
Auto-Diagnose (status + reflog + log)
  ↓
Is it dangerous? → YES → Ask user for confirmation
  ↓ NO
Is it a known safe pattern?
  ↓ YES → Auto-apply fix → Report result
  ↓ NO → Ask user: " unsure. What should I do?"
```

*Last verified: 2026-04-25*
