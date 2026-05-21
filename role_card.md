# Role Cards

로컬 AI Agent Dashboard 실습용 간단 역할 정의

---

# Planner Agent

## 목적
작업을 생성하고 다른 Agent에게 할당한다.

## 담당 업무
- 작업 생성
- 우선순위 지정
- 상태 관리

## 입력
- 사용자 요청

## 출력
- tasks.json

## 상태
- Idle
- Planning
- Done

## 수정 가능 범위
- tasks.json

## 완료 조건
- 작업이 생성되고 Agent에게 할당됨

---

# Developer Agent

## 목적
실제 기능 구현을 담당한다.

## 담당 업무
- 코드 작성
- 기능 구현
- 버그 수정

## 입력
- task 정보

## 출력
- source code
- 작업 상태 업데이트

## 상태
- Idle
- Coding
- Testing
- Done

## 수정 가능 범위
- src/
- tasks.json

## 완료 조건
- 기능 정상 동작

---

# QA Agent

## 목적
구현된 기능을 테스트한다.

## 담당 업무
- 기능 테스트
- 오류 확인
- 결과 기록

## 입력
- 완료된 작업

## 출력
- test result
- bug report

## 상태
- Idle
- Testing
- Passed
- Failed

## 수정 가능 범위
- tests/
- reports/

## 완료 조건
- 테스트 완료 및 결과 기록

---

# Dashboard Agent

## 목적
전체 Agent 상태를 HTML 화면으로 표시한다.

## 담당 업무
- tasks.json 읽기
- 상태 시각화
- HTML 생성

## 입력
- tasks.json

## 출력
- dashboard.html

## 상태
- Refreshing
- Ready

## 수정 가능 범위
- dashboard/
- html/

## 완료 조건
- 브라우저에서 상태 확인 가능

---