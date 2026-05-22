---
title: 모임 참석 확인 서비스 구현 및 테스트 계획
type: implementation-plan
status: draft
---

# 모임 참석 확인 서비스 구현 및 테스트 계획

## 목적
AI 해봄 모임 전에 회원이 공개 HTML 페이지에서 참석 여부, 음료 선택, 요청사항을 제출하게 한다.

운영진은 대시보드에서 응답을 확인하고, 최종 결과를 Markdown 파일로 내려받아 Obsidian이나 위키에 기록한다.

## 현재 상태
현재 `operations/attendance-check/index.html`은 화면 시안과 로컬 저장 데모다.
루트의 `meeting-attendance-check.html`은 기존 링크 호환을 위해 당분간 유지한다.

- GitHub Pages에서 열린다.
- 응답은 각 회원의 브라우저 `localStorage`에만 저장된다.
- 여러 회원의 응답이 한곳에 모이지 않는다.
- 대시보드와 Markdown 내보내기 흐름은 확인할 수 있다.

## 목표 구조

```text
회원
-> GitHub Pages HTML
-> Cloudflare Worker API
-> Cloudflare Workers KV
-> 운영진 대시보드
-> Markdown 다운로드
-> Obsidian 또는 wiki 기록
```

GitHub Pages는 정적 HTML을 제공한다.
실제 응답 저장 서버는 Cloudflare Worker가 맡는다.

공식 문서 기준으로 GitHub Pages는 HTML, CSS, JavaScript 같은 정적 파일을 게시하는 서비스다.
따라서 자체 데이터 저장 서버로 쓰지 않는다.

Cloudflare Worker는 HTTP 요청을 받는 `fetch` 핸들러로 API를 만들 수 있다.
Workers KV는 Worker에서 읽고 쓰는 키-값 저장소로 쓴다.

## 왜 이 방식인가
- GitHub Pages는 계속 사용할 수 있다.
- 별도 서버 PC를 켜 둘 필요가 없다.
- Google Sheet를 쓰지 않는다.
- 회원은 GitHub 계정 없이 제출할 수 있다.
- 운영진은 HTML 대시보드에서 바로 집계와 Markdown 내보내기를 확인한다.
- 나중에 D1, Notion, 사내 서버로 바꾸기 쉽다.

## 1차 범위

### 포함
- 회원용 공개 HTML 제출 화면
- 응답 저장 API
- 운영진용 대시보드
- 참석, 불참, 미정 집계
- 음료별 수량 집계
- 요청사항 목록
- 응답자 목록
- Markdown 다운로드
- 관리자 PIN으로 대시보드 보호

### 제외
- 카카오톡 자동 발송
- 회원 로그인
- 결제 또는 실제 음료 주문
- 개인정보가 많은 회원 DB
- 장기 보관용 정식 데이터베이스 설계

## 데이터 항목

| 항목 | 필수 | 설명 |
|---|---:|---|
| meetingId | 예 | 모임 식별자. 예: `2026-06-01-regular` |
| meetingTitle | 예 | 모임명 |
| meetingDate | 예 | 모임일 |
| memberName | 예 | 이름 |
| memberTeam | 아니오 | 부서 또는 소속 |
| attendance | 예 | 참석, 불참, 미정 |
| drink | 예 | 음료 선택 |
| request | 아니오 | 요청사항 |
| submittedAt | 예 | 제출 시각 |

수집 항목은 최소화한다.
전화번호, 주민등록번호, 사번, 계좌번호는 받지 않는다.

## API 초안

### `POST /responses`
회원 응답을 저장한다.

요청 예시:

```json
{
  "meetingId": "2026-06-01-regular",
  "meetingTitle": "AI 해봄 정기모임",
  "meetingDate": "2026-06-01",
  "memberName": "홍길동",
  "memberTeam": "운영지원팀",
  "attendance": "참석",
  "drink": "아이스 아메리카노",
  "request": "설치 부분을 천천히 보고 싶습니다."
}
```

응답 예시:

```json
{
  "ok": true,
  "id": "response-id"
}
```

### `GET /summary?meetingId=...`
운영진 대시보드용 집계 데이터를 가져온다.

요청 헤더:

```text
X-Admin-Pin: 운영진_PIN
```

응답 예시:

```json
{
  "total": 12,
  "attendance": {
    "참석": 9,
    "불참": 2,
    "미정": 1
  },
  "drinks": {
    "아이스 아메리카노": 5,
    "아이스 라떼": 3,
    "차": 1
  },
  "requests": [
    {
      "memberName": "홍길동",
      "request": "설치 부분을 천천히 보고 싶습니다."
    }
  ],
  "responses": []
}
```

### `GET /markdown?meetingId=...`
운영진용 Markdown 기록을 가져온다.

요청 헤더:

```text
X-Admin-Pin: 운영진_PIN
```

응답은 `text/markdown` 형식으로 받는다.

## 저장 방식
Workers KV에는 응답 1건을 키 1개로 저장한다.

키 예시:

```text
meeting:2026-06-01-regular:response:01J...
```

같은 회원이 다시 제출하면 최신 응답으로 덮어쓴다.
이를 위해 별도 인덱스 키를 둔다.

```text
meeting:2026-06-01-regular:member:홍길동
```

주의할 점:
- KV는 읽기가 빠르고 단순하다.
- 같은 키에 너무 자주 쓰는 구조는 피한다.
- 응답이 많아지고 조회 조건이 복잡해지면 D1로 옮긴다.

## 화면 구성

### 회원 화면
- 모임명
- 모임일
- 이름
- 부서 또는 소속
- 참석 여부
- 음료 선택
- 요청사항
- 제출 버튼
- 제출 완료 화면

### 운영진 대시보드
- 전체 응답 수
- 참석, 불참, 미정 수
- 음료별 수량
- 요청사항 목록
- 응답자 목록
- Markdown 다운로드
- 응답 초기화 버튼은 1차 버전에서 제외

## 구현 순서

### 1단계: API 설계 고정
- 데이터 항목을 확정한다.
- 응답 저장 규칙을 정한다.
- 관리자 PIN 방식으로 대시보드를 보호한다.

완료 기준:
- API 요청과 응답 예시가 문서에 정리되어 있다.

### 2단계: Cloudflare Worker 초안 작성
- `POST /responses`를 만든다.
- `GET /summary`를 만든다.
- `GET /markdown`을 만든다.
- CORS를 GitHub Pages 도메인 기준으로 허용한다.

완료 기준:
- `curl` 또는 브라우저에서 응답 저장과 조회가 된다.

### 3단계: KV 연결
- KV namespace를 만든다.
- Worker에 KV binding을 연결한다.
- 응답 1건을 KV에 저장한다.
- meetingId 기준으로 응답 목록을 읽는다.

완료 기준:
- Worker를 재시작해도 응답이 남아 있다.

### 4단계: HTML 연결
- 기존 `operations/attendance-check/index.html`의 `localStorage` 저장을 API 저장으로 바꾼다.
- 루트 호환 파일인 `meeting-attendance-check.html`도 같은 내용으로 맞춘다.
- 운영진 대시보드는 `/summary`를 읽는다.
- Markdown 다운로드는 `/markdown` 결과를 사용한다.

완료 기준:
- GitHub Pages에서 제출한 응답이 Worker에 저장된다.
- 다른 브라우저에서 대시보드를 열어도 같은 집계가 보인다.

### 5단계: 공개 테스트
- 테스트용 모임 ID를 만든다.
- 운영진 1명이 제출한다.
- 다른 브라우저 또는 휴대폰에서 제출한다.
- 대시보드 집계와 Markdown을 확인한다.

완료 기준:
- 여러 기기에서 제출한 응답이 한 대시보드에 모인다.
- Markdown 파일을 내려받아 Obsidian에 넣을 수 있다.

## 테스트 체크리스트

### 회원 제출
- [ ] 이름 없이 제출하면 안내가 나온다.
- [ ] 참석, 불참, 미정이 각각 저장된다.
- [ ] 기타 음료가 저장된다.
- [ ] 요청사항이 없어도 제출된다.
- [ ] 같은 이름이 다시 제출하면 최신 응답으로 바뀐다.

### 대시보드
- [ ] 관리자 PIN이 없으면 조회되지 않는다.
- [ ] 참석 수가 맞다.
- [ ] 음료 수량이 맞다.
- [ ] 요청사항 목록이 맞다.
- [ ] Markdown 다운로드가 된다.

### 보안과 운영
- [ ] GitHub Pages 도메인에서만 API 호출을 허용한다.
- [ ] 관리자 PIN은 HTML에 넣지 않는다.
- [ ] 개인정보 최소 수집 문구가 화면에 있다.
- [ ] 테스트 응답을 지우는 절차가 있다.

## 위험과 대응

### 회사 정책상 Cloudflare 사용이 어려울 수 있음
대응:
- 먼저 테스트용으로만 사용한다.
- 실제 운영 전 저장 위치 승인을 확인한다.
- 안 되면 사내 서버 방식으로 바꾼다.

### 공개 API에 장난 응답이 들어올 수 있음
대응:
- 1차는 모임별 짧은 코드 또는 제출용 키를 둔다.
- 운영진 대시보드에서 이상 응답을 제외할 수 있게 한다.

### 관리자 PIN이 노출될 수 있음
대응:
- PIN은 Worker 환경변수에 둔다.
- HTML 파일에는 넣지 않는다.
- PIN이 노출되면 즉시 바꾼다.

### 장기 운영에는 KV가 불편할 수 있음
대응:
- 1차는 KV로 단순하게 만든다.
- 응답이 많아지면 D1로 옮긴다.

## 파일 변경 예상

Private:

```text
Output/final/meeting-attendance-check.html
Output/final/meeting-attendance-service-plan.md
Output/final/operations/attendance-check/index.html
Output/final/operations/attendance-check/implementation-plan.md
Output/final/share-index.html
Output/final/share-guide-notion-final.md
wiki/index.md
wiki/log.md
```

Public:

```text
docs/meeting-attendance-check.html
docs/meeting-attendance-service-plan.md
docs/operations/attendance-check/index.html
docs/operations/attendance-check/implementation-plan.md
docs/index.html
docs/share-index.html
docs/share-guide-notion-final.md
```

새 API 코드를 저장할 위치는 구현 시 정한다.
후보는 `tools/attendance-worker/` 또는 별도 저장소다.

## 첫 테스트 시나리오

1. 테스트 모임을 만든다.
   - meetingId: `test-regular-meeting`
   - meetingTitle: `AI 해봄 테스트 모임`
2. 운영진이 PC에서 응답 1건을 제출한다.
3. 휴대폰 브라우저에서 응답 1건을 제출한다.
4. 운영진 대시보드에서 2건이 보이는지 확인한다.
5. Markdown을 다운로드한다.
6. Obsidian에 넣어 기록이 읽기 좋은지 확인한다.

## 다음 결정
구현 전에 아래 3가지를 정한다.

1. Cloudflare Workers를 테스트용으로 써도 되는가?
2. 관리자 PIN 방식으로 1차 대시보드를 보호해도 되는가?
3. 새 API 코드를 현재 private 저장소에 둘지, public 저장소에 둘지 정한다.

추천은 private 저장소에 API 코드를 두고, 공개 저장소에는 HTML만 두는 방식이다.

## 참고 공식 문서
- GitHub Pages는 정적 파일을 게시하는 서비스다: https://docs.github.com/articles/user-organization-and-project-pages
- Cloudflare Worker는 HTTP 요청을 받는 `fetch` 핸들러로 동작한다: https://developers.cloudflare.com/workers/runtime-apis/fetch/
- Workers KV는 Worker에서 키-값 데이터를 읽고 쓸 수 있다: https://developers.cloudflare.com/kv/
- KV 쓰기는 `put()`으로 처리한다: https://developers.cloudflare.com/kv/api/write-key-value-pairs/
