# GitFlow: Automated Conflict Resolution & PR Workflow

## 🩺 Core Philosophy
**"진단하고, 자동복구하고, 정상화 과정을 자동 보고한다."**
Git 충돌, 꼬인 흐름, 실수한 커밋을 **에이전트가 스스로 판단하여 자동 복구**하며, 모든 작업 완료 시 **정확한 라벨링된 PR**과 **업데이트된 문서**를 자동으로 생성합니다.

## 🎯 Primary Objectives (스킬의 목적)
1.  **자동 충돌/꼬임 해결**: Git 흐름의 마비를 스스로 진단 및 복구 (사용자 개입 최소화).
2.  **워크플로우 자동화**: 모든 수정 사항 및 복구는 `[feat]`, `[fix]` 라벨이 붙은 PR 생성을 통해 보고됨.
3.  **문서 동기화**: 스킬의 철학과 주요 기능이 변경될 때 `README.md`를 즉시 갱신.

## 🛠 Prerequisites
* `git` CLI tools are available.
* `gh` CLI (GitHub CLI) is available for PR creation.

## ⚠️ Automated Risk Judgment Matrix (복구 로직)
모든 복구 작업은 다음과 같이 스스로 위험도를 판단하여 실행합니다:

| 위험도 | 상황 예시 | 에이전트 행동 |
|:---|:-:|:-:|
| **Safe** (자동) | Detached HEAD, Merge/Rebase 충돌 | **즉시 실행 후 정상화 보고** |
| **Caution** (보고) | 로컬 변경사항 손실 가능성 | 임시 브랜치 생성 후 실행 및 결과 보고 |
| **Dangerous** (확인) | Force Push, 원본 Commit 삭제 | 반드시 사용자에게 확인 요청 (옵션 제시) |

## 🚑 Auto-Recovery Patterns (자동 복구 매개변수)

### 1. Merge / Rebase Conflict
* **Action**: 충돌 파일 식별 (`git diff --name-only --diff-filter=U`) → `ours`/`theirs` 전략 적용 → `git add` → `git commit` 또는 `git rebase --continue` 자동 실행.

### 2. Detached HEAD / Orphan
* **Action**: `git status` 분석 후 파일 수정 유무 파악. 무수정 시 `git checkout -`, 수정 시 `git checkout -b safe-recovery`.

### 3. Lost / Amended Commits
* **Action**: `git reflog` 및 `git log` 검증 후, 가장 가까운 지점을 찾아 `git reset --soft` 또는 `git branch <name> <hash>`로 복원.

## 📢 Auto-Reporting & Workflow (PR & 문서 자동화)

모든 기술적 작업이 완료되면 즉시 다음 워크플로우를 실행합니다:

### 1. Commit Convention (커밋 라벨링)
모든 로컬 수정 사항은 다음 라벨을 사용하여 커밋합니다:
* `[feat]`: 새로운 복구 기능, 자동화 로직 추가
* `[fix]`: 충돌 해결 로직 개선, 복구 실패 수정
* `[docs]`: `SKILL.md` 또는 `README.md` 업데이트
* `[refactor]`: 기존 코드 재구성

### 2. Pull Request Automation
복구된 브랜치는 반드시 `gh` CLI를 사용하여 다음과 같이 PR 생성이 유도되어야 합니다:
* **Title format**: `[label] description of automated fix` (예: `[fix] resolved merge conflict in component X`)
* **Body format**:
  > ## 변경 사항 요약
  > ### 주요 목적: (문제 설명 및 자동 복구 목적)
  > ### 파일 구성: (변경된 주요 파일 요약)
  > ### 핵심 기능/수정: (에이전트가 수행한 동작 요약)

### 3. Documentation Update
스킬의 주요 로직이나 철학이 변경된 경우, 에이전트는 즉시 `README.md`를 업데이트해야 합니다.
* **Update trigger**: "철학 정의 변경", "새로운 위험도 라벨 추가", "주요 자동화 기능 확장"

## 📋 Operations Flowchart (에이전트 실행 순서)

```
User Reports Git Issue
  ↓
[1] Auto-Diagnosis (status + reflog) → Determine Risk Matrix
  ↓
[2] Automated Recovery (Conflict/Rebase/Head fixes)
  ↓
[3] Create Safety Branch (for all Dangerous/Heavy changes)
  ↓
[4] Commit with Label ([fix]/[feat]/[docs])
  ↓
[5] Generate Auto PR (gh pr create) & Update README.md
```

*Last verified: 2026-04-25*
