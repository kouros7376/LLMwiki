# LLM Wiki 개념

Andrej Karpathy 가 2026년 4월 초 트위터/Gist 로 제안한 운영 패턴. **LLM 에이전트가 같은 자료를 매번 새로 읽지 않고, 위키 형태로 정리해 둔 지식 위에 누적적으로 답하게 만드는 것**. 본 kouwiki 자체가 이 패턴의 구현.

## 문제 정의 — RAG 의 한계
- RAG(Retrieval-Augmented Generation): 질문 들어오면 관련 문서를 검색해 LLM 에 붙임
- 강력하지만 **지식이 "쌓이지" 않는다** — 어제 찾은 것도 오늘 또 검색
- 매 질의마다 같은 자리에서 새로 시작 → 시간 보상 없음

## 해법 — Wiki
> "위키는 복리로 쌓이는 자산이다. RAG처럼 매번 재발견하는 게 아니라, 한 번 이해한 것 위에 다음 이해가 쌓인다." — Karpathy

→ 자세한 본질은 [[지식의_복리]].

## 3 레이어 구조

| 레이어 | 역할 |
|--------|------|
| **Raw Sources** | 원본 자료. URL·PDF·마크다운. 절대 건드리지 않음. → 본 위키의 [[CLAUDE\|raw/]] |
| **Wiki** | LLM 이 직접 쓰고 유지하는 마크다운 페이지들. 지식이 누적됨. → 본 위키의 wiki/ |
| **Schema** | 위키 운영 규칙을 LLM 에게 알려주는 설정 문서. → 본 위키의 [[CLAUDE]] |

## 3 동작 모드

### 1. Ingest
소스가 들어오면 관련 페이지를 만들거나 기존 페이지에 통합. **"새 파일 추가"가 아니라 "기존 지식에 흡수"**. 중복 시 통합, 출처 명시.

### 2. Query
마스터 인덱스를 먼저 읽고, 관련 페이지를 종합해 답변. 위키에 없는 건 없다고 명시. **추측 금지**.

### 3. Lint
고아 페이지·끊어진 링크·오래된 페이지를 주기적으로 잡음. 위키가 쌓이면서 썩지 않게.

→ kouwiki 의 "자료넣기 / 질문하기 / [[건강검진]]" 이 정확히 이 3 모드.

## "컴파일된 지식" 비유
런타임마다 다시 해석하는 게 아니라, **미리 한 번 정리해 두는 것**. 책을 읽을 때마다 처음부터 시작하는 게 아니라 이전에 밑줄 친 것 위에 새 메모를 추가하는 것과 같음. → [[지식의_복리]]

## 한국 블로그 사례

### 사례 1 — "AI에게 기억력을 심는 방법" (오시잉, My Tiny Life Recorder)
- 동기: Power Automate 플로우 설계 세션 후 다음 날 에이전트가 "처음 만난 사람처럼" 답함
- 구현: `llm_wiki/` 폴더 안에 `sources/`, `wiki/`, `schema/`, `output/` 분리
- 자연어 명령으로 운영: "이 URL wiki에 추가해줘", "wiki에서 Power Automate 알려줘", "wiki 점검해줘"

### 사례 2 — "LLM Wiki 활용해보기" (여름겨울)
- 적용: AI 거장들의 인터뷰/대담 모음(@Alisvolatprop12 정리본)을 ingest
- 셋업: "그냥 llm wiki claude한테 주고 환경 셋업 해달라고 하면 된다"
- 결과: 옵시디언 그래프뷰로 자동 연결 시각화. "그들의 생각과 방향성을 대답해주는 나만의 LLM"
- 회사 적용 아이디어: **Jira issue 다 ingest 후 신규 issue 발생 시 Claude 에게 던지기** ← 의외의 연결: ERP 마이그레이션의 **레거시 데이터 ingest** 와 정확히 같은 패턴

### 사례 3 — "흩어진 메모에서 살아있는 지식 그래프로" (Abraham Song, Brunch)
- 동기: Visa 엔지니어링 그룹 지식관리자 출신이지만 본인 지식이 Google Drive · Gmail · OneNote · Apple Notes · Slack · Medium · Brunch 에 분산. 옵시디언 로컬 구현 후 sync 구독료 회피 위해 셀프호스트 채택
- 구현: Hetzner CX23 리눅스 VPS (월 ~5달러) + FastAPI 백엔드 + Vanilla JS SPA + D3.js 포스 시뮬레이션 + Telegram 봇 + Hermes 에이전트. [wiki.ai-biz.app](https://wiki.ai-biz.app) 에서 운영
- 차별점: [[Graphify]] 통합으로 페이지 하단에 **자동 [[교차참조]] 주입** (EXTRACTED/INFERRED/AMBIGUOUS 라벨 + 신뢰도 점수). 본 위키와 달리 인간 큐레이션 단계 없이 그래프 결과가 표면화
- 핵심 메타: **"사서(librarian)" 비유** — LLM 을 검색 엔진이 아닌 사서로 취급. 사서는 묻기 전에 스스로 정리·연결·패턴 발견. → [[지식의_복리]] 의 "컴파일된 지식" 비유의 시스템 관점 짝
- 실증 데이터: OAuth2 ↔ PaymentGateway 가 공유 유틸 함수로 연결되어 있음을 Graphify 가 자동 발견 (수동 위키링크에 절대 안 잡힘)
- 자세한 분석은 [[LLM_Wiki_웹구현_사례]] 별도 페이지

→ 사례 1·2 가 옵시디언 로컬에 머문다면, 사례 3 은 **같은 패턴의 셀프호스트 웹 구현** — 본 위키가 사내 공유 단계로 진화할 경우 직접 참고할 청사진.

## 알려진 한계
- **토큰 소비** — Ingest 시 토큰 많이 씀. 모델 최적화 부재 시 비용 부담
- **환각 방지** — 주기적 lint 필수. [[건강검진]] 으로 데이터 무결성 유지
- **고립 노드** — 어떤 페이지에서도 링크되지 않는 페이지가 생김. 본 위키도 [[CLAUDE\|운영규칙]] 으로 "고립 노드 금지" 명시

## 본 위키에서의 적용
- 본 kouwiki 의 [[CLAUDE]] 가 곧 Schema
- raw/ 가 Sources, wiki/ 가 Wiki
- [[index]] 가 마스터 인덱스
- [[log]] 가 작업 이력
- 본 페이지 자체가 Karpathy 패턴의 자기참조적 정리

## Karpathy 본인 사례 — 100 페이지 / 40만 단어

2026년 4월 GitHub Gist 공개 후 16M views. 운영 방식: 한쪽에 Claude Code/Cursor, 다른 쪽에 Obsidian. 새 소스 들어오면 LLM 이 위키 전체 업데이트, 본인은 읽기·질문·방향 제시만. **단일 연구 주제 위키가 100 articles · 400,000 words** (PhD 논문보다 길음). 본인이 직접 쓴 부분 없음. **"Obsidian = IDE, LLM = programmer, wiki = codebase"** 메타포가 본 위키 [[Wiki_Backed_Chatbot|챗봇 통합]] 아키텍처의 출발점.

→ 본 사무소 운영 확장 시 Karpathy 의 1인 모델을 그대로 쓸 수 없음 (권한·동시편집·드리프트 governance 미검증). 5~10명 admin 큐레이션 모델로 확장 시 발생하는 8가지 운영 패턴은 [[LLM_Wiki_운영_패턴]] 별도 페이지에 정리.

## 의외의 도메인 연결 — ERP 마이그레이션

[[건설_PMS]] 에서의 대림산업 사례 — "회계 손익 ↔ 공사 손익 일원화" 가 본질적으로 LLM Wiki 와 같은 패턴이라는 것이 본 위키의 핵심 통찰. 둘 다 **흩어진 정보를 한 자리에 누적해 매번 재발견하지 않게 만드는 시도**. → [[정보_일원화_패턴]]

## 원전·출처
- Karpathy LLM Wiki Gist: <https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f>
- 한국 블로그 1: <https://blog.naver.com/oh_seeing/224260358976>
- 한국 블로그 2: <https://blog.naver.com/summer_winter_nh/224247633516>
- raw/LLM Wiki AI에게 기억력을 심는 방.. 네이버블로그.md
- raw/LLM Wiki 활용해보기 네이버 블로그.md
- 수집일: 2026-05-05

## 관련
- [[지식의_복리]] — 본 패턴의 메타 명제
- [[정보_일원화_패턴]] — ERP/PMS 도메인에서 같은 패턴
- [[CLAUDE]] — 본 위키의 Schema 구현
- [[index]] — 본 위키의 마스터 인덱스
- [[더존_아마란스10]] — ONE AI 가 풀려는 같은 문제의 통합형 접근
- [[통합형_vs_연동형]] — LLM Wiki+MCP 의 위치
