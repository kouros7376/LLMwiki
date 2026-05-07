# kouwiki — 전체 목차

300명 규모 건축설계사무소 전산실 총괄의 개인 지식 위키. 원본 자료는 `raw/` 에, 정리된 지식은 `wiki/` 에 둔다.
운영 규칙은 [[CLAUDE]] 참고. 작업 이력은 [[log]].

## 카테고리

### ERP / 마이그레이션
통합 ERP 마이그레이션 업계 정보, 벤더 비교, 도입 의사결정 포인트.
- [[더존_아마란스10]] — 통합형 플랫폼 (ERP+그룹웨어+ONE AI)
- [[영림원_K-System_Ace]] — 산업특화 + 외부 연동 강점, 프로젝트 모듈
- [[ERP_벤더_비교]] — 더존 vs 영림원 항목별 비교
- [[건설_PMS]] — 건설/건축 PMS·PMIS 일반론, 대림산업 사례
- [[ERP_RFP_체크리스트]] — 도입 시 검증 항목 25개

### AI 바이브 코딩
바이브 코딩 워크플로우, Claude 활용 패턴, LLM Wiki 개념.
- [[Claude_Code]] — 본 위키 운영 환경. MCP 6개 연동
- [[바이브_코딩_도구]] — Claude Code · Cursor · Copilot · Windsurf · Antigravity · Kiro 6강 비교
- [[LLM_Wiki_개념]] — Karpathy 원전, 3 레이어 + 3 모드, 한국·해외 블로그 사례 3건 + Karpathy 본인 100페이지·40만단어 사례
- [[LLM_Wiki_운영_패턴]] — 1인 → 5~10명 admin → 전사 확장의 8가지 운영 패턴 + 사례 10건 (2026-05-06 추가)
- [[LLM_Wiki_웹구현_사례]] — Abraham Song 의 셀프호스트 웹앱(wiki.ai-biz.app). LLM Wiki + Graphify 통합 실증
- [[지식의_복리]] — RAG vs Wiki 메타 명제. WikiChat 환각 -55% / Graphify 토큰 -99% 정량 증거
- [[Graphify]] — Karpathy 직계 자동 그래프 도구. GraphRAG/LightRAG 와의 위치 비교
- [[Graphify_심층_가이드]] — Graphify의 핵심 개념, 동작 원리, 활용 시나리오 상세 분석
- [[Graphify]] — 멀티모달 코퍼스를 질의 가능한 지식 그래프로 변환하는 AI 코딩 어시스턴트
- [[Graphify_종합_가이드]] — 멀티모달 코드베이스를 위한 AI 코딩 어시스턴트 지식 그래프

### 사내 LLM 시스템 (신설, 2026-05-06)
사내 챗봇 + 위키 통합 시스템 구축 가이드. 기존 [[자동화_시나리오|시나리오 6]] 의 인프라 정리.
- [[사내_챗봇_시스템]] — 300명 사무소용 RAG·LLM 백엔드·인증·운영 + 한국 사례 9건
- [[Wiki_Backed_Chatbot]] — Wiki + 챗봇 4가지 통합 패턴 + 코드 골격 + 5단계 도입 로드맵
- [[한국어_LLM_스택]] — Kiwi · KURE-v1 · Solar Pro 2 · Kordoc · 80/15/5 라우팅

### 전산업무 자동화
전산실 운영/업무 자동화 도구 + 시나리오.
- [[워크플로우_자동화_도구]] — n8n · Power Automate · Make · Zapier · Retool
- [[자동화_시나리오]] — 5개 사내 적용 시나리오 + 시나리오 6 (사내 LLM 위키 챗봇) + 도구 분기 매트릭스
- [[RPA]] — Robotic Process Automation. 본 사무소 도입 타당성 + 국내 7개·해외 3대 벤더 비교 + 3-tier 권장 아키텍처 (2026-05-06 추가)

### 건축 IT / AEC
건축설계사무소 사내 개발·디지털 전환 레퍼런스.
- [[해외_AEC_사내개발]] — Foster + Partners (Hydra/Hermes 등 6개 사내 도구), Gensler NFORM
- [[정림건축_혁신본부]] — Junglim Innovation + JAdU. 한국 1위 사례
- [[AEC_자동화_플랫폼]] — Speckle · Hypar · Augmenta + NXT BLD 2026 트렌드
- [[건축IT_사내개발_전략]] — 300명 규모 사무소용 3단계 로드맵
- [[사내_인쇄_복합기_가이드]] — 5층 복합기 인쇄·스캔 사용법

### 메타 / 인사이트 (cross-cutting)
서로 다른 도메인을 가로지르는 패턴. ERP/PMS/LLM/AEC 가 같은 구조를 공유한다는 통찰.
- [[정보_일원화_패턴]] — 손익 일원화 = LLM Wiki 누적 = 단일 플랫폼 = BIM 단일정보원
- [[통합형_vs_연동형]] — 더존:영림원 = ChatGPT Workspace:Claude+MCP = Construction Cloud:Speckle
- [[건강검진]] — 위키 Lint = ERP 정합성 = PMS 마감 점검 = 자동화 모니터링
- [[워처_Race_가드_테스트]] — ingest 엔진의 중복 처리 방지 메커니즘

## 통계

- 총 페이지: 25 (index, log 제외)
- 마지막 업데이트: 2026-05-06
- 카테고리: 6 (사내 LLM 시스템 신설)
- 입수 raw 자료: 18 (ERP/PMS 4 + LLM Wiki 블로그 2 + AI/자동화/AEC 4 + Graphify 1 + 심화 2 + 딥리서치 3 + RPA 2)

## 사용법 메모

- **자료 추가**: `raw/` 에 파일을 넣고 Claude 에게 "자료 넣었어" 또는 파일명 전달
- **질문**: 그냥 자연어로. Claude 가 `[[위키링크]]` 로 근거 페이지 인용
- **건강검진**: "건강검진 해줘" → [[건강검진]] 절차에 따라 깨진 링크 / 중복 / 스텁 / 고립 노드 보고

## 핵심 통찰 빠른 진입로

처음 위키를 보는 사람을 위한 추천 동선:
1. [[지식의_복리]] — 왜 이 위키인가
2. [[정보_일원화_패턴]] — 본 위키의 핵심 통찰 (LLM Wiki ↔ ERP ↔ BIM 동형성)
3. [[통합형_vs_연동형]] — 모든 도메인의 1차 분기점
4. **본인 업무 진입로 선택**:
   - ERP 의사결정 → [[ERP_벤더_비교]]
   - 사내 개발 시작 → [[건축IT_사내개발_전략]]
   - 자동화 도입 → [[자동화_시나리오]]
   - 코드 작업 도구 → [[Claude_Code]] / [[바이브_코딩_도구]]
