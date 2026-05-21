# AGENTS.md
# 멀티 에이전트 오케스트레이션 대시보드 — 에이전트 가이드

이 프로젝트는 7개의 특화된 AI 에이전트가 협력하여 작업을 계획, 구현, 검증, 시각화합니다.
각 에이전트는 `.opencode/agents/` 디렉토리의 마크다운 파일로 정의되어 있습니다.

---

## 에이전트 목록

| 에이전트 | 파일 | 모드 | 색상 | 핵심 역할 |
|---------|------|------|------|-----------|
| **Orchestrator** | `orchestrator.md` | primary | 🟣 Indigo | 전체 파이프라인 지휘 |
| **Planner** | `planner.md` | subagent | 🟣 Violet | tasks.json 생성 |
| **Developer** | `developer.md` | subagent | 🟢 Emerald | 기능 구현 |
| **Reviewer** | `reviewer.md` | subagent | 🟡 Amber | 코드 1차 검토 |
| **QA** | `qa.md` | subagent | 🔴 Red | 통합 테스트 |
| **Monitor** | `monitor.md` | subagent | ⚫ Slate | 상태 감시 |
| **Dashboard** | `dashboard.md` | subagent | 🔵 Sky | HTML 시각화 |

---

## 에이전트 상세

---

### 🟣 Orchestrator

**파일**: `.opencode/agents/orchestrator.md`
**모드**: `primary` (Tab 키로 전환 가능한 주 에이전트)

**역할**
전체 멀티 에이전트 파이프라인의 총 지휘자. 사용자 요청을 분석하고 서브에이전트를 순서대로 호출하여 작업을 완료시킨다.

**담당**
- 사용자 요청 → 실행 계획 수립
- Planner, Developer, Reviewer, QA, Dashboard 순서로 에이전트 디스패치
- `pipeline_state.json` 유지 관리
- 실패 시 복구 전략 실행 (재시도 → 에스컬레이션)

**수정 가능 파일**
- `pipeline_state.json`
- `tasks.json`

**수정 불가 파일**
- `src/**`, `tests/**`, `reports/**`, `logs/**`, `dashboard/**`

---

### 🟣 Planner

**파일**: `.opencode/agents/planner.md`
**모드**: `subagent` (Orchestrator가 호출)

**역할**
사용자 요청을 원자적인 작업 목록으로 분해하여 `tasks.json`을 생성한다. 코드는 절대 작성하지 않는다.

**담당**
- 요청 분석 및 작업 분해
- 우선순위 및 의존 관계 정의
- `acceptance_criteria` 작성 (QA 테스트 기준)

**수정 가능 파일**
- `tasks.json` (생성 및 초기화)

**핵심 제약**
- 코드 작성 금지
- 다른 에이전트 호출 금지

---

### 🟢 Developer

**파일**: `.opencode/agents/developer.md`
**모드**: `subagent` (Orchestrator가 호출)

**역할**
`tasks.json`에서 할당된 task를 받아 `src/` 하위에 실제 코드를 구현한다.

**담당**
- task 명세에 따른 기능 구현
- 기존 코드 패턴 준수
- 구현 완료 후 `status: "review"` 업데이트

**수정 가능 파일**
- `src/**`
- `tasks.json` (상태 업데이트만)

**허용 bash 명령**
- `node`, `npm run`, `python`, `npx` (빌드/실행 전용)

**핵심 제약**
- `as any`, `@ts-ignore` 사용 금지
- 빈 catch 블록 금지
- `tests/`, `reports/`, `logs/`, `dashboard/` 수정 금지

---

### 🟡 Reviewer

**파일**: `.opencode/agents/reviewer.md`
**모드**: `subagent` (Orchestrator가 호출)

**역할**
Developer가 완성한 코드를 QA 이전에 검토한다. 승인/반려 판정을 내리고 `reports/review_report.json`을 작성한다.

**담당**
- 보안 이슈 (하드코딩 시크릿, XSS, SQL 인젝션)
- 치명적 버그 (미처리 예외, null 역참조)
- 코드 품질 (패턴 일관성, 에러 핸들링)
- `acceptance_criteria` 충족 여부 확인

**판정 기준**
- `approved` → `status: "qa_ready"`로 업데이트
- `rejected` → `status: "in_progress"` + `review_feedback` 추가

**수정 가능 파일**
- `reports/review_report.json`
- `tasks.json` (상태 업데이트만)

**핵심 제약**
- `src/` 코드 직접 수정 금지
- 모호한 피드백 금지 ("개선 필요" → 구체적 수정 방향 명시)

---

### 🔴 QA

**파일**: `.opencode/agents/qa.md`
**모드**: `subagent` (Orchestrator가 호출)

**역할**
Reviewer가 승인한 기능을 통합 테스트한다. `acceptance_criteria` 기반 시나리오를 작성하고 실행하여 `reports/test_result.json`에 기록한다.

**담당**
- 해피 패스 / 엣지 케이스 / 에러 시나리오 테스트
- `tests/` 디렉토리에 실행 가능한 테스트 파일 작성
- 모든 결과 기록 (통과 + 실패 모두)

**판정 기준**
- 전체 통과 → `status: "done"`
- 실패 있음 → `status: "failed"` + `qa_failure_summary`

**수정 가능 파일**
- `tests/**`
- `reports/test_result.json`
- `tasks.json` (상태 업데이트만)

**허용 bash 명령**
- `npm test`, `pytest`, `node --test`, `npx jest`, `npx vitest`

**핵심 제약**
- 테스트를 삭제하거나 스킵하여 통과시키는 행위 금지
- `src/` 코드 수정 금지

---

### ⚫ Monitor

**파일**: `.opencode/agents/monitor.md`
**모드**: `subagent` (Orchestrator가 호출)

**역할**
파이프라인 전반의 상태를 감시하고 이상 징후를 감지한다. 개입하지 않고 관찰 결과만 보고한다.

**감지 항목**
| 조건 | 심각도 |
|------|--------|
| 동일 task가 같은 status에서 5회 이상 관찰 | WARNING |
| 동일 task 실패 2회 | WARNING |
| 동일 task 실패 3회 이상 | CRITICAL |
| pipeline_state와 tasks 상태 불일치 | WARNING |

**수정 가능 파일**
- `logs/monitor_log.json` (append 전용)

**핵심 제약**
- `src/`, `tests/`, `tasks.json`, `pipeline_state.json`, `reports/` 수정 금지
- 다른 에이전트 직접 호출 금지
- 추측 금지 — 관찰 사실만 보고

---

### 🔵 Dashboard

**파일**: `.opencode/agents/dashboard.md`
**모드**: `subagent` (Orchestrator가 호출)

**역할**
모든 상태 파일을 읽어 `dashboard/dashboard.html`을 생성한다. 브라우저에서 전체 파이프라인 상태를 한눈에 확인할 수 있는 UI를 렌더링한다.

**화면 구성**
| 섹션 | 내용 |
|------|------|
| Pipeline Status Banner | 현재 단계 + 진행률 바 + 경과 시간 |
| Agent Status Cards | 에이전트별 상태 카드 (색상 코딩) |
| Task Board | Kanban 컬럼 (pending → done) |
| Review & QA Summary | 리뷰 판정 + 테스트 통과율 |
| Monitor Log Timeline | 최근 10개 모니터링 이벤트 |

**수정 가능 파일**
- `dashboard/**`

**핵심 제약**
- 다른 모든 파일 수정 금지
- 외부 CDN 의존 없이 자급자족 HTML 생성
- 데이터 없는 섹션은 "No data yet"으로 graceful 처리

---

## 파일 소유권 매트릭스

| 파일/디렉토리 | 생성 | 읽기 | 쓰기 |
|--------------|------|------|------|
| `tasks.json` | Planner | 전체 | Planner, Developer, Reviewer, QA |
| `pipeline_state.json` | Orchestrator | 전체 | Orchestrator |
| `src/**` | Developer | Developer, Reviewer, QA | Developer |
| `tests/**` | QA | QA | QA |
| `reports/review_report.json` | Reviewer | Reviewer, Dashboard | Reviewer |
| `reports/test_result.json` | QA | QA, Dashboard | QA |
| `logs/monitor_log.json` | Monitor | Monitor, Dashboard | Monitor |
| `dashboard/dashboard.html` | Dashboard | — | Dashboard |

---

## 작업 상태 전이

```
pending
  └─▶ in_progress   (Developer 착수)
        └─▶ review         (Developer 완료)
              ├─▶ in_progress   (Reviewer 반려 — 재작업)
              └─▶ qa_ready      (Reviewer 승인)
                    └─▶ done          (QA 통과)
                    └─▶ failed        (QA 실패 — Orchestrator 판단으로 재작업 or 에스컬레이션)
```

---

## 에이전트 호출 방법

```bash
# Orchestrator로 전환 (Tab 키 또는 @ 멘션)
@orchestrator 대시보드 로그인 기능을 구현해줘

# 서브에이전트 직접 호출
@planner 이 요구사항을 tasks.json으로 만들어줘
@dashboard 현재 상태로 HTML 리포트 생성해줘
@monitor 파이프라인 상태 점검해줘
```

---

## 관련 파일

- [`agent-team.md`](./agent-team.md) — 에이전트 팀 설계 원본 (상세 스키마, 통신 프로토콜, 실패 처리 정책)
- [`role_card.md`](./role_card.md) — 초기 역할 카드 (Planner, Developer, QA, Dashboard 원형)
- [`.opencode/agents/`](.opencode/agents/) — 에이전트 정의 파일
