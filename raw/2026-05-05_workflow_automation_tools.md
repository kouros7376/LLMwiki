# 워크플로우 자동화 도구 — 전산실 업무 자동화 레퍼런스

수집일: 2026-05-05
출처:
- <https://invaiz.com/blog/2025-automation-tools-summary-workflow-automation-part-3>
- <https://tech.hancom.com/workflow-n8n-service/>
- <https://www.magicaiprompts.com/docs/automation/n8n-usage-guide/>
- <https://www.alchera.ai/resource/blog/n8n>
- <https://insight.infograb.net/blog/2024/07/31/workflow-n8n/> (인포그랩)
- <https://upsd.co.kr/한-번-설정하면-손이-거의-안-간다-n8n-chatgpt-자동화의-현실/>
- <https://blog.hops.pub/en/ai-automation-examples>

## 한 줄 요약
전산실 반복업무 자동화 도구 3축: **n8n(오픈소스 셀프호스트)** / **Power Automate(MS 생태계)** / **Make·Zapier(SaaS 노코드)**. 각 도구의 강점이 명확히 다르므로 **업무 성격 + 데이터 정책** 기준으로 선택.

## 도구별 비교

### 1. n8n (오픈소스 워크플로우 엔진)
- **포지션**: 오픈소스(Fair-code 라이선스), 셀프호스트 가능, 노코드+로우코드 혼합
- **강점**:
  - **셀프호스트** → 사내 망에서 운영, 데이터 외부 유출 없음 (GDPR 등 엄격 환경 적합)
  - 400+ 통합 노드 (Slack, Notion, GitHub, OpenAI, Postgres, Google Workspace 등)
  - 커스텀 JS/Python 노드 작성 가능 → **레거시 시스템·ERP·내부 DB 연동** 자유
  - 로컬 모델 LLM(Ollama 등) 연결 가능
- **약점**: 셀프호스트 운영 부담 (서버, 백업, 모니터링), 학습 곡선 있음
- **가격**: 셀프호스트 무료, 클라우드 $24/월~
- **추천**: **사내 데이터 정책이 엄격한 건축설계사무소 IT 담당자에게 1순위**
- **대표 시나리오**:
  - 메일 도착 → 분류 → Notion 업무 등록
  - 일정 시간마다 Notion 페이지 검색 → OpenAI 리뷰 → Slack 알림
  - 유튜브 영상 요약 → 사내 메신저 전달
  - GPT 키워드 모니터링 → 일일 리포트

### 2. Microsoft Power Automate
- **포지션**: MS 365 생태계 통합형 RPA + 워크플로우
- **강점**:
  - **Office 365 / Dynamics 365 / SharePoint** 와 깊은 통합
  - Desktop Flow(RPA) — UI 자동화로 레거시 시스템 조작
  - AI Builder — 문서에서 데이터 추출 (송장, 계약서 등)
  - Copilot 통합 (자연어로 플로우 생성)
- **약점**: MS 외 시스템과는 connector 추가 비용, 라이선스 복잡
- **가격**: 사용자당 $15/월 (Premium), Process plan $150/월~
- **추천**: **MS 365 위주의 사무환경 + RPA 필요 시** (스캔 문서 처리, 레거시 ERP UI 자동화)

### 3. Zapier
- **포지션**: SaaS 통합의 표준, 5,000+ 앱 지원
- **강점**: 가장 많은 앱 지원, 설정 직관적, 비개발자 친화
- **약점**: 비싼 편(작업당 과금), 복잡한 로직 어려움, 셀프호스트 불가
- **가격**: $19.99/월~, 트랜잭션 기반
- **추천**: SaaS 위주 환경, 빠른 시작, 비개발자 직원 자동화

### 4. Make (구 Integromat)
- **포지션**: 시각적 시나리오, Zapier 대안
- **강점**: 시각적 UI 직관적, Zapier 대비 저렴, 분기·반복 표현 쉬움
- **약점**: 셀프호스트 불가, 한국어 자료 적음
- **가격**: 무료 1,000 ops/월~, $10.59/월~
- **추천**: Zapier 대비 비용·시각적 UI 선호 시

### 5. Retool
- **포지션**: 사내 도구 빠른 구축 (워크플로우 + 내부 admin UI)
- **강점**: DB 직접 연결 → 빠르게 사내 admin 화면 만들기, 워크플로우 + UI 통합
- **약점**: 워크플로우 단독 비교에서는 n8n/Make가 강함
- **가격**: 무료 5명까지, $10/사용자~
- **추천**: 사내 데이터 조회/수정 UI 가 함께 필요할 때

## 도입 시나리오 — 건축설계사무소 IT 담당자 관점

### 시나리오 1: 인허가 서류 처리 자동화
- **n8n** + Power Automate(Desktop Flow) 조합
- 메일 첨부 PDF → AI Builder/Claude API로 추출 → Excel/DB 정리 → 결재 요청

### 시나리오 2: 프로젝트 진행 일일 보고서
- **n8n** Schedule Trigger → 사내 PMS DB 조회 → ChatGPT 요약 → 메신저(Teams/Slack) 전송
- → [[건설_PMS]] 의 일원화 데이터를 활용

### 시나리오 3: 직원 온보딩
- **Power Automate** + AD 통합
- 입사 등록 → AD 계정 생성 → 라이선스 할당 → 환영 메일 + 첫 주 일정 자동 등록

### 시나리오 4: 외주 협력사 데이터 수집
- **n8n** 외부 폼 (Google Forms / Tally) → DB 적재 → 누락 알림 → [[건설_PMS]] 협력사 미입력 함정 완화

### 시나리오 5: 회의록 자동화 + 위키 ingest
- 회의 녹음 → Whisper 전사 → Claude 요약 → **n8n** → 위키(Obsidian/Notion) 자동 등록
- → [[지식의_복리]] 의 조직 적용

## 본 위키 환경과의 연결

본 kouwiki 가 **수동 ingest 모델**(사용자가 raw/ 에 자료 넣음)이지만, 위 시나리오 5처럼 n8n + Claude API 로 **자동 ingest 파이프라인** 구축 가능:
- 메일/슬랙/Notion 새 글 → n8n 트리거 → Claude API 정리 → kouwiki repo 에 자동 push

이 경우 [[통합형_vs_연동형]] 의 연동형 패턴이 자동화 영역으로 확장됨.

## 도입 권장 — 단계적 접근

```
1단계 (1~2개월): n8n 셀프호스트 1대 구축, 시나리오 1~2개로 PoC
       └─ 권장 시나리오: 일일 보고서 자동화 (효과 즉시 체감, 리스크 낮음)

2단계 (3~6개월): 효과 검증된 워크플로우 5~10개 운영, 사용자 교육
       └─ Power Automate 병행 검토 (RPA 필요 영역 식별)

3단계 (6개월~): 사내 자동화 플랫폼 표준화, 신규 요구사항 → 자동 검토
       └─ 워크플로우 카탈로그·문서·온보딩 정비
```

## 도구 선택 매트릭스

| 조건 | 우선 도구 |
|------|----------|
| 데이터 외부 송출 금지 | **n8n (셀프호스트)** |
| MS 365 중심 환경 + RPA | Power Automate |
| 비개발자 직원도 만들고 싶음 | Zapier / Make |
| 사내 admin UI 도 필요 | Retool |
| AI(LLM) 워크플로우 깊이 활용 | n8n + 외부 LLM API |

## 흔한 함정
1. **자동화 자체가 목적이 됨** — "월 100개 작업 정도의 간단한 자동화로 시작" 권장
2. **에러 처리 부재** — 한 번 깨지면 누구도 모름 → 모니터링·알림 필수
3. **권한·시크릿 관리 허술** — n8n credentials 백업 분리 필수
4. **셀프호스트 운영 인력 미배정** — n8n 도입 시 운영 SRE 시간 산정 필요
