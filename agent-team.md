# Agent Team Design
# 멀티 에이전트 오케스트레이션 대시보드

---

## 팀 구성 개요

```
사용자 요청
    │
    ▼
┌─────────────────┐
│  Orchestrator   │  ← 전체 워크플로우 지휘
└────────┬────────┘
         │ 지시
    ┌────┴─────────────────────────┐
    ▼                              ▼
┌─────────┐                 ┌──────────┐
│ Planner │                 │ Monitor  │  ← 상태 감시
└────┬────┘                 └──────────┘
     │ tasks.json
     ▼
┌───────────┐
│ Developer │  ← 구현
└─────┬─────┘
      │ source code
      ▼
┌──────────┐
│ Reviewer │  ← 코드 리뷰 (QA 이전 1차 검증)
└─────┬────┘
      │ 승인 / 반려
      ▼
┌──────────┐
│    QA    │  ← 기능 테스트
└─────┬────┘
      │ test result
      ▼
┌───────────┐
│ Dashboard │  ← 전체 상태 시각화
└───────────┘
```

---

## 에이전트 상세 정의

---

### Orchestrator Agent *(신규)*

#### 목적
전체 에이전트 팀의 워크플로우를 지휘하고 조율한다.
작업의 흐름을 결정하고, 에이전트 간 의존성을 관리하며, 실패 시 복구 전략을 실행한다.

#### 담당 업무
- 사용자 요청을 분석하여 실행 계획 수립
- 각 에이전트에게 작업 지시 및 순서 조율
- 에이전트 간 데이터 전달 중계
- 실패/블로킹 상황 감지 및 재시도 or 에스컬레이션
- 전체 파이프라인 완료 여부 판단

#### 입력
- 사용자 요청
- 각 에이전트의 완료 신호 및 결과

#### 출력
- 에이전트별 작업 지시 (instruction payload)
- pipeline_state.json (현재 파이프라인 실행 상태)
- 최종 완료 보고

#### 상태
- `Idle` — 대기 중
- `Analyzing` — 요청 분석 중
- `Dispatching` — 에이전트에게 작업 분배 중
- `Waiting` — 에이전트 완료 대기 중
- `Recovering` — 실패 복구 중
- `Done` — 전체 파이프라인 완료

#### 수정 가능 범위
- `pipeline_state.json`
- `tasks.json` (Planner 호출 전 초기화)

#### 완료 조건
- 모든 에이전트가 `Done` 또는 `Passed` 상태에 도달
- pipeline_state.json에 최종 결과 기록됨

---

### Planner Agent *(role_card.md 기반)*

#### 목적
작업을 생성하고 다른 Agent에게 할당한다.

#### 담당 업무
- 사용자 요청 또는 Orchestrator 지시를 바탕으로 작업 목록 생성
- 각 작업에 우선순위 및 담당 에이전트 지정
- 작업 간 의존 관계 정의
- tasks.json 상태 관리

#### 입력
- 사용자 요청
- Orchestrator의 실행 지시

#### 출력
- `tasks.json`

#### tasks.json 스키마
```json
{
  "tasks": [
    {
      "id": "task-001",
      "title": "작업 제목",
      "description": "상세 설명",
      "assignee": "developer",
      "priority": "high | medium | low",
      "status": "pending | in_progress | review | done | failed",
      "depends_on": [],
      "created_at": "ISO8601",
      "updated_at": "ISO8601"
    }
  ]
}
```

#### 상태
- `Idle`
- `Planning`
- `Done`

#### 수정 가능 범위
- `tasks.json`

#### 완료 조건
- 작업이 생성되고 담당 에이전트에게 할당됨

---

### Developer Agent *(role_card.md 기반)*

#### 목적
실제 기능 구현을 담당한다.

#### 담당 업무
- Planner가 생성한 task를 받아 코드 작성
- 기능 구현 및 버그 수정
- 작업 완료 후 tasks.json 상태 업데이트

#### 입력
- `tasks.json`의 할당된 task 정보
- 기존 코드베이스 컨텍스트

#### 출력
- `src/` 하위 소스 코드
- tasks.json 상태 업데이트 (`in_progress` → `review`)

#### 상태
- `Idle`
- `Coding`
- `Testing` — 로컬 단위 테스트 수행 중
- `Done`

#### 수정 가능 범위
- `src/`
- `tasks.json`

#### 완료 조건
- 기능 정상 동작 확인
- tasks.json 상태가 `review`로 업데이트됨

---

### Reviewer Agent *(신규)*

#### 목적
Developer가 작성한 코드를 QA 이전에 1차 검토하여 명백한 결함을 조기에 차단한다.

#### 담당 업무
- 코드 품질 검토 (가독성, 패턴 일관성, 불필요한 복잡도)
- 명백한 버그 또는 누락된 예외 처리 확인
- 보안 이슈 체크 (하드코딩된 시크릿, SQL 인젝션 등 명백한 위반)
- 리뷰 결과를 `review_report.json`에 기록
- 반려 시 Developer에게 구체적인 피드백 전달

#### 입력
- `src/` 변경 파일
- `tasks.json`의 task 정보

#### 출력
- `reports/review_report.json`
- tasks.json 상태 업데이트 (`review` → `in_progress` 반려 또는 `qa_ready` 승인)

#### review_report.json 스키마
```json
{
  "task_id": "task-001",
  "verdict": "approved | rejected",
  "issues": [
    {
      "severity": "critical | major | minor",
      "file": "src/example.js",
      "line": 42,
      "message": "설명"
    }
  ],
  "reviewed_at": "ISO8601"
}
```

#### 상태
- `Idle`
- `Reviewing`
- `Approved`
- `Rejected`

#### 수정 가능 범위
- `reports/`
- `tasks.json`

#### 완료 조건
- 모든 `critical` 이슈 없음 → Approved
- Approved 시 tasks.json 상태 `qa_ready`로 변경

---

### QA Agent *(role_card.md 기반)*

#### 목적
Reviewer를 통과한 기능을 통합 테스트하여 최종 품질을 검증한다.

#### 담당 업무
- 기능 테스트 (시나리오 기반)
- 엣지 케이스 및 경계값 테스트
- 오류 확인 및 재현
- 테스트 결과 기록

#### 입력
- Reviewer가 승인한 완료 작업 (`qa_ready` 상태)
- `tasks.json`

#### 출력
- `tests/` 테스트 파일
- `reports/test_result.json`

#### test_result.json 스키마
```json
{
  "task_id": "task-001",
  "result": "passed | failed",
  "test_cases": [
    {
      "name": "테스트 케이스명",
      "status": "passed | failed",
      "error": "에러 메시지 (실패 시)"
    }
  ],
  "tested_at": "ISO8601"
}
```

#### 상태
- `Idle`
- `Testing`
- `Passed`
- `Failed`

#### 수정 가능 범위
- `tests/`
- `reports/`

#### 완료 조건
- 테스트 완료 및 결과 기록
- `Failed` 시 Orchestrator에게 재작업 신호 전달

---

### Monitor Agent *(신규)*

#### 목적
전체 에이전트의 상태와 시스템 건강도를 실시간으로 감시한다.
이상 징후를 감지하고 Orchestrator에게 경고를 전달한다.

#### 담당 업무
- 에이전트별 상태 폴링 및 기록
- 타임아웃 감지 (에이전트가 일정 시간 이상 응답 없음)
- 작업 실패 패턴 감지 (동일 작업 N회 이상 실패)
- 시스템 이벤트 로그 작성
- Orchestrator에게 경고 신호 전달

#### 입력
- 각 에이전트의 상태 신호
- `pipeline_state.json`
- `tasks.json`

#### 출력
- `logs/monitor_log.json`
- Orchestrator로의 경고 이벤트

#### monitor_log.json 스키마
```json
{
  "timestamp": "ISO8601",
  "agent": "developer",
  "event": "timeout | failure | state_change | recovered",
  "detail": "설명",
  "action_taken": "notified_orchestrator | auto_retry | escalated"
}
```

#### 상태
- `Watching` — 정상 감시 중
- `Alerting` — 이상 징후 감지, 경고 전달 중
- `Idle`

#### 수정 가능 범위
- `logs/`

#### 완료 조건
- 파이프라인 전체가 완료될 때까지 상시 동작
- 이상 감지 시 즉시 Orchestrator에게 보고

---

### Dashboard Agent *(role_card.md 기반)*

#### 목적
전체 에이전트 상태와 작업 진행 현황을 HTML 화면으로 시각화한다.

#### 담당 업무
- `tasks.json`, `pipeline_state.json`, `reports/`, `logs/` 읽기
- 에이전트별 현재 상태 카드 렌더링
- 작업 진행률 및 통계 시각화
- 실패/경고 항목 하이라이트
- HTML 정적 파일 생성

#### 입력
- `tasks.json`
- `pipeline_state.json`
- `reports/review_report.json`
- `reports/test_result.json`
- `logs/monitor_log.json`

#### 출력
- `dashboard/dashboard.html`

#### 대시보드 구성 요소
| 섹션 | 내용 |
|------|------|
| Agent Status | 에이전트별 현재 상태 카드 (Idle / Running / Done / Error) |
| Task Board | Kanban 형태의 작업 상태 (pending → in_progress → review → done) |
| Pipeline Flow | 파이프라인 단계별 진행 현황 |
| Log Stream | Monitor 이벤트 로그 타임라인 |
| Summary | 전체 완료율, 실패율, 소요 시간 통계 |

#### 상태
- `Refreshing`
- `Ready`

#### 수정 가능 범위
- `dashboard/`

#### 완료 조건
- 브라우저에서 전체 에이전트 상태 확인 가능

---

## 파일 시스템 구조

```
project/
├── tasks.json                    # Planner 생성, 전 에이전트 공유
├── pipeline_state.json           # Orchestrator 관리
│
├── src/                          # Developer 작업 영역
│
├── tests/                        # QA 작업 영역
│
├── reports/
│   ├── review_report.json        # Reviewer 출력
│   └── test_result.json          # QA 출력
│
├── logs/
│   └── monitor_log.json          # Monitor 출력
│
└── dashboard/
    └── dashboard.html            # Dashboard 출력
```

---

## 에이전트 간 통신 프로토콜

| 발신 | 수신 | 트리거 | 전달 내용 |
|------|------|--------|-----------|
| 사용자 | Orchestrator | 새 요청 | 요청 텍스트 |
| Orchestrator | Planner | 파이프라인 시작 | 요청 분석 결과 |
| Planner | Developer | tasks.json 업데이트 | task 할당 정보 |
| Developer | Reviewer | 코드 완성 신호 | 변경 파일 목록 |
| Reviewer | Developer | Rejected | review_report.json |
| Reviewer | QA | Approved | qa_ready task 정보 |
| QA | Orchestrator | 테스트 완료 | passed / failed 신호 |
| Monitor | Orchestrator | 이상 감지 | 경고 이벤트 |
| Orchestrator | Dashboard | 상태 변경 시 | 렌더링 트리거 |

---

## 작업 상태 전이도

```
pending
  │
  ▼ Developer 착수
in_progress
  │
  ▼ Developer 완료
review
  │
  ├─ Rejected ──→ in_progress (재작업)
  │
  ▼ Reviewer Approved
qa_ready
  │
  ▼ QA 착수
testing
  │
  ├─ Failed ───→ in_progress (재작업)
  │
  ▼ QA Passed
done
```

---

## 실패 처리 정책

| 실패 유형 | 감지 주체 | 처리 방식 | 재시도 한도 |
|-----------|-----------|-----------|------------|
| Developer 타임아웃 | Monitor | Orchestrator에 경고 → 재할당 | 2회 |
| Reviewer Rejected | Reviewer | Developer에게 피드백 재전달 | 3회 |
| QA Failed | QA | Orchestrator → Developer 재작업 | 2회 |
| 연속 실패 | Monitor | Orchestrator 에스컬레이션 → 사용자에게 보고 | — |
