---
source_type: deep_research
topic: 사내 챗봇 시스템 개발 과정
captured_at: 2026-05-06
contributor: Claude (Sonnet 기반 리서치)
---

# 사내 챗봇 시스템 개발 과정 — 300명 건축설계사무소 IT 책임자 관점의 딥리서치

> **컨텍스트**: 본 리서치는 이미 Gemini File Search 기반의 RAG 챗봇 `haema-knowledge-base`(사내 매뉴얼 PDF Q&A)를 운영 중인 300명 규모 건축설계사무소(해마종합건축사사무소)가, 다음 단계로 본격적인 사내 챗봇 시스템을 구축할 때 학습해야 할 모든 영역을 정리한 문서다. 인프라는 Synology NAS·다우오피스·별도 PMS+ERP·GPU 부재라는 제약을 가지며, 핵심 스택은 Python·FastAPI·Claude Code다.

---

## 1. 리서치 범위 + 방법

### 리서치 범위
1. 사내 챗봇 시스템 아키텍처 패턴 (LLM 래퍼 / RAG / Agentic / Hybrid)
2. 데이터 파이프라인 (수집·청킹·임베딩·벡터DB·증분 업데이트)
3. LLM 백엔드 선택 (클라우드 vs 온프레미스 vs 하이브리드 라우팅)
4. RAG vs Fine-tuning vs Agentic 비교
5. 인증·권한·감사 (LDAP/SSO·RBAC·감사 로그)
6. 한국어 처리 특수성 (형태소 분석기·한국어 임베딩·벤치마크)
7. 운영·모니터링·비용 관리 (토큰 비용·환각 측정·피드백 루프·A/B 테스트)
8. 실제 사례 분석 (한국 대기업·해외·건설업계·실패 사례)
9. 개발 단계별 일정·인력 (PoC→MVP→정식)
10. 대표 도구/프레임워크 비교 (LangChain·LlamaIndex·Haystack·Dify·Chainlit 등)

### 리서치 방법
- WebSearch 한국어/영어 병행 (`사내 챗봇 RAG`, `enterprise chatbot architecture`, `HyperCLOVA 사내`, `Skanska Sidekick`, `LLM 토큰 비용 모니터링`, `한국어 임베딩 KURE` 등 12개 키워드 군집).
- WebFetch 로 핵심 본문 4건 수집 (Skelter Labs · AI Expert Network · KURE GitHub · YoungJu.dev RAG 가이드).
- 단순 인용이 아니라 **300명 사무소 + 기존 Gemini 챗봇 운영 중**이라는 컨텍스트에 맞춰 종합 분석을 추가했다.

---

## 2. 핵심 발견 10개

### 발견 1 — 아키텍처 패턴: "단순 LLM 래퍼"는 거의 무가치, 표준은 RAG, 미래는 Agentic+MCP

2025년 기업용 챗봇은 사실상 **모듈형 RAG**가 baseline이다. 단순 LLM 래퍼(System Prompt + LLM 호출)는 일반 지식 Q&A는 가능하지만 사내 매뉴얼·도면·계약서·PMS 데이터를 활용할 수 없어 사내 챗봇으로서의 가치가 거의 없다. **2025년 성공한 엔터프라이즈 LLM 배포의 약 80%가 견고한 RAG 아키텍처에 의존**한다는 보고가 있다(Glean, TechTarget). 표준 5계층 분리는 다음과 같다.

```
[Client UI] ← WebSocket ← [API Gateway] → [Application Layer (FastAPI)] → [LLM Gateway] → [Vector DB / Cache / Object Store]
```

각 계층을 독립적으로 확장·교체할 수 있도록 설계해야 LLM 공급사 변경(예: OpenAI → Claude → HyperCLOVA)에도 백엔드 코드를 다시 쓸 필요가 없다. 2025년 후반부터는 **MCP(Model Context Protocol)** 가 컨텍스트 일관성·툴 호출 표준으로 정착했고, RAG 자체가 단순 "검색 후 생성"에서 **Context Engine**으로 진화 중이다(RAGFlow blog).

> **300명 사무소 적용**: 현 단계 `haema-knowledge-base`는 Gemini File Search라는 SaaS RAG에 의존하지만, 다음 MVP에서는 **FastAPI 백엔드 + 자체 벡터DB + LLM Gateway 추상화 계층**으로 옮겨야 PMS·ERP·다우오피스 데이터까지 흡수할 수 있다.

### 발견 2 — 데이터 파이프라인: 청크 512 토큰 + 10–20% 오버랩 + Hybrid Search 가 2025년 baseline

업계 합의된 출발점은 다음과 같다(Weaviate, Databricks Community, Firecrawl):

- **청크 크기**: 400–512 토큰 (한국어 기준 약 800–1000자)
- **오버랩**: 10–20% (50–100 토큰)
- **분할 방식**: `RecursiveCharacterTextSplitter`로 시작, 필요 시 Semantic Chunking 도입
- **Semantic Chunking**: 단순 분할 대비 +9% Recall, 일부 도메인에서 최대 70% 정확도 상승. 단 비용·복잡도가 증가하므로 baseline에서 메트릭이 부족할 때만 전환.

**Hybrid Search**(Dense vector + BM25 키워드 + Reciprocal Rank Fusion)는 순수 벡터 검색 대비 검색 정확도 **15–30% 향상**으로 2025년 사실상 표준이 되었다. 한국어 환경에서는 BM25 토크나이저로 `Kiwi`를 쓰는 것이 가장 자연스럽다(테디노트 등 다수 사례).

> **300명 사무소 적용**: 매뉴얼 PDF는 표·도면 캡션이 많아 일반 텍스트 청크보다 **구조 인식형 파서**(Kordoc, Deep Parser)가 적합하다. 특히 HWP 처리는 LangChain·LlamaIndex에서 기본 지원하지 않으므로 `Kordoc` 등 한국어 특화 OSS 사용이 거의 필수다.

### 발견 3 — 한국어 환경에서 임베딩 모델은 `BGE-M3` 계열, 특히 `KURE-v1` 이 SOTA

한국어 RAG에서는 OpenAI `text-embedding-3-large`로 시작해도 무방하지만, 비용·데이터 보호·정확도 면에서 **고려대 NLP 연구실의 KURE-v1**(BGE-M3 기반·약 200만 한국어 학습 데이터)이 MTEB-ko 리더보드 상위권이며 `dragonkue/BGE-m3-ko`와 `BAAI/bge-m3`보다 모든 평가 지표에서 우수하다는 결과가 보고됐다.

| 모델 | 차원 | 운영 형태 | 한국어 강점 | 비용 |
|---|---|---|---|---|
| OpenAI text-embedding-3-large | 3072 | API | 양호 | $0.13/1M tokens |
| BGE-M3 | 1024 | Self-host | 매우 우수 | 인프라만 |
| **KURE-v1** | 1024 | Self-host | **국내 SOTA** | 인프라만 |
| KoE5 | 1024 | Self-host | 우수 (E5 기반) | 인프라만 |
| KoSimCSE-RoBERTa | 768 | Self-host | 짧은 문장 강세 | 인프라만 |

> **300명 사무소 적용**: GPU가 없으므로 KURE-v1을 자체 호스팅하려면 CPU 임베딩(초당 수십 문장 수준)이 가능하지만 인덱싱 단계는 충분하고, 실시간 쿼리 임베딩만 OpenAI API로 보내는 **하이브리드**도 충분히 가능하다. 매뉴얼 PDF는 한 번 인덱싱하면 증분 업데이트만 필요하므로 GPU 부재가 결정적 제약은 아니다.

### 발견 4 — 벡터DB 선택: 300명 규모는 `Qdrant` 또는 `pgvector` 가 정답

벡터DB 6종 비교(LiquidMetal, Tensorblue 등 다수)를 종합하면:

| 벡터DB | 성격 | p99 latency | 강점 | 약점 |
|---|---|---|---|---|
| Pinecone | 완전 관리형 SaaS | 8ms | Zero-ops | 비용 급증 |
| Weaviate | OSS / Cloud | 10ms | Hybrid·멀티모달 | 운영 복잡 |
| **Qdrant** | OSS (Rust) | **2ms** | 메타데이터 필터 강력 | 한국어 사례 적음 |
| Milvus | OSS (분산) | 5ms | 수십억 벡터 확장성 | 인프라 무거움 |
| Chroma | OSS (Python) | - | 가장 단순 | 프로덕션 부족 |
| pgvector | Postgres 확장 | - | 기존 RDB 통합 | 1억 벡터 한계 |

**300명 규모, 사내 매뉴얼 100~500만 청크 추정**이면 Pinecone·Milvus는 과잉이고, **Qdrant**(Synology Docker로 자체 호스팅) 또는 **Postgres+pgvector**(다우오피스/PMS DB 통합 시)가 합리적이다. `pgvector`는 1000만~1억 벡터 이전까지는 성능 문제가 없고, 메타데이터(부서·문서종류·기밀등급)와 벡터를 하나의 RDB에서 조인 검색할 수 있는 운영상 이점이 크다.

### 발견 5 — LLM 백엔드는 "클라우드 1순위 + 온프레미스 백업"의 하이브리드

GPU가 없는 사무소가 100% 온프레미스 LLM을 운영하는 것은 비현실적이다. 2025년 한국어 챗봇 아레나는 **Gemini 3 시리즈가 1~3위를 독점**하며, KMMLU 기준으로는 GPT-4o(81.1점), SKT A.X 4.0(78.3점), Solar Pro 2(31B로 Claude 4 Sonnet급) 순으로 한국어 성능이 우수하다(MSAP, Elice 벤치마크).

권장 패턴은 **Hybrid LLM Routing**이다. Microsoft Research(Hybrid LLM, ICLR 2024)·Requesty 사례에 따르면 라우터가 쿼리 난이도를 판별해 단순 질의는 작은/저렴한 모델, 복잡 질의는 GPT-4o/Claude로 보내는 방식으로 **AI 처리 비용 39% 절감 + 단순 쿼리 지연 32~38% 단축**이 가능하다.

> **300명 사무소 적용**: 1차 호출은 Gemini 1.5 Flash(저비용·한국어 양호), 2차 폴백은 Claude Sonnet(설계 도면 캡션·법규 해석 같은 복합 추론), 민감 정보가 포함된 질의는 **Solar Pro 2 또는 HyperCLOVA X**(국내 사업자, KISA 보안 적합성) 라우팅이 합리적이다.

### 발견 6 — RAG vs Fine-tuning vs Agentic: 사내 챗봇은 RAG가 90%, Agentic은 일부 워크플로우

| 방식 | 적합 도메인 | 운영 부담 | 환각 | 한국 기업 채택률 |
|---|---|---|---|---|
| **RAG** | 사내 매뉴얼·정책·FAQ | 중간 | 낮음 (출처 인용) | 매우 높음 (~80%) |
| Fine-tuning | 톤·스타일 통일, 분류 | 높음 (재학습) | 데이터 의존 | 낮음 (특수 케이스) |
| Agentic | 다단계 업무·툴 호출 | 매우 높음 | 중간 | 증가 중 (~20%) |
| Hybrid | RAG + 툴 호출 | 매우 높음 | 낮음 | 신규 표준화 중 |

Skelter Labs 가이드와 KB국민카드·A언론사 사례에 따르면 **사내 지식 챗봇은 RAG가 압도적**이고, Fine-tuning은 비용 대비 효과가 낮아 사내 톤·말투 통일이 절실한 경우만 보조로 쓴다. Agentic은 PMS·ERP·승인 워크플로우 자동화에 유효하지만 LLM 환각 한 번이 결재선을 잘못 태울 수 있어 **HITL(Human-in-the-Loop) 게이트**를 강제해야 한다.

### 발견 7 — 인증·권한·감사: SSO + RBAC + 감사 로그는 협상 불가

2025년 기준 엔터프라이즈 챗봇은 SSO(Microsoft Entra ID·Google Workspace·LDAP)와 RBAC, 그리고 감사 로그가 사실상 의무다(EMQX 5.3, MakinaRocks Runway, LoginRadius 가이드). 핵심 패턴은:

- **SSO**: SAML 2.0 또는 OIDC. 다우오피스(Google Cloud 구축형)이라면 **Google Workspace SSO** 직접 통합이 가장 매끄럽다.
- **RBAC**: 부서·민감도별 인덱스 분리 + 메타데이터 필터(예: `department:design AND security_level<=2`).
- **감사 로그**: 모든 질의·답변·검색 결과·소스 문서 ID를 90일 이상 보존(Postgres `audit_log` 테이블 + Loki/Elasticsearch).
- **2026 트렌드**: 비휴먼 ID(API·봇)가 휴먼 ID 대비 3:1로 증가, **봇 자체의 권한 관리**가 보안 1순위.

> **300명 사무소 적용**: 챗봇이 PMS의 프로젝트 정보를 조회할 때 "이 사용자가 이 프로젝트의 PM 또는 본부장인가"를 매 질의마다 검증하는 권한 체크 미들웨어를 FastAPI Dependency로 구현해야 한다. RBAC 누락은 가장 흔한 사내 챗봇 사고 원인이다.

### 발견 8 — 한국어 처리 특수성: 형태소 분석기는 `Kiwi`, HWP는 별도 파서, 리랭커는 한국어 특화

한국어는 교착어이므로 **BM25 같은 키워드 검색에 형태소 분석이 필수**다. 분석기 비교:

| 분석기 | 속도 | 정확도 | 라이센스 | 비고 |
|---|---|---|---|---|
| MeCab-ko | 최고 | 양호 | LGPL | 사전 기반 |
| **Kiwi** | 빠름 | **86.7% (모호성 해소)** | LGPL | 딥러닝 결합·Pythonic |
| Khaiii | 중간 | 양호 | Apache 2 | 카카오, 데이터 기반 |
| Okt | 느림 | 보통 | Apache 2 | 트위터 출신 |
| Kkma | 가장 느림 | 정확 | GPL | 학술용 |

대부분 신규 프로젝트는 **Kiwi**가 첫 선택이다. 한국어 RAG 파이프라인은 다음과 같이 구성한다:

1. PDF/HWP/DOCX → Kordoc 또는 Deep Parser로 Markdown 변환
2. Kiwi로 BM25 인덱스 생성, KURE-v1으로 dense 임베딩
3. Hybrid Search (RRF) → BAAI/bge-reranker-v2-m3 또는 한국어 특화 reranker로 상위 5건 재정렬
4. LLM 프롬프트에 컨텍스트 + 출처 메타데이터 주입

### 발견 9 — 운영·모니터링·비용 관리: RAGAS·LangSmith·자체 피드백 루프

운영 단계의 4대 요소(Iguazio, Logz.io, ZenML 보고서):

1. **토큰 비용 모니터링**: RAGAS의 TokenUsageParser, LangSmith, Helicone, Langfuse 중 택1. **프롬프트 변경으로 평균 토큰이 30% 늘면 청구서가 오기 전에 CI에서 잡혀야 한다.**
2. **환각 측정**: RAGAS Faithfulness/Answer Relevancy 4지표(Ground truth 없이 측정 가능). 단순 검색형 질의에는 효과적이지만 복잡 질의에서는 부족하므로 **HaluGate**(vLLM blog 2025/12) 같은 토큰 단위 환각 감지 보강.
3. **사용자 피드백 루프**: 답변별 👍/👎 + 필수 reason 코멘트 → 일주일치 모아 RAGAS 재평가 + 청크/프롬프트/리랭커 튜닝.
4. **A/B 테스트**: LLM 모델별·프롬프트별·청크 크기별 트래픽 분배(예: 10% 카나리)로 응답 품질·비용·지연 동시 평가.

업계 규범상 **데이터 준비에 프로젝트 시간의 50~70%를 써야** 하고, MIT 연구는 **GenAI 프로젝트의 95%가 의미 있는 가치 창출 실패**라고 보고한다(Forbes/S&P Global 인용). 따라서 운영 지표 없이는 "쓸만한 챗봇"인지조차 판별 불가능하다.

### 발견 10 — 개발 일정: PoC 4–8주, MVP 8–16주, 정식 출시 6–9개월이 현실

업계 통상 일정(Asana, Biz4Group, DEPT, Ardura Consulting):

- **PoC (4–8주)**: 단일 사용 케이스, 100~500개 문서, 30명 내부 베타. 비용 $5K~$15K.
- **MVP (8–16주)**: 운영 인증·로깅·UI·증분 인덱싱·5개 부서 확장.
- **정식 출시 (3~6개월 추가)**: SLA·모니터링·온콜·SSO·감사 통합·전사 출시.

필요 인력 구성: AI/LLM 엔지니어 1, 백엔드(FastAPI) 1, 프론트(Chainlit/React) 0.5, DevOps 0.5, UX·도메인 전문가 0.5, PM 0.3 — 약 **3.8 FTE × 6개월**이 표준. 300명 사무소라면 IT 책임자 1인 + 외부 전문 협력사 패턴(예: Skelter Labs, MakeBot, Upstage AI Lab) 또는 **사내 시민 개발자(Vibe Coder) + Claude Code로 1~2인 구성**도 충분히 가능하다.

---

## 3. 사례 모음

### 사례 A — Skanska Sidekick (글로벌 건설사, 27,000명)
- **스택**: Microsoft Azure + GPT-4, 폐쇄형 클라우드 배포
- **목적**: 사내 지식 검색·문서 요약·콘텐츠 생성
- **성과**: 정보 접근 속도 가속화, 팀 협업 개선
- **교훈**: 데이터 보안 최우선 + 대규모 변화관리·교육 필수
- **건설업계 시사점**: 27,000명 vs 300명이라는 규모 차이는 있으나, **사내 매뉴얼·표준 도면·법규**라는 핵심 콘텐츠는 동일. Sidekick의 5계층 아키텍처(Client/Gateway/App/LLM/Storage)를 그대로 축소 적용 가능.

### 사례 B — KB국민카드 (한국 금융, Skelter Labs 협업)
- **목적**: 200건 이상 이벤트 정보의 신속·정확 제공
- **방식**: RAG + Extra Data 꼬리표(시나리오별 분기)
- **성과**: 대규모 문서 관리 운영 부담 해결
- **교훈**: 비즈니스 목표 명확화(추상적 요구는 실패). "고품질 소스 콘텐츠 확보"가 RAG 정확도 결정.

### 사례 C — A언론사 (Skelter Labs)
- **목적**: 검증된 출처 기반 답변으로 환각 최소화
- **방식**: 정제 기사만 인덱싱, 출처 강제 인용
- **교훈**: 소스 콘텐츠 품질 = 챗봇 품질 (1:1).

### 사례 D — 삼성전자 "삼성 가우스" + "code.i"
- **공개**: 2023년 11월
- **모델**: 자체 개발 텍스트·코드·이미지 생성형 AI
- **활용**: 이메일 초안·문서 요약·번역 + 사내 코드 어시스턴트
- **교훈**: 자체 LLM은 데이터 보호와 도메인 적합성에서 우위, 단 30만 명 규모이기에 가능. 300명 사무소는 자체 모델 학습 대신 클라우드 API + 강한 RAG가 ROI상 합리적.

### 사례 E — 현대백화점 "루이스(Lewis)" — HyperCLOVA X 기반
- **목적**: 판촉 행사 카피라이팅 자동화
- **모델**: NAVER CLOVA Studio
- **교훈**: 한국어 카피·문체에서는 국내 LLM이 글로벌 대비 자연스러움 우위.

### 사례 F — 현대자동차그룹 이노션
- **방식**: 사내 GPT 기반 자체 솔루션
- **활용**: 마케팅 전략 수립 고도화
- **교훈**: 도메인 데이터 + 일반 LLM의 결합으로 충분.

### 사례 G — LG전자 AICC + LG디스플레이 OLED
- **방식**: STT/TA + 보이스봇 + 제조 데이터 분석 AI
- **성과**: 품질 이상 분석 시간 3주 → 2일
- **교훈**: 챗봇이 "대화형 UI"에 머무르지 않고 운영 데이터와 결합할 때 가치 극대화.

### 사례 H — Upstage Solar Pro 2 (조달청)
- **역할**: 2025년 12월 조달청 "생성형 AI 업무지원 서비스" 1호 공급사
- **경쟁력**: 100만 토큰당 $0.5(동급의 1/10), 31B 컴팩트, 한국어 SOTA 근접
- **시사점**: 공공·민간 조달 환경에서 국내 LLM 채택이 빠르게 늘어 데이터 보호 요건이 강한 건축설계사무소(공공 발주 비중 높음)에도 적합.

### 사례 I — 카사코리아 (AWS Bedrock 기반 Agentic 챗봇)
- **방식**: Amazon Bedrock + 에이전트형 (대고객)
- **교훈**: 사내가 아닌 대고객이지만 Agentic 구조 참고 가치 있음.

### 사례 J — Spoon Labs RAG 도입기 (Medium)
- **방식**: 사내 RAG 챗봇 점진적 도입
- **교훈**: "조용히 그리고 낭만적으로" — 빅뱅이 아닌 점진적 출시·피드백·재인덱싱 루프가 성공 패턴.

### 실패 사례 — DPD 챗봇 욕설 사건, Taco Bell 18,000컵 주문 크래시
- **공통 원인**: 가드레일 부재, HITL 부재, 도메인 외 질의 처리 미설계.
- **교훈**: 챗봇은 **거절(Refuse) 능력**이 핵심. "이 질문은 답변할 수 없습니다"를 우아하게 말하는 것이 답변 정확도보다 중요한 경우가 많다. **MIT 연구: GenAI 프로젝트의 95%가 가치 창출 실패**, **S&P Global: 2025년 기업의 42%가 대부분의 AI 이니셔티브 폐기**.

---

## 4. 도구 비교 표 (RAG/Agent 프레임워크)

| 항목 | LangChain | LlamaIndex | Haystack | Dify | Flowise | Chainlit |
|---|---|---|---|---|---|---|
| 주력 | 오케스트레이션·툴 | 데이터 인덱싱·쿼리 | 파이프라인·검색 | 노코드 LLMOps | 노코드 RAG | LLM UI |
| 진입장벽 | 중간 (방대) | 낮음 | 중간 | **매우 낮음 (UI)** | 매우 낮음 | 낮음 |
| 한국 채택 | 매우 높음 | 높음 | 중간 | 빠르게 증가 | 중간 | 빠르게 증가 |
| 한국어 자료 | 위키독스 한국어 튜토리얼 풍부 | 다수 | 부족 | 한국어 가이드 다수 | 일부 | 다수 |
| HWP 지원 | × (별도 파서 필요) | × (별도) | × | × | × | n/a |
| 프로덕션 | 검증 풍부 | 검증 풍부 | 매우 안정 | 자체 호스팅 가능 | 경량 | 빠른 발전 |
| 적합 시나리오 | 복잡한 에이전트·툴 호출 | 데이터 우선·인덱싱 | 검색 기반 엔터프라이즈 | 비개발자 운영 | 빠른 PoC | 챗봇 UI 표준 |
| 권장 조합 | 백엔드 오케스트레이션 | 인덱싱 단계 | 프로덕션 검색 | 부서별 PoC | 1주 PoC | 사내 UI |

**해마 사무소 권장 조합**: 백엔드 = LangChain (FastAPI 위에 RAG 체인) + 인덱싱 = LlamaIndex (한국어 PDF 로더 풍부) + UI = Chainlit (인증·스레드·피드백 내장) + 부서별 노코드 = Dify (총무팀·인사팀 자체 운영).

---

## 5. LLM 백엔드 비교 표

| 모델 | 제공사 | 한국어 품질 | 비용/1M 토큰 (입력 기준) | 속도 | 데이터 보호 | 비고 |
|---|---|---|---|---|---|---|
| GPT-4o | OpenAI | 매우 우수 (KMMLU 81.1) | $2.50 | 빠름 | API (옵션 enterprise) | 균형 1위 |
| GPT-4o-mini | OpenAI | 우수 | $0.15 | 매우 빠름 | API | 라우터의 small 모델로 적합 |
| Claude Sonnet 4.x | Anthropic | 매우 우수 (글쓰기 1위급) | $3 | 빠름 | API + Bedrock 지역 | 복합 추론·문서 강함 |
| Claude Haiku | Anthropic | 우수 | $0.25 | 매우 빠름 | API | 저가 라우터용 |
| Gemini 1.5 Flash | Google | 우수 (아레나 1위 시리즈) | $0.075 | 매우 빠름 | API + Vertex 지역 | 가성비 최강 |
| Gemini 2.5 Pro | Google | 매우 우수 | $1.25 | 빠름 | Vertex AI | 한국어 자연스러움 강세 |
| HyperCLOVA X | Naver | 매우 우수 (문체 SOTA) | API 기준 | 빠름 | NCP 국내 리전 | 국내 데이터 보호 강점 |
| Solar Pro 2 (31B) | Upstage | 매우 우수 (Claude 4 Sonnet급 추론) | **$0.5** | 빠름 | API + 온프레미스 옵션 | 가성비 + 국내 |
| A.X 4.0 | SKT | 우수 (KMMLU 78.3) | 협의 | - | 국내 | 통신 도메인 강세 |
| Kanana | Kakao | 우수 | 협의 | - | 국내 | 카카오 생태계 통합 |
| Llama 3.x 70B (Ollama/vLLM) | Meta | 보통 (한국어 fine-tune 필요) | 인프라만 | GPU 필요 | 100% 온프레 | GPU 부재 사무소엔 부적합 |
| Qwen 3 9B/32B | Alibaba | 우수 | 인프라만 | CPU도 9B 가능 | 100% 온프레 | 온프레 1순위 후보 |
| Gemma 2 9B | Google | 보통 | 인프라만 | CPU 가능 | 100% 온프레 | 라이센스 우호적 |

> **300명 사무소 권장 라우팅**:
> - 80% 일반 질의 → **Gemini 1.5 Flash** ($0.075)
> - 15% 복합 추론·법규 해석 → **Claude Sonnet** ($3)
> - 5% 민감 정보 (인사·계약) → **Solar Pro 2 또는 HyperCLOVA X** (국내 리전)
> - 비상 시 자체 호스팅 백업 → **Qwen 3 9B on Synology Docker** (CPU 추론, 분당 수십 토큰 수준이지만 가능)

---

## 6. 권장 개발 로드맵 (300명 건축설계사무소 맞춤)

### Phase 0 — 현재 (이미 완료)
- Gemini File Search 기반 `haema-knowledge-base` 운영
- 사내 매뉴얼 PDF Q&A
- **검증된 가치**: 직원이 "AI에게 묻는다"는 행동 정착

### Phase 1 — PoC (4–8주, 이미 진행한 단계의 확장)
- **목적**: FastAPI 백엔드로 Gemini File Search 의존성 분리
- **작업**:
  - FastAPI + Chainlit 베이스라인 구축
  - Synology Docker에 **Qdrant** 또는 **Postgres+pgvector** 구동
  - PDF + HWP 처리 파이프라인 (`Kordoc` 또는 `Deep Parser`)
  - 임베딩 = OpenAI `text-embedding-3-small` (저비용 시작) 또는 `KURE-v1` 자체 호스팅
  - LLM = Gemini 1.5 Flash (가성비)
  - Hybrid Search (Kiwi BM25 + dense)
- **인력**: IT 책임자 1 + Claude Code 보조 (비개발자 1명 포함 가능)
- **산출물**: 단일 본부 30명 베타, RAGAS 점수 baseline 측정
- **예산**: API 월 $50~$200 + Synology 디스크 추가

### Phase 2 — MVP (8–16주)
- **목적**: 전사 출시 가능한 신뢰성·보안·운영성 확보
- **작업**:
  - Google Workspace SSO 통합 (다우오피스 = Google Cloud 구축형)
  - RBAC: 부서·민감도 메타데이터 필터
  - 감사 로그: Postgres `audit_log` + 90일 보존
  - LLM Gateway 추상화 (Hybrid LLM Routing)
  - 사용자 피드백 (👍/👎 + reason)
  - 증분 인덱싱 자동화 (Synology 폴더 변경 감지 → Qdrant 업데이트)
  - 모니터링: Langfuse 또는 Helicone
- **인력**: IT 책임자 + 외주 LLM 엔지니어 1 (3개월 단기)
- **산출물**: 5개 본부 100명 베타, RAGAS Faithfulness ≥ 0.85 목표
- **예산**: API 월 $300~$800 + 외주 약 ₩30~50M

### Phase 3 — 정식 (3–6개월 추가)
- **목적**: 전사 + Agentic 워크플로우 진입
- **작업**:
  - PMS·ERP·다우오피스 API 통합 (Tool 계층)
  - HITL 게이트가 있는 Agent (예: 외근 신청서 초안 작성·결재 라우팅)
  - A/B 테스트 (모델·프롬프트·청크 크기)
  - SLA 99.5% (Synology HA + 클라우드 LLM 폴백)
  - 분기별 RAGAS 재평가 + 청크/리랭커 재튜닝
- **인력**: 운영 0.5 FTE 정착
- **산출물**: 300명 전사 + 제휴 사무소 확장 검토

---

## 7. 300명 사무소 IT 책임자를 위한 핵심 의사결정 체크리스트

1. **현재 Gemini File Search → 자체 RAG 전환 시점**: 사용자 100명 도달 또는 PMS/ERP 데이터 결합 욕구 발생 시점.
2. **벡터DB 결정**: Synology만 있으면 **Qdrant Docker**, RDB 통합 필요하면 **pgvector**.
3. **임베딩**: 시작은 OpenAI `text-embedding-3-small`, 안정화 후 **KURE-v1** 자체 호스팅.
4. **LLM**: 80/15/5 라우팅 (Gemini Flash / Claude Sonnet / Solar·HyperCLOVA).
5. **HWP 처리**: `Kordoc` 또는 `Deep Parser` — 한국어 RAG의 숨은 결정 변수.
6. **UI**: Chainlit (인증·스레드·피드백 내장).
7. **인증**: Google Workspace SSO (다우오피스와 동일 IdP).
8. **감사**: Postgres `audit_log` + 분기별 검토.
9. **평가**: RAGAS + 사용자 피드백 + 월별 회고.
10. **거버넌스**: 가드레일 프롬프트 + Refuse 시나리오 + HITL 게이트.

---

## 8. 출처 (URL 목록 + 수집일 2026-05-06)

### 한국어 소스
- [요즘IT — RAG 기반 사내 지식 챗봇 구축기](https://yozm.wishket.com/magazine/)
- [YoungJu.dev — Slack Bot + LangChain RAG 챗봇 구축 실전 가이드](https://www.youngju.dev/blog/chatbot/2026-03-03-slack-langchain-rag-chatbot)
- [오상테크놀로지 — RAG 사내지식 챗봇 솔루션](https://www.osangtech.co.kr/RAG.html)
- [kt cloud — AI 검색 증강 생성(RAG) 핵심 개념과 시스템 구조](https://tech.ktcloud.com/entry/2025-08-ktcloud-ai-rag-%EC%8B%9C%EC%8A%A4%ED%85%9C%EA%B5%AC%EC%A1%B0-%EC%9D%B4%ED%95%B4)
- [삼성SDS — Gen AI 해커톤 기업 맞춤형 RAG 적용 사례](https://www.samsungsds.com/kr/insights/rag-customization.html)
- [코스미안뉴스 — RAG 혁명, 한국 기업 2025년 AI 전면 도입](https://www.cosmiannews.com/news/406460)
- [Skelter Labs — 기업용 LLM+RAG 챗봇 도입 가이드](https://www.skelterlabs.com/blog/enterprise-llm-and-rag)
- [Spoon Labs (Medium) — RAG 도입기, 챗봇을 만들다](https://medium.com/spoontech/rag-%EB%8F%84%EC%9E%85%EA%B8%B0)
- [MakeBot — 기업이 2025년 RAG 시스템을 선택하는 이유](https://www.makebot.ai/blog/gieobi-2025nyeone-rag-siseutemeul-seontaeghaneun-juyo-iyu-gisuljeog-bunseog)
- [Botpress — 2025년에 RAG 챗봇 만드는 법](https://botpress.com/ko/blog/build-rag-chatbot)
- [namu.wiki — CLOVA X / HyperCLOVA](https://namu.wiki/w/CLOVA%20X)
- [NAVER CLOUD PLATFORM — HyperCLOVA X](https://www.ncloud.com/solution/featured/hyperclovax)
- [CIO Korea — 네이버 파라미터 40% 줄인 HyperCLOVA X](https://www.cio.com/article/3828615/)
- [DARVIS Blog — 2025 기업 AI 활용 사례 (삼성·LG·SK·현대)](https://blog.dfinite.ai/enterprise-ai-adoption-strategy)
- [전자신문 — 삼성전자 사내 업무 지원 생성형 AI 도입](https://www.etnews.com/20231218000389)
- [AWS 기술 블로그 — 카사코리아 Agentic CS 챗봇](https://aws.amazon.com/ko/blogs/tech/kasakorea-agentic-cs-chatbot/)
- [GetNews — 2026 신년특집 삼성·SK·LG·현대차 AI](https://www.getnews.co.kr/news/articleView.html?idxno=855507)
- [한국딥러닝 — 문서 Parser PDF부터 HWP까지](https://www.koreadeep.com/blog/document-parser)
- [GitHub — chrisryugj/kordoc HWP·PDF·DOCX→Markdown](https://github.com/chrisryugj/kordoc)
- [GitHub — nlpai-lab/KURE 한국어 임베딩](https://github.com/nlpai-lab/KURE)
- [GitHub — bab2min/Kiwi 한국어 형태소 분석기](https://github.com/bab2min/Kiwi)
- [GitHub — ssisOneTeam Korean Embedding Benchmark](https://github.com/ssisOneTeam/Korean-Embedding-Model-Performance-Benchmark-for-Retriever)
- [su-park/mteb_ko_leaderboard](https://github.com/su-park/mteb_ko_leaderboard)
- [Data Dynamics — 임베딩 모델 선택 가이드 (한국어 벤치마크)](https://www.data-dynamics.io/ko/blog/embedding-model-guide)
- [GeekNews — 한국어 RAG BGE-M3 처음부터 구현](https://news.hada.io/topic?id=18603)
- [GPTers — Ollama 한국어 LLM](https://www.gpters.org/dev/post/korean-version-local-llm-74iRcn2ODbKSbMF)
- [omoknooni — LLama 기반 온프레미스 LLM 서버](http://blog.omoknooni.me/144)
- [YoungJu.dev — vLLM & Ollama 완벽 가이드](https://www.youngju.dev/blog/llm/vllm_ollama_serving_complete_guide)
- [SK Devocean — Ollama 개인형 LLM 서버](https://devocean.sk.com/blog/techBoardDetail.do?ID=165685&boardType=techBlog)
- [AWS 기술 블로그 — 한국어 Reranker로 RAG 성능 올리기](https://aws.amazon.com/ko/blogs/tech/korean-reranker-rag/)
- [Upstage Blog — Solar Pro 2 출시](https://www.upstage.ai/blog/ko/solar-pro-2-launch)
- [MSAP — 국내 LLM 모델 현황과 비교](https://www.msap.ai/blog-home/blog/korea-llm/)
- [Elice — 국산 LLM 6종 벤치마크](https://elice.io/ko/resources/blog/llm-benchmark-korea-elice)
- [WikiDocs — LLM 한국어 사용성 순위](https://wikidocs.net/226061)
- [WikiDocs — LangChain 한국어 튜토리얼](https://wikidocs.net/book/14314)
- [WikiDocs — Dify 노트](https://wikidocs.net/303950)
- [Velog — 한글 검색 성능 테스트 (Kiwi/BM25/FAISS)](https://velog.io/@sobit/)
- [WindyFlo Blog — LLMOps 4대 플랫폼 비교](https://blog.windyflo.com/blog/llmops-%ED%98%81%EB%AA%85-%EA%B8%B0%EC%97%85-ai/)
- [OPENMARU APM — 엔터프라이즈 AI 에이전트 빌더 4종](https://www.openmaru.io/ai-agent-builder/)

### 영어 소스
- [TechTarget — RAG best practices for enterprise AI teams](https://www.techtarget.com/searchenterpriseai/tip/RAG-best-practices-for-enterprise-AI-teams)
- [Glean — RAG models guide to enterprise AI 2025](https://www.glean.com/blog/rag-models-enterprise-ai)
- [Intelliarts — Enterprise RAG System Best Practices](https://intelliarts.com/blog/enterprise-rag-system-best-practices/)
- [Vajra Global — Enterprise Chatbots in 2025 Strategic Guide](https://vajraglobal.com/thought-leadership-solutions/from-faq-bots-to-digital-colleagues-rethinking-enterprise-chatbots-in-2025/)
- [Applied AI — Enterprise RAG Architecture Practitioner's Guide](https://www.applied-ai.com/briefings/enterprise-rag-architecture/)
- [RAGFlow — From RAG to Context 2025 review](https://ragflow.io/blog/rag-review-2025-from-rag-to-context)
- [Kanerika — LlamaIndex vs LangChain vs Haystack](https://kanerika.com/blogs/llamaindex-vs-langchain-vs-haystack/)
- [LangCopilot — Best RAG Frameworks 2025](https://langcopilot.com/posts/2025-09-18-top-rag-frameworks-2024-complete-guide)
- [Firecrawl — 15 Best Open-Source RAG Frameworks 2026](https://www.firecrawl.dev/blog/best-open-source-rag-frameworks)
- [LiquidMetal AI — Vector Database Comparison (Pinecone/Weaviate/Qdrant/Milvus/Chroma)](https://liquidmetal.ai/casesAndBlogs/vector-comparison/)
- [Tensorblue — Best Vector Database 2025](https://tensorblue.com/blog/vector-database-comparison-pinecone-weaviate-qdrant-milvus-2025)
- [Markaicode — Streamlit vs Gradio vs Chainlit](https://markaicode.com/vs/streamlit-vs-gradio-vs-chainlit/)
- [Squadbase — Streamlit vs Gradio in 2025](https://www.squadbase.dev/en/blog/streamlit-vs-gradio-in-2025-a-framework-comparison-for-ai-apps)
- [GetStream — 3 Best Python Frameworks for AI UIs](https://getstream.io/blog/ai-chat-ui-tools/)
- [Weaviate — Chunking Strategies for RAG](https://weaviate.io/blog/chunking-strategies-for-rag)
- [Databricks Community — Ultimate Guide to Chunking](https://community.databricks.com/t5/technical-blog/the-ultimate-guide-to-chunking-strategies-for-rag-applications/ba-p/113089)
- [Stack Overflow — Breaking up is hard to do (Chunking)](https://stackoverflow.blog/2024/12/27/breaking-up-is-hard-to-do-chunking-in-rag-applications/)
- [Firecrawl — Best Chunking Strategies for RAG 2026](https://www.firecrawl.dev/blog/best-chunking-strategies-rag)
- [Pinecone — Conversational Memory for LLMs with LangChain](https://www.pinecone.io/learn/series/langchain/langchain-conversational-memory/)
- [Vellum — How Should I Manage Memory for LLM Chatbot](https://www.vellum.ai/blog/how-should-i-manage-memory-for-my-llm-chatbot)
- [LangChain Docs — Memory overview](https://docs.langchain.com/oss/python/concepts/memory)
- [GetMaxim — Context Window Management Strategies](https://www.getmaxim.ai/articles/context-window-management-strategies-for-long-context-ai-agents-and-chatbots/)
- [arXiv 2404.14618 — Hybrid LLM Cost-Efficient Query Routing (Microsoft Research, ICLR 2024)](https://arxiv.org/abs/2404.14618)
- [Requesty Blog — Intelligent LLM Routing in Enterprise AI](https://www.requesty.ai/blog/intelligent-llm-routing-in-enterprise-ai-uptime-cost-efficiency-and-model)
- [Microsoft Research — Hybrid LLM publication](https://www.microsoft.com/en-us/research/publication/hybrid-llm-cost-efficient-and-quality-aware-query-routing/)
- [GitHub — microsoft/best-route-llm](https://github.com/microsoft/best-route-llm)
- [Iguazio — LLM Observability Tools 2025](https://www.iguazio.com/blog/llm-observability-tools-in-2025/)
- [vLLM Blog — HaluGate Token-Level Hallucination Detection](https://blog.vllm.ai/2025/12/14/halugate.html)
- [RAGAS Docs — Cost Analysis](https://docs.ragas.io/en/stable/howtos/applications/_cost/)
- [Atlan — RAGAS, TruLens, DeepEval comparison](https://atlan.com/know/llm-evaluation-frameworks-compared/)
- [Logz.io — Top LLM Observability Tools](https://logz.io/blog/top-llm-observability-tools/)
- [ZenML — 10 Best LLM Monitoring Tools 2025](https://www.zenml.io/blog/best-llm-monitoring-tools)
- [EMQX Blog — Enterprise 5.3 Audit Logs RBAC SSO](https://www.emqx.com/en/blog/emqx-enterprise-5-3-release-notes)
- [SuperTokens — Enterprise Identity Management](https://supertokens.com/blog/enterprise-identity-management)
- [StackAI — SSO and RBAC for AI Agents](https://www.stackai.com/insights/sso-and-rbac-for-ai-agents-how-to-secure-enterprise-ai-deployments)
- [MakinaRocks — Runway SSO RBAC Activity Log](https://www.makinarocks.ai/en/blog/runway-mlsecops-sso-rbac-activity-log-features/)
- [LoginRadius — RBAC Best Practices for Enterprise SSO](https://www.loginradius.com/blog/engineering/rbac-enterprise-sso-federation)
- [AIX (AI Expert Network) — Skanska Sidekick Case Study](https://aiexpert.network/ai-at-skanska/)
- [Frontiers in Built Environment — Conversational document-native automation in construction](https://www.frontiersin.org/journals/built-environment/articles/10.3389/fbuil.2025.1713342/full)
- [MDPI — Generative AI in AEC Trends](https://www.mdpi.com/2673-8945/4/4/46)
- [arXiv 2310.04427 — Generative AI in Construction Industry](https://arxiv.org/pdf/2310.04427)
- [Sphere Inc — AI Use Cases for Construction Industry 2025](https://www.sphereinc.com/blogs/ai-use-cases-for-construction/)
- [MakeBot — Why Most Enterprise Chatbot Projects Fail](https://www.makebot.ai/blog-en/why-most-enterprise-chatbot-projects-fail-before-they-begin)
- [Teneo — Chatbot Examples Gone Wrong](https://www.teneo.ai/blog/chatbot-examples-gone-wrong-lessons-and-insights)
- [ICTworks — 4 Lessons from Global AI Chatbot Failures](https://www.ictworks.org/global-digital-health-ai-chatbot-failures/)
- [MonteCarlo Data — 4 Famous AI Fails](https://www.montecarlodata.com/blog-famous-ai-fails)
- [Asana — Proof of Concept Definition Steps Examples 2026](https://asana.com/resources/proof-of-concept)
- [Biz4Group — PoC Development of an AI Chatbot Practical Guide](https://www.biz4group.com/blog/ai-chatbot-poc-development)
- [DEPT — From AI POCs to MVPs](https://www.deptagency.com/insight/from-ai-pocs-to-mvps-a-proven-process-for-speed-success/)
- [Ardura Consulting — AI Implementation Cost PoC to Production](https://ardura.consulting/blog/ai-implementation-cost-poc-to-production/)
- [Charles Sieg — Building Enterprise Chatbot React FastAPI WebSocket](https://www.charlessieg.com/articles/enterprise-chatbot-react-fastapi-websocket-architecture.html)
- [FastAPI Docs — WebSockets](https://fastapi.tiangolo.com/advanced/websockets/)
- [RedHat — vLLM vs Ollama](https://www.redhat.com/en/topics/ai/vllm-vs-ollama)

---

*문서 끝. 본 리서치 노트는 해마종합건축사사무소 IT 책임자(admin@haemaarch.com)의 사내 챗봇 시스템 다음 단계 의사결정을 위한 학습 자료다. 갱신 시점: 2026-05-06.*
