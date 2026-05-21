# 멀티 에이전트 오케스트레이션 대시보드

## TL;DR

> **Quick Summary**: 7개 에이전트의 상태와 파이프라인 진행 현황을 한눈에 보여주는 단일 HTML 대시보드를 구현한다. Mock 데이터를 내장하여 서버 없이 파일을 직접 열 수 있다.
>
> **Deliverables**:
> - `dashboard/index.html` — 완전한 단일 파일 대시보드 (CSS·JS 인라인)
>
> **Estimated Effort**: Short (35분)
> **Parallel Execution**: NO — 단일 파일 순차 완성
> **Critical Path**: T1 → T2 → T3

---

## Context

### Original Request
멀티 에이전트 오케스트레이션 관리 대시보드 (HTML + CSS + JS)
- 에이전트 카드 (에이전트 정보, 상태)
- 작업 상태 목록 (요약, 상태, 배정 에이전트)
- 로그/메모 패널 (실행 로그)
- 모델 사용 전략 체크리스트 (패턴 레퍼런스)

### Interview Summary
**Key Discussions**:
- **데이터 로딩**: Mock 데이터 내장 — 서버 없이 파일 직접 오픈 가능
- **테마**: 라이트 테마
- **전략 섹션**: 현재 시스템 패턴을 보여주는 레퍼런스 카드
- **범위**: 35분 내 완료 — 단일 파일, 파일 분리 없음

### Design Direction (frontend-design SKILL 기반)
**콘셉트: "Editorial Data Terminal"**
> Bloomberg 터미널 + 데이터 저널리즘 감성 — 정보가 주인공인 라이트 대시보드

| 요소 | 선택 |
|------|------|
| 헤딩 폰트 | `DM Serif Display` (Google Fonts) |
| 데이터 폰트 | `IBM Plex Mono` (Google Fonts) |
| 배경 | `#FAFAF8` (따뜻한 오프화이트) |
| 텍스트 | `#111111` |
| 액센트 | `#D4410C` (버밀리언 레드) |
| 보더 | `1px solid #E5E5E0`, 라운드 없음 |
| 카드 | 그림자 없음, 테두리만 |
| 배지 | 대문자 모노스페이스 + 컬러 도트 |

**절대 금지**: `Inter`, `Roboto`, `border-radius: 8px`, 보라 계열 그라디언트

### Data Sources (agent-team.md 기반)
에이전트 7개: Orchestrator, Planner, Developer, Reviewer, QA, Monitor, Dashboard
작업 상태: `pending → in_progress → review → qa_ready → done → failed`
로그 심각도: `INFO | WARNING | CRITICAL`
전략 패턴 4개: 리더형, 파이프라인형, 역할분리형, 위원회형

---

## Work Objectives

### Core Objective
`dashboard/index.html` 단일 파일로, 7개 에이전트 상태·작업 목록·로그·전략 패턴을 시각화하는 라이트 테마 대시보드를 완성한다.

### Concrete Deliverables
- `dashboard/index.html` (인라인 CSS + 인라인 JS 포함, 외부 의존 없음)

### Definition of Done
- [ ] 브라우저에서 파일을 직접 열어 4개 섹션이 모두 렌더링됨
- [ ] 에이전트 7개 카드가 상태별 색상 도트로 표시됨
- [ ] 작업 목록이 상태·우선순위 배지와 함께 표시됨
- [ ] 로그가 심각도 색상으로 표시됨
- [ ] 전략 카드 4개 중 현재 적용 항목에 "현재 적용됨" 표시

### Must Have
- 단일 HTML 파일 (서버 불필요)
- `DM Serif Display` + `IBM Plex Mono` 폰트 사용
- 4개 섹션 모두 구현
- 라이트 테마 / 버밀리언 레드 액센트

### Must NOT Have (Guardrails)
- `Inter`, `Roboto`, `Arial`, `system-ui` 폰트 사용 금지
- `border-radius` 남용 금지 (카드에 라운드 없음)
- 보라 계열 그라디언트 금지
- 외부 CDN JS 라이브러리 금지 (Google Fonts CSS는 허용)
- 자동 새로고침 로직 금지 (Mock 데이터 환경에서 무의미)
- 별도 CSS/JS 파일 생성 금지 (인라인 통합)

---

## Verification Strategy

### Test Decision
- **Infrastructure exists**: NO
- **Automated tests**: None
- **Agent-Executed QA**: 브라우저 직접 열기 + 시각 확인

### QA Policy
각 태스크 완료 후 실행 에이전트가 직접 `dashboard/index.html`을 브라우저에서 열어 렌더링 결과를 확인한다.

---

## Execution Strategy

### Sequential Execution (단일 파일 순차 완성)

```
T1 (15분): HTML 뼈대 + 인라인 CSS
  └─▶ T2 (10분): 인라인 JS — Mock 데이터
        └─▶ T3 (10분): 인라인 JS — 렌더러 4개 + init()
```

### Agent Dispatch Summary
- T1 → `visual-engineering` + `frontend-ui-ux` 스킬
- T2 → `quick`
- T3 → `quick`

---

## TODOs

- [ ] 1. HTML 뼈대 + 인라인 CSS 작성

  **What to do**:

  `dashboard/index.html` 파일을 새로 생성하고 아래 내용을 완성한다.

  **HTML 구조**:
  ```html
  <!DOCTYPE html>
  <html lang="ko">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Agent Orchestration Dashboard</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display&family=IBM+Plex+Mono:wght@400;500&display=swap" rel="stylesheet">
    <style>
      /* 여기에 모든 CSS 인라인 작성 */
    </style>
  </head>
  <body>
    <header>...</header>
    <main>
      <section id="agents-section">...</section>
      <section id="tasks-section">...</section>
      <section id="logs-section">...</section>
      <section id="strategy-section">...</section>
    </main>
    <footer>...</footer>
    <script>
      /* T2, T3에서 추가 */
    </script>
  </body>
  </html>
  ```

  **CSS `<style>` 블록에 포함할 내용**:

  *1) CSS 변수 (tokens)*
  ```css
  :root {
    --bg: #FAFAF8;
    --surface: #FFFFFF;
    --border: #E5E5E0;
    --text-primary: #111111;
    --text-secondary: #6B6B6B;
    --accent: #D4410C;
    --success: #16A34A;
    --warning: #B45309;
    --error: #DC2626;
    --info: #1D4ED8;
    --font-display: 'DM Serif Display', serif;
    --font-mono: 'IBM Plex Mono', monospace;
    --space-1: 4px; --space-2: 8px; --space-3: 12px;
    --space-4: 16px; --space-6: 24px; --space-8: 32px;
  }
  ```

  *2) Reset + base*
  ```css
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  body { background: var(--bg); color: var(--text-primary); font-family: var(--font-mono); font-size: 13px; line-height: 1.6; }
  h1, h2, h3 { font-family: var(--font-display); font-weight: 400; }
  ```

  *3) 레이아웃*
  - `<header>`: 좌측 타이틀(`DM Serif Display` 28px) + 우측 타임스탬프(`IBM Plex Mono` 11px)
  - `<main>`: CSS Grid, `grid-template-columns: 1fr 1fr`, `gap: 24px`, `padding: 24px`
  - 각 `<section>`: `border: 1px solid var(--border)`, `background: var(--surface)`, `padding: 20px`
  - 섹션 제목: `DM Serif Display` 18px, `border-bottom: 1px solid var(--border)`, `padding-bottom: 8px`, `margin-bottom: 16px`

  *4) 에이전트 카드 스타일*
  ```css
  .agent-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(180px, 1fr)); gap: 12px; }
  .agent-card { border: 1px solid var(--border); padding: 12px; background: var(--bg); }
  .agent-card .name { font-family: var(--font-display); font-size: 15px; margin-bottom: 6px; }
  .status-dot { display: inline-block; width: 8px; height: 8px; border-radius: 50%; margin-right: 6px; }
  .status-label { font-family: var(--font-mono); font-size: 11px; text-transform: uppercase; letter-spacing: 0.05em; }
  /* 상태 색상 */
  .dot-idle { background: #9CA3AF; }
  .dot-running { background: var(--accent); }
  .dot-done { background: var(--success); }
  .dot-error { background: var(--error); }
  ```

  *5) 작업 목록 테이블 스타일*
  ```css
  .task-table { width: 100%; border-collapse: collapse; }
  .task-table th { font-size: 10px; text-transform: uppercase; letter-spacing: 0.08em; color: var(--text-secondary); border-bottom: 1px solid var(--border); padding: 6px 8px; text-align: left; }
  .task-table td { padding: 8px; border-bottom: 1px solid var(--border); vertical-align: top; }
  .badge { display: inline-block; font-size: 10px; text-transform: uppercase; letter-spacing: 0.06em; padding: 2px 6px; border: 1px solid currentColor; }
  /* 배지 색상 */
  .badge-pending { color: #9CA3AF; }
  .badge-in_progress { color: var(--accent); }
  .badge-review { color: var(--warning); }
  .badge-qa_ready { color: var(--info); }
  .badge-done { color: var(--success); }
  .badge-failed { color: var(--error); }
  .badge-high { color: var(--error); }
  .badge-medium { color: var(--warning); }
  .badge-low { color: var(--text-secondary); }
  ```

  *6) 로그 패널 스타일*
  ```css
  .log-list { list-style: none; max-height: 300px; overflow-y: auto; }
  .log-item { display: grid; grid-template-columns: 90px 70px 1fr; gap: 8px; padding: 6px 0; border-bottom: 1px solid var(--border); font-size: 11px; }
  .log-time { color: var(--text-secondary); }
  .log-severity { text-transform: uppercase; font-weight: 500; }
  .sev-INFO { color: var(--info); }
  .sev-WARNING { color: var(--warning); }
  .sev-CRITICAL { color: var(--error); }
  ```

  *7) 전략 카드 스타일*
  ```css
  .strategy-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
  .strategy-card { border: 1px solid var(--border); padding: 14px; position: relative; }
  .strategy-card.active { border-color: var(--accent); }
  .strategy-card .pattern-name { font-family: var(--font-display); font-size: 15px; margin-bottom: 4px; }
  .strategy-card .pattern-desc { color: var(--text-secondary); font-size: 11px; line-height: 1.5; }
  .applied-badge { position: absolute; top: 10px; right: 10px; font-size: 10px; color: var(--accent); text-transform: uppercase; letter-spacing: 0.06em; }
  ```

  **Must NOT do**:
  - `border-radius` 사용 금지 (도트 제외)
  - `Inter`, `Roboto` 폰트 금지
  - 그림자(`box-shadow`) 남용 금지
  - `<script>` 블록에 JS 작성 금지 (T2·T3에서 추가)

  **Recommended Agent Profile**:
  - **Category**: `visual-engineering`
  - **Skills**: [`frontend-ui-ux`]

  **Parallelization**:
  - **Can Run In Parallel**: NO
  - **Parallel Group**: Sequential Step 1
  - **Blocks**: T2, T3
  - **Blocked By**: None

  **Acceptance Criteria**:
  - [ ] `dashboard/index.html` 파일이 존재함
  - [ ] 브라우저에서 열면 흰 배경 + 헤더 + 4개 빈 섹션 박스가 보임
  - [ ] Google Fonts (DM Serif Display, IBM Plex Mono)가 로드됨 (네트워크 연결 시)
  - [ ] `<script>` 블록이 비어 있거나 플레이스홀더 주석만 있음

  **QA Scenarios**:
  ```
  Scenario: CSS 토큰 및 레이아웃 확인
    Tool: 브라우저에서 dashboard/index.html 직접 열기
    Steps:
      1. 파일 탐색기에서 index.html 더블클릭
      2. 헤더에 "Agent Orchestration Dashboard" 텍스트 확인
      3. 4개 섹션 박스가 2×2 그리드로 배치되었는지 확인
      4. 배경색이 순백이 아닌 크림색(#FAFAF8)인지 확인
    Expected Result: 4개 빈 섹션 박스가 테두리와 함께 렌더링됨
    Evidence: 스크린샷 또는 육안 확인
  ```

  **Commit**: YES (T1 단독)
  - Message: `feat(dashboard): HTML skeleton + inline CSS design system`
  - Files: `dashboard/index.html`

---

- [ ] 2. 인라인 JS — Mock 데이터 추가

  **What to do**:

  T1에서 만든 `dashboard/index.html`의 `<script>` 블록 상단에 Mock 데이터 객체를 추가한다. HTML 구조는 건드리지 않는다.

  **추가할 데이터 구조**:

  *1) 에이전트 7개*
  ```js
  const AGENTS = [
    { id: 'orchestrator', name: 'Orchestrator', role: '파이프라인 지휘', status: 'running', color: '#6366f1' },
    { id: 'planner',      name: 'Planner',      role: 'tasks.json 생성', status: 'done',    color: '#8b5cf6' },
    { id: 'developer',    name: 'Developer',    role: '기능 구현',        status: 'running', color: '#10b981' },
    { id: 'reviewer',     name: 'Reviewer',     role: '코드 검토',        status: 'idle',    color: '#f59e0b' },
    { id: 'qa',           name: 'QA',           role: '통합 테스트',       status: 'idle',    color: '#ef4444' },
    { id: 'monitor',      name: 'Monitor',      role: '상태 감시',        status: 'running', color: '#64748b' },
    { id: 'dashboard',    name: 'Dashboard',    role: 'HTML 시각화',      status: 'idle',    color: '#0ea5e9' },
  ];
  ```
  `status` 값: `'idle' | 'running' | 'done' | 'error'`

  *2) 작업 5개*
  ```js
  const TASKS = [
    { id: 'task-001', title: '로그인 UI 구현',      status: 'done',        priority: 'high',   assignee: 'developer' },
    { id: 'task-002', title: 'API 연동 작업',        status: 'in_progress', priority: 'high',   assignee: 'developer' },
    { id: 'task-003', title: '에러 핸들링 추가',     status: 'review',      priority: 'medium', assignee: 'developer' },
    { id: 'task-004', title: '대시보드 HTML 생성',   status: 'qa_ready',    priority: 'medium', assignee: 'developer' },
    { id: 'task-005', title: '배포 스크립트 작성',   status: 'pending',     priority: 'low',    assignee: 'developer' },
  ];
  ```

  *3) 로그 5개*
  ```js
  const LOGS = [
    { time: '10:42:01', severity: 'INFO',     agent: 'orchestrator', detail: '파이프라인 시작 — task-002 착수' },
    { time: '10:41:33', severity: 'INFO',     agent: 'planner',      detail: 'tasks.json 생성 완료 (5개 작업)' },
    { time: '10:40:55', severity: 'WARNING',  agent: 'monitor',      detail: 'task-003 review 상태 15분 경과' },
    { time: '10:39:10', severity: 'INFO',     agent: 'reviewer',     detail: 'task-003 반려 — null 역참조 위험' },
    { time: '10:38:02', severity: 'CRITICAL', agent: 'qa',           detail: 'task-001 테스트 3회 실패 후 통과' },
  ];
  ```

  *4) 전략 패턴 4개*
  ```js
  const STRATEGIES = [
    {
      name: '리더형 (Orchestrator)',
      desc: '단일 컨트롤러가 전체 흐름을 결정. 명확한 책임, 빠른 의사결정.',
      applied: true,
    },
    {
      name: '파이프라인형 (Sequential)',
      desc: 'Planner→Dev→Review→QA 순차 실행. 각 단계가 이전 결과에 의존.',
      applied: true,
    },
    {
      name: '역할 분리형 (Specialized)',
      desc: '에이전트마다 단일 책임. 교체·확장이 쉽고 버그 추적이 명확.',
      applied: true,
    },
    {
      name: '위원회형 (Committee)',
      desc: '다수 에이전트 투표·합의. 고위험 결정에 적합, 속도 희생.',
      applied: false,
    },
  ];
  ```

  **Must NOT do**:
  - HTML 구조 수정 금지
  - CSS `<style>` 블록 수정 금지
  - 렌더러 함수 작성 금지 (T3에서 담당)

  **Recommended Agent Profile**:
  - **Category**: `quick`
  - **Skills**: []

  **Parallelization**:
  - **Can Run In Parallel**: NO
  - **Parallel Group**: Sequential Step 2
  - **Blocks**: T3
  - **Blocked By**: T1

  **Acceptance Criteria**:
  - [ ] `<script>` 블록에 `AGENTS`, `TASKS`, `LOGS`, `STRATEGIES` 4개 상수가 선언됨
  - [ ] 각 배열 항목 수: AGENTS=7, TASKS=5, LOGS=5, STRATEGIES=4
  - [ ] 브라우저 콘솔에서 `AGENTS[0]` 입력 시 객체 반환됨

  **QA Scenarios**:
  ```
  Scenario: Mock 데이터 콘솔 확인
    Tool: 브라우저 개발자 도구 콘솔
    Steps:
      1. index.html 열기 → F12 → Console 탭
      2. AGENTS.length 입력 → 7 반환 확인
      3. TASKS.length 입력 → 5 반환 확인
      4. STRATEGIES.filter(s => s.applied).length 입력 → 3 반환 확인
    Expected Result: 모든 값이 예상 숫자와 일치
    Evidence: 콘솔 출력 육안 확인
  ```

  **Commit**: YES (T1+T2 묶음)
  - Message: `feat(dashboard): add mock data (agents, tasks, logs, strategies)`
  - Files: `dashboard/index.html`

---

- [ ] 3. 인라인 JS — 렌더러 4개 + init() 추가

  **What to do**:

  `dashboard/index.html`의 `<script>` 블록 하단 (T2 데이터 아래)에 렌더러 함수 4개와 `init()`을 추가한다.

  **renderAgents()**:
  ```js
  function renderAgents() {
    const grid = document.getElementById('agents-grid');
    // AGENTS 배열을 순회하여 .agent-card div 생성
    // 각 카드: .name (에이전트명), span.status-dot + span.status-label
    // status → dot 클래스: idle=dot-idle, running=dot-running, done=dot-done, error=dot-error
    // 하단에 role 텍스트 (color: var(--text-secondary), font-size: 11px)
    grid.innerHTML = AGENTS.map(a => `
      <div class="agent-card">
        <div class="name">${a.name}</div>
        <div style="margin: 6px 0;">
          <span class="status-dot dot-${a.status}"></span>
          <span class="status-label">${a.status}</span>
        </div>
        <div style="color:var(--text-secondary);font-size:11px;">${a.role}</div>
      </div>
    `).join('');
  }
  ```

  **renderTasks()**:
  ```js
  function renderTasks() {
    const tbody = document.getElementById('tasks-tbody');
    // TASKS 배열 → <tr> 생성
    // 컬럼: ID | 제목 | 상태 배지 | 우선순위 배지 | 담당 에이전트
    // 상태 배지: <span class="badge badge-{status}">{status}</span>
    // 우선순위 배지: <span class="badge badge-{priority}">{priority}</span>
    tbody.innerHTML = TASKS.map(t => `
      <tr>
        <td style="color:var(--text-secondary);font-size:11px;">${t.id}</td>
        <td>${t.title}</td>
        <td><span class="badge badge-${t.status}">${t.status.replace('_',' ')}</span></td>
        <td><span class="badge badge-${t.priority}">${t.priority}</span></td>
        <td style="color:var(--text-secondary);">${t.assignee}</td>
      </tr>
    `).join('');
  }
  ```

  **renderLogs()**:
  ```js
  function renderLogs() {
    const list = document.getElementById('logs-list');
    // LOGS 배열 → <li class="log-item"> 생성
    // 3컬럼 그리드: 시간 | 심각도 | 에이전트: 내용
    list.innerHTML = LOGS.map(l => `
      <li class="log-item">
        <span class="log-time">${l.time}</span>
        <span class="log-severity sev-${l.severity}">${l.severity}</span>
        <span><strong>${l.agent}</strong>: ${l.detail}</span>
      </li>
    `).join('');
  }
  ```

  **renderStrategy()**:
  ```js
  function renderStrategy() {
    const grid = document.getElementById('strategy-grid');
    // STRATEGIES 배열 → .strategy-card div 생성
    // applied=true 이면 class에 'active' 추가 + .applied-badge "현재 적용됨" 표시
    grid.innerHTML = STRATEGIES.map(s => `
      <div class="strategy-card ${s.applied ? 'active' : ''}">
        ${s.applied ? '<span class="applied-badge">현재 적용됨</span>' : ''}
        <div class="pattern-name">${s.name}</div>
        <div class="pattern-desc">${s.desc}</div>
      </div>
    `).join('');
  }
  ```

  **init()**:
  ```js
  function init() {
    renderAgents();
    renderTasks();
    renderLogs();
    renderStrategy();
    document.getElementById('last-updated').textContent =
      '마지막 업데이트: ' + new Date().toLocaleTimeString('ko-KR');
  }

  init();
  ```

  HTML에 연결할 ID 목록 (T1에서 만든 section 내부에 반드시 존재해야 함):
  - `id="agents-grid"` — 에이전트 카드 컨테이너
  - `id="tasks-tbody"` — 작업 테이블 tbody
  - `id="logs-list"` — 로그 ul
  - `id="strategy-grid"` — 전략 카드 컨테이너
  - `id="last-updated"` — 푸터 타임스탬프

  **Must NOT do**:
  - HTML 구조 수정 금지 (ID가 없으면 T1로 돌아가서 추가)
  - CSS 수정 금지
  - `setInterval` 자동 새로고침 추가 금지

  **Recommended Agent Profile**:
  - **Category**: `quick`
  - **Skills**: []

  **Parallelization**:
  - **Can Run In Parallel**: NO
  - **Parallel Group**: Sequential Step 3
  - **Blocks**: 없음 (최종 단계)
  - **Blocked By**: T1, T2

  **Acceptance Criteria**:
  - [ ] 브라우저에서 index.html 열면 에이전트 카드 7개가 보임
  - [ ] 작업 테이블에 5행이 보이고 상태 배지가 색상으로 구분됨
  - [ ] 로그 5개가 INFO(파랑)/WARNING(황)/CRITICAL(빨강)으로 구분됨
  - [ ] 전략 카드 4개 중 3개에 "현재 적용됨" 배지가 보임
  - [ ] 전략 카드 "현재 적용됨" 항목 테두리가 버밀리언 레드(`#D4410C`)로 강조됨
  - [ ] 푸터에 타임스탬프가 표시됨

  **QA Scenarios**:
  ```
  Scenario: 전체 렌더링 확인 (Happy Path)
    Tool: 브라우저 직접 열기
    Steps:
      1. dashboard/index.html 더블클릭으로 브라우저에서 열기
      2. 에이전트 섹션: 카드 7개 + 상태 도트 확인
      3. 작업 섹션: 5행 테이블 + 배지 확인 (IN PROGRESS, DONE 등 대문자)
      4. 로그 섹션: 5개 항목 + CRITICAL 항목이 빨간색인지 확인
      5. 전략 섹션: "현재 적용됨" 카드 3개의 테두리가 붉은색인지 확인
      6. 푸터 타임스탬프가 현재 시각과 일치하는지 확인
    Expected Result: 4개 섹션 모두 데이터가 채워져 렌더링됨
    Evidence: 육안 확인

  Scenario: 콘솔 에러 없음 확인 (Failure Check)
    Tool: 브라우저 개발자 도구
    Steps:
      1. F12 → Console 탭 열기
      2. 페이지 새로고침
      3. 빨간색 에러 메시지가 없는지 확인
    Expected Result: 콘솔에 에러 0건
    Evidence: 콘솔 육안 확인
  ```

  **Commit**: YES (전체 완성)
  - Message: `feat(dashboard): complete single-file dashboard with renderers`
  - Files: `dashboard/index.html`

---

## Final Verification Wave

- [ ] F1. **최종 렌더링 검증** — `unspecified-high`
  브라우저에서 `dashboard/index.html`을 열어 4개 섹션 전체를 확인한다. 에이전트 카드 7개, 작업 5개, 로그 5개, 전략 4개가 모두 보이고 콘솔 에러가 없는지 검증한다.
  Output: `Sections [4/4] | Agents [7/7] | Tasks [5/5] | Console Errors [0] | VERDICT: APPROVE/REJECT`

- [ ] F2. **디자인 가이드라인 준수** — `unspecified-high`
  `dashboard/index.html` 소스를 검토한다. `DM Serif Display`와 `IBM Plex Mono` 사용 여부, `border-radius` 남용 없음, `Inter`/`Roboto` 미사용, 버밀리언 레드 액센트 적용 여부를 확인한다.
  Output: `Fonts [PASS/FAIL] | No-radius [PASS/FAIL] | Accent-color [PASS/FAIL] | VERDICT`

---

## Commit Strategy

- **T1**: `feat(dashboard): HTML skeleton + inline CSS design system`
- **T2**: `feat(dashboard): add mock data`
- **T3**: `feat(dashboard): complete renderers and init`

---

## Success Criteria

### Verification Commands
```bash
# 브라우저에서 직접 열기 (Windows)
start dashboard/index.html

# 또는 Mac/Linux
open dashboard/index.html
```

### Final Checklist
- [ ] `dashboard/index.html` 단일 파일로 완성
- [ ] 외부 JS 라이브러리 의존 없음
- [ ] `DM Serif Display` + `IBM Plex Mono` 적용됨
- [ ] 4개 섹션 모두 Mock 데이터로 렌더링됨
- [ ] 콘솔 에러 0건
