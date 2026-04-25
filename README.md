# GitFlow 🩺

**"진단하고, 판단하고, 자동복구한다."**
**Git 흐름의 꼬임을 스스로 해결하는 자동 복구 에이전트**

## 📖 개요
이 스킬은 Git 충돌, 꼬인 흐름, 실수한 커밋을 **에이전트가 스스로 판단하여 자동 처리**합니다.

필수 확인이 필요한 극히 제한된 상황(Force Push 등)을 제외하고는 **에이전트가 자동 복구하여 정상화**하여 사용자 개입을 최소화합니다.

## ✨ 주요 기능

### 1. 상황 진단 (Auto-Diagnosis)
`git status`, `git log`, `git reflog`를 종합 분석하여 문제의 원인을 파악합니다.

### 2. 위험도 판단 (Automated Judgment)
작업 전 3단계 위험도 매트릭스로 안전성을 스스로 판단합니다.

| 위험도 | 상황 | 에이전트 행동 |
|:---|:-:|:-|:-:|
| **Safe** | 일반적인 충돌, 꼬인 흐름 | **즉시 자동 처리 및 정상화** |
| **Caution** | 로컬 변경사항 손실 위험 | 자동 백업 후 처리 (보고) |
| **Dangerous** | Force Push, irreversible Reset | **반드시 사용자 확인 요청** |

### 3. 자동 복구 (Auto-Processing)
대부분의 충돌과 꼬인 브랜치는 **자동으로 정상화**합니다.
- 파일 충돌: 문맥에 따라 `ours`/`theirs` 자동 선택
- Detached HEAD: 자동으로 복귀 또는 새로운 브랜치로 분리
- 실수 커밋: `reflog`를 이용해 가장 가까운 지점으로 복원

### 4. 사용자 개입 (Confirmed Only)
원격 히스토리 overwrite, 영구 삭제 등 **돌이킬 수 없는 작업**만 사용자에게 확인을 요청합니다.

## 🚀 설치 방법

레포지토리를 `.hermes/skills` 디렉토리로 복사하거나 링크합니다.

```bash
cd ~/.hermes/skills
ln -sf ~/project/GitFlow git-flow-recovery
```

## 📝 커밋 컨벤션

이 레포지토리의 커밋 및 PR 은 다음 라벨을 사용합니다.

| 라벨 | 용도 |
|:---|:-|
| `[feat]` | 새로운 기능, 스킬 로직 추가 |
| `[fix]` | 버그 수정, 오타 교정 |
| `[style]` | 코드 포맷, 레이블 변경 |
| `[refactor]` | 코드 리팩토링 |
| `[docs]` | 문서 업데이트 |
| `[chore]` | 기타 작업을 포함하는 커밋 |

**PR 제목 예시**:
- `[feat] add automated conflict resolution logic`
- `[fix] correct dangerous action judgment rules`
- `[docs] update SKILL.md philosophy and workflow`

## ⚠️ 사용 전 참고 사항
- 이 스킬은 **자동 복구**를 핵심 철학으로 합니다.
- 모든 작업 전 자동 임시 브랜치 생성이 보장됩니다.
- Force Push 등 원격 상태 변경 관련해서는 반드시 확인 절차를 따릅니다.

---

### 🛠 기술 스택
*   **Base**: Python (hermes_tools) & Bash
*   **Logic**: Pattern Matching based on `git reflog`, `status` & `log` outputs.
