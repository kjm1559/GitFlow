# GitFlow 🩺

**"진단하고, 자동으로 해결하고, PR과 문서로 정상화하는 Git 에이전트"**

## 📖 개요
이 스킬은 Git 충돌, 꼬인 흐름, 실수한 커밋을 **에이전트가 스스로 판단하여 자동 처리**합니다.

핵심은 단순 복구가 아닌, **"자동 복구 → 라벨링된 PR 생성 → 문서 동기화"**의 완전한 자동화 워크플로우입니다.

## 🎯 주요 목표
1.  **자동 충돌/꼬임 해결**: Git 흐름의 마비를 스스로 진단 및 복구 (사용자 개입 최소화).
2.  **PR 자동 보고**: 모든修復 및 스킬 변경 사항을 `[feat]`/`[fix]` 라벨이 붙은 PR로 자동 보고.
3.  **문서 동기화**: `SKILL.md` 및 `README.md`의 지속적인 동기화 유지.

## ✨ 주요 기능

### 1. 상황 진단 (Auto-Diagnosis)
`git status`, `git log`, `git reflog`를 종합 분석하여 문제의 원인을 파악하고 위험도를 결정합니다.

### 2. 위험도 판단 & 자동 복구 (Automated Risk-Based Recovery)
| 위험도 | 상황 예시 | 에이전트 행동 |
|:---|:-:|:-:|
| **Safe** (자동) | Detached HEAD, Merge/Rebase 충돌 | **즉시 정상화 후 PR 보고** |
| **Caution** (보고) | 로컬 변경사항 손실 위험 | 임시 브랜치 생성 후 실행 및 결과 보고 |
| **Dangerous** (확인) | Force Push, 원본 Commit 삭제 | 반드시 사용자에게 확인 요청 (옵션 제시) |

### 3. PR 자동 생성 및 라벨링 (Auto-Reporting)
모든 자동화 작업 완료 시, `gh` CLI를 사용하여 PR 생성을 유도합니다:
* **제목 포맷**: `[라벨] 내용 요약` (`[feat]`, `[fix]`, `[docs]`, `[refactor]` 사용)
* **PR 본문**: 변경 목적, 파일 구성, 핵심 행동 요약

### 4. 문서 동기화 (Docs Sync)
스킬의 주요 로직이나 철학이 변경될 때 `README.md`와 `SKILL.md`를 즉시 업데이트합니다.

## 🚀 설치 방법

레포지토리를 `.hermes/skills` 디렉토리로 복사하거나 링크합니다.

```bash
cd ~/.hermes/skills
ln -sf ~/project/GitFlow git-flow-recovery
```

## 📝 커밋 및 PR 컨벤션 (Commit Convention)

이 스킬(레포지토리)은 다음 라벨링 규칙을 엄격히 따릅니다:

| 라벨 | 용도 |
|:---|:-|
| `[feat]` | 새로운 자동화 기능, 복구 패턴, PR 생성 로직 추가 |
| `[fix]` | 버그 수정, 충돌 해결 로직 개선, 오타 교정 |
| `[style]` | 코드 포맷, 레이블 변경 (논리 없음) |
| `[docs]` | `SKILL.md` 또는 `README.md` 문서화 작업 |
| `[refactor]` | 기존 코드 재구성 (기능 변동 없음) |
| `[chore]` | 빌드 절차, 스크립트, 기타 도구 작업 |

**PR 제목 예시**:
- `[feat] add automated merge conflict resolution strategy`
- `[fix] correct dangerous action judgment rules`
- `[docs] update workflow and add quick reference to SKILL.md`

## ⚠️ 사용 전 참고 사항
- 이 스킬은 **자동 복구**를 핵심으로 하며, **정상화된 결과는 반드시 PR 통해 보고**됩니다.
- 모든 작업 전 안전 장치를 위한 임시 브랜치 생성이 보장됩니다.
- `Force Push` 등 원격 상태 변경 관련해서는 반드시 확인 절차를 따릅니다.

---

### 🛠 기술 스택
*   **Base**: Python (hermes_tools), Bash, Git
*   **Reporting**: GitHub CLI (`gh`)
*   **Logic**: Pattern Matching based on `git reflog`, `status` & `log` outputs.
