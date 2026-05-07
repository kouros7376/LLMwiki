# 작업 이력

위키에 가해진 변경 이력을 시간순으로 기록한다. (최신이 위)

| 날짜 | 작업 | raw 자료 | 갱신/생성된 페이지 |
|------|------|----------|--------------------|
| 2024-05-15 | 신규 생성 | raw/Graphify 복잡한 코드베이스를 한눈에 - AI 코딩 어시스턴트를 위한 지식 그래프(Knowledge Graph) 도구 1.md | wiki/Graphify_종합_가이드.md | markdown
| 2026-05-06 | 신규 페이지 생성 | raw/graphify — 멀티모달 코퍼스를 질의 가능한 지식 그래프로 바꾸는 AI 코딩 어시스턴트 스킬.md | [[Graphify]] | JSON |
| 2026-05-07 | 신규 생성 | raw/Graphify 카파시의 위키 구현 버전.md | [[Graphify_심층_가이드]] | wiki/Graphify_심층_가이드.md |
| 2026-05-06 | 신규 페이지 생성 | raw/race_test.md | [[워처_Race_가드_테스트]] | markdown |
| 2026-05-07 | 자동 ingest (워처) | raw/auto_ingest_test.md | [[사내_인쇄_복합기_가이드]] |
| 2026-05-06 | **RPA 도입 타당성 ingest** — 사용자 제공 RPA 개관(나무위키 발췌, 국내 7개 벤더 정보) + 건축설계회사 도입 타당성 딥리서치(60+ 출처). 신규 페이지 [[RPA]] 생성 — Skanska/Arcadis/AECOM/Foster·Gensler 사례 + 국내 사례 부재 + 본 사무소 적용 매트릭스(★★★★★ 정부 사이트만, 다우오피스·BIM은 ★★ 이하) + 4가지 도구 비교(RPA·n8n·API·LLM 에이전트) + 국내 7개·해외 3개 벤더 가격 비교 + ROI 100%/12-18개월 회수 + 컨설팅 보고서 실패율(EY 30-50%, BCG 70%, Gartner Agent-washing) + 4단계 로드맵 + 3-tier 권장 아키텍처. 핵심 결론: **전사 RPA 부적합, 5-10개 한정 선택적 도입**. 기존 보강: [[자동화_시나리오]] 도구 분기 매트릭스, [[워크플로우_자동화_도구]] RPA 분기 한 줄, [[건축IT_사내개발_전략]] 1단계에 선택적 RPA PoC 추가 | `2026-05-06_rpa_namuwiki_overview.md` (사용자 제공), `2026-05-06_rpa_arch_firm_feasibility_research.md` (딥리서치) | [[RPA]], [[자동화_시나리오]], [[워크플로우_자동화_도구]], [[건축IT_사내개발_전략]], [[index]] |
| 2026-05-06 | **딥리서치 3건 ingest** — LLM Wiki 운영 방법 + 사내 챗봇 개발 + Wiki+챗봇 통합 구현. 신규 카테고리 "사내 LLM 시스템" 신설 + 신규 페이지 4개([[LLM_Wiki_운영_패턴]], [[사내_챗봇_시스템]], [[한국어_LLM_스택]], [[Wiki_Backed_Chatbot]]) + 기존 4개 보강([[LLM_Wiki_개념]] Karpathy 100페이지/40만단어, [[지식의_복리]] WikiChat 환각-55%/Graphify 토큰-99% 정량증거, [[Graphify]] GraphRAG/LightRAG 위치, [[자동화_시나리오]] 시나리오 6 추가). 핵심 발견: 본 사무소 적용 시 통합 패턴 D(Wiki 1차 + Gemini File Search fallback) 가 현 환경 최적, 한국어 스택 SOTA(Kiwi/KURE-v1/Solar Pro 2/Kordoc), 5~10 admin 큐레이션 모델 4단계 로드맵 | `2026-05-06_llm_wiki_operations_research.md`, `2026-05-06_corporate_chatbot_development_research.md`, `2026-05-06_wiki_backed_chatbot_research.md` | [[LLM_Wiki_운영_패턴]], [[사내_챗봇_시스템]], [[한국어_LLM_스택]], [[Wiki_Backed_Chatbot]], [[LLM_Wiki_개념]], [[지식의_복리]], [[Graphify]], [[자동화_시나리오]], [[index]] |
| 2026-05-06 | **Graphify·LLM Wiki 심화 자료 2건 ingest** — PyTorch Korea 의 Graphify 상세 정리(Sourcegraph/Code2Vec/Neo4j 비교 + 보안 설계) + Abraham Song(前 Visa 지식관리자) 의 셀프호스트 웹앱 실증(wiki.ai-biz.app, Hetzner CX23 + FastAPI + D3.js + Telegram + Hermes). 신규 페이지 1개([[LLM_Wiki_웹구현_사례]]) — 본 위키의 사내 공유 단계 진화 시 청사진. "사서(librarian)" 비유 추가 — 컴파일된 지식의 시스템 관점 짝. OAuth2↔PaymentGateway 자동 발견 일화가 [[지식의_복리]]·[[Graphify]] 의 정량 실증. Hermes 동명이인 주의 표시(Foster Hermes 와 무관) | `Graphify 복잡한 코드베이스를 한눈에 - AI 코딩 어시스턴트를 위한 지식 그래프(Knowledge Graph) 도구.md`, `LLM Wiki + Graphify를 직접 만들다.md` | [[LLM_Wiki_웹구현_사례]], [[Graphify]], [[LLM_Wiki_개념]], [[지식의_복리]], [[index]] |
| 2026-05-05 | **Graphify 도입** — Karpathy `/raw` 폴더 워크플로 직계 도구. Python 3.12.10 환경에 `graphifyy` 0.7.5 설치 + Claude Code Windows 글로벌 스킬(`~/.claude/skills/graphify/`) 등록. vault `CLAUDE.md` 보존 위해 사용자 레벨 설치 채택. 자동 그래프·INFERRED 엣지 후보 발견으로 본 위키 수동 [[교차참조]] 보완. [[지식의_복리]] 의 토큰 절감 명제(Lucas Rosati 사례 499x) 정량 구현 | `2026-05-05_graphify_install_guide.md` | [[Graphify]], [[index]] |
| 2026-05-05 | **건강검진** — 깨진 링크 1건(CLAUDE.md 헤더 `[[위키링크]]`) 백틱 처리. 6개 점검 항목 모두 클린. 메타 페이지(정보_일원화_패턴 56, 통합형_vs_연동형 43, 지식의_복리 33)가 그래프 중심에 위치 — 본 위키 통찰 구조 정상 | — | [[CLAUDE]] |
| 2026-05-05 | AI/자동화/AEC 자료 4건 ingest. 카테고리 2개(AI 바이브 코딩 / 전산업무 자동화) 채움 + 신규 카테고리 1개(건축 IT/AEC) 신설. Foster Hermes ↔ ERP 손익 일원화, Hydra ↔ 지식의 복리 등 의외의 동형성 추가 | `2026-05-05_vibe_coding_tools.md`, `2026-05-05_workflow_automation_tools.md`, `2026-05-05_aec_global_inhouse_dev.md`, `2026-05-05_aec_korea_inhouse_dev.md` | [[Claude_Code]], [[바이브_코딩_도구]], [[워크플로우_자동화_도구]], [[자동화_시나리오]], [[해외_AEC_사내개발]], [[정림건축_혁신본부]], [[AEC_자동화_플랫폼]], [[건축IT_사내개발_전략]], [[index]] |
| 2026-05-05 | ERP/PMS + LLM Wiki 자료 6건 ingest. 의외의 도메인 동형성 발견(LLM Wiki ↔ ERP 손익 일원화) → 메타 페이지 3개 신설 | `2026-05-05_amaranth10_overview.md`, `2026-05-05_younglimwon_ksystem_ace.md`, `2026-05-05_amaranth10_vs_ksystem_comparison.md`, `2026-05-05_construction_pms_reference.md`, `LLM Wiki AI에게 기억력을 심는 방.. 네이버블로그.md`, `LLM Wiki 활용해보기 네이버 블로그.md` | [[더존_아마란스10]], [[영림원_K-System_Ace]], [[ERP_벤더_비교]], [[건설_PMS]], [[ERP_RFP_체크리스트]], [[LLM_Wiki_개념]], [[지식의_복리]], [[정보_일원화_패턴]], [[통합형_vs_연동형]], [[건강검진]], [[index]] |
| 2026-05-05 | 위키 초기화 — 폴더 구조, CLAUDE.md, index, log 생성 | — | [[index]], [[log]] |

## 메모 — 의외의 연결점 누적

### 4차 ingest (2026-05-06, Graphify·LLM Wiki 심화)
- **Abraham Song 의 셀프호스트 웹앱(wiki.ai-biz.app) = 본 위키의 다음 단계 청사진** — 같은 [[LLM_Wiki_개념]] + [[Graphify]] 조합을 Hetzner CX23(월 ~5달러) + FastAPI + D3.js + Telegram 으로 구현. 본 위키가 사내 공유 단계 진화 시 직접 참고
- **"사서(librarian)" 비유 = 컴파일된 지식의 시스템 관점 짝** — 사용자 관점 비유(컴파일)와 시스템 관점 비유(사서)가 [[지식의_복리]] 메타 명제의 양면. Graphify INFERRED 엣지가 사서의 능동적 활동의 정량 구현
- **OAuth2 ↔ PaymentGateway 자동 발견 일화** — 코드에 구조적으로 존재하지만 텍스트로 안 보이던 연결을 Graphify 가 발견. [[지식의_복리]] / [[Graphify]] 의 "INFERRED 엣지가 인간 큐레이션을 보강한다" 명제의 실증 사례
- **저자가 명시한 "저항하는 통합"** — graphifyy 통합이 개념상 단순하나 실제론 큐 hang / 메모리 누수 / 마크다운 파싱 실패 등 너저분. [[자동화_시나리오]] 의 "단계적 도입" 주장과 같은 교훈
- **타임아웃 분리 실측 데이터(쿼리 10s / 경로 30s / 재빌드 300s)** — 본 위키에서 Graphify 자동화/CI 거는 경우 출발점 사이징 자료
- **Telegram + 백엔드 분리 패턴** — 사내에선 다우오피스 메신저 + 백엔드로 치환 가능. 직원이 메신저로 자료 던지면 자동 ingest 흐름 (= [[자동화_시나리오]] 의 신규 후보)
- **Hermes 동명이인** — Abraham Song 의 Telegram 에이전트 "Hermes" 와 [[해외_AEC_사내개발|Foster Hermes (BIM 단일 데이터원)]] 가 우연히 같은 이름. 그리스 전령신이 "자료 전달자/중개자" 로 양쪽에 자연스럽게 매핑된 약한 동형성 단서 ([[정보_일원화_패턴]])
- **PyTorch Korea 정리의 인접 도구 비교** — Sourcegraph(코드 검색만, 그래프 미형성) / Code2Vec(임베딩 기반) / Neo4j(저장소만, 자동 생성 X). Graphify 의 자리는 "Karpathy /raw + 멀티모달 + 자동 클러스터" 의 교집합 — 본 위키와 같은 계보이므로 1순위
- **Graphify 의 로컬 우선 보안(SSRF/XSS/YAML 인젝션 방어 + 원시 코드 외부 송신 없음)** = [[ERP_RFP_체크리스트]] 의 보안 사고 구조와 동일. [[해외_AEC_사내개발|Foster "AI 실용성, 자체 통제"]] 와 같은 입장

### 3차 ingest (2026-05-05, Graphify 도입)
- **Graphify README가 명시적으로 "Karpathy /raw 폴더 워크플로 기반"** ← 본 위키와 같은 원전 직계. 본 위키가 인간(Claude) 큐레이션 위주라면 Graphify는 알고리즘 클러스터링 위주 — 같은 가치의 두 구현
- **Graphify 의 18개+ 어댑터 (Claude/Cursor/Codex/Gemini/Copilot/Antigravity/...) + `--mcp` 모드** = [[Claude_Code]] + MCP 6개와 같은 [[통합형_vs_연동형|연동형]] 아키텍처. 코어 엔진 + 다수 인터페이스 조립
- **graph.json 영속 = [[지식의_복리]] 의 토큰 단위 측정** (Lucas Rosati 사례: 세션당 71.5x, 쿼리당 499x). [[지식의_복리]] 명제가 정량적으로 측정 가능해진 것
- **Foster Hermes 단일 데이터원 ↔ Graphify graph.json 단일 그래프** = [[정보_일원화_패턴]] 의 BIM 도메인 ↔ 지식 도메인 동형
- **Graphify EXTRACTED/INFERRED/AMBIGUOUS 엣지 감사 = [[CLAUDE|운영규칙]] "추측 금지" 원칙의 알고리즘 표상**. 동일 메커니즘
- **`graphify benchmark` + `--cluster-only` = [[건강검진]] 의 자동화 도구**. 고립 노드/중복 후보/미연결 카테고리를 정량 점검

### 2차 ingest (2026-05-05, AI/자동화/AEC)
- **Foster Hermes (클라우드 단일 데이터원)** = ERP 손익 일원화 = 위키 단일 정보원 → [[정보_일원화_패턴]] 의 AEC 도메인
- **Foster Hydra (130만 시뮬레이션 자산화)** = [[지식의_복리]] 의 시뮬레이션·컴퓨테이셔널 디자인 도메인
- **Speckle (오픈 데이터 허브) vs Autodesk Construction Cloud (통합)** = [[통합형_vs_연동형]] AEC 도메인 양극
- **NXT BLD 2026 "Applications on Demand" 트렌드** = [[Claude_Code]] + MCP 구조와 정확히 같은 방향
- **Foster "AI 실용성, 이미지 생성 회피" 원칙** = [[ERP_RFP_체크리스트]] 의 데모 검증 원칙 (마케팅 ≠ 실무 가치)
- **정림 J-edu 모델** = [[지식의_복리]] 의 조직·인적자원 차원 구현
- **정림 BIM 규모검토 Package "단일 정보원"** = ERP 손익 일원화의 BIM 도메인 구현
- **n8n 셀프호스트 + Claude Code + MCP** = 모두 "사내 인프라 + 외부 LLM = 데이터 정책 보존" 동형 패턴
- **ERP 통합형/연동형 분기 = AEC 통합형/연동형 분기** → 두 결정을 일관되게 해야 사무소 IT 전략 모순 없음 (→ [[건축IT_사내개발_전략]])

### 1차 ingest (2026-05-05, ERP/PMS + LLM Wiki)
- **LLM Wiki "지식 복리 누적"** ↔ **대림산업 PMS "손익 일원화"** 본질 동일 → [[정보_일원화_패턴]]
- **더존 아마란스 vs 영림원 K-System** ↔ **ChatGPT Workspace vs Claude+MCP** 같은 양극 형태 → [[통합형_vs_연동형]]
- **위키 Lint / ERP 정합성 / PMS 마감 점검** 모두 같은 메커니즘 → [[건강검진]]
- 블로그 작가의 **"Jira issue 다 ingest"** 아이디어 ↔ ERP 마이그레이션의 레거시 데이터 이관 동일 구조

이 통찰들이 [[ERP_RFP_체크리스트]], [[건축IT_사내개발_전략]] 의 "1차 결정은 통합형/연동형 분기" 의 근거가 됨.
