---
source_type: deep_research
topic: LLM Wiki 적용 챗봇 시스템 구현
captured_at: 2026-05-06
contributor: Claude (Sonnet 기반 리서치)
---

# LLM Wiki 를 적용한 챗봇 시스템의 구현 (Wiki-Backed Chatbot Implementation)

## 리서치 범위 + 방법

본 리서치는 한국 건축설계사무소(300인 규모) IT 책임자가 이미 운영 중인 두 가지 자산 — (1) Gemini File Search 기반 사내 매뉴얼 RAG 챗봇, (2) Karpathy LLM Wiki 패턴으로 큐레이션 중인 개인 vault — 을 **하나의 시스템으로 통합**할 때의 아키텍처·도구·운영·코드 패턴을 정리한다.

핵심 질문은 단순하다. **"매번 PDF 청크를 검색해 답을 합성하는 RAG"** 대신 **"이미 인간·LLM이 큐레이션해놓은 위키 페이지를 1차 컨텍스트로 사용하는 챗봇"** 은 어떤 모습인가? 위키 페이지 = 사전 컴파일된 지식 단위, [[교차참조]] = 답변의 구조적 맥락, 페이지 단위 출처 = 사용자 검증 가능 — 이 세 가지 차이를 축으로 본 리서치를 진행했다.

방법은 다음과 같다.

- **WebSearch**: 한국어·영어 14건 검색 (Karpathy LLM wiki, Abraham Song, GraphRAG, LightRAG, Smart Connections, Khoj, Copilot, WikiChat, Gemini File Search, graphify 벤치마크 등)
- **WebFetch**: 핵심 8건 본문 직접 수집 (Karpathy gist, Abraham Song Brunch, Lucas Astorian llmwiki, devwhodevs/engraph, Smart Connections, Epsilla blog, MindStudio 비교, LightRAG 등)
- 분석 기준: 사용자 vault(kouwiki) + 사내 Gemini 챗봇 통합 관점에서 의미 있는 패턴만 발췌 후 종합

---

## 핵심 발견 10개

### 1. Wiki-Backed RAG 와 Vector RAG 의 본질적 차이

벡터 RAG 는 **stateless 검색기** 다. 매 질의마다 PDF 를 청크로 잘라 임베딩 유사도로 끌어오고, LLM 은 그 청크들을 다시 읽어 답을 합성한다. 동일 질문이 반복돼도 매번 같은 비용·같은 노이즈로 처리된다. Epsilla 가 "RAG is stateless. Every query is a new day" 라고 표현한 그대로다.

위키 기반 RAG 는 **컴파일된 지식의 호출**이다. raw/ 의 원본 자료를 LLM 이 단 한 번 읽고 wiki/ 폴더에 개념 단위 마크다운 페이지로 정착시킨다. 이후 질의는 (1) 관련 위키 페이지 매칭 → (2) 페이지 본문 + [[교차참조]] 링크된 인접 페이지를 컨텍스트로 주입 → (3) LLM 이 답변. 사용자는 답변과 함께 출처 페이지 ID 를 받으므로 직접 그 페이지를 열어 검증·심화 탐색이 가능하다.

핵심 trade-off:

- **Vector RAG**: 자동성 높음, 큐레이션 부담 0, 답변 추적성 낮음, 동일 질문 비용 일정
- **Wiki-Backed**: 큐레이션 부담 있음(LLM 자동화로 일부 상쇄), 답변 추적성 페이지 단위로 명확, 반복 질의 시 토큰 절감

MindStudio 의 비교는 이 분기를 단순화한다. 잘 정의된 정책·매뉴얼·FAQ(예: 건축설계사무소의 사내 규정·CAD 표준·납품 매뉴얼)는 Wiki, 비정형 대규모 아카이브(예: 과거 프로젝트 도면 메타·고객 회의록 10년치)는 RAG.

### 2. Wiki + Chatbot 통합 아키텍처 패턴 4가지

리서치 결과 **4가지 통합 패턴**이 식별됐다. 사용자의 kouwiki vault + Gemini File Search 환경에 모두 적용 가능하다.

**패턴 A — 위키 본문 직접 주입 (Direct-Inject)**
- 사용자 질문 → 키워드/임베딩으로 위키 페이지 N개 선정 → 페이지 본문 전체를 시스템 프롬프트에 주입 → LLM 답변
- 장점: 구현이 가장 단순(FastAPI + frontmatter 파서 + LLM API 한 번 호출). 위키가 200페이지 미만이면 index.md 만 항상 주입해도 200K 컨텍스트 안에 들어간다.
- 단점: 위키가 1000페이지 이상 되면 페이지 매칭 알고리즘이 곧 병목이 된다.

**패턴 B — 위키 + 그래프 동시 사용 (Wiki + Graph)**
- Abraham Song wiki.ai-biz.app 의 패턴이 정확히 이것. 위키 마크다운은 사람이 읽기 위한 정착물, graphify 가 추출한 graph.json 은 LLM 이 길찾기·추론하기 위한 구조물.
- 질문 → graphify 의 query/path/explain 으로 관련 노드 추적 → 해당 노드와 연결된 위키 페이지를 컨텍스트로 주입 → 답변. 그래프가 cross-cutting 한 질문(예: "OAuth 모듈과 결제 모듈은 어떻게 연결돼?")을 자연스럽게 처리한다.

**패턴 C — 위키 페이지를 벡터 임베딩 (Hybrid Vector + Wikilink Graph)**
- engraph 가 대표 사례. 5계층 검색: 임베딩 + BM25 + 위키링크 그래프 확장 + CrossEncoder 재순위화 + 시간 점수. 상호 순위 융합(RRF)으로 통합.
- 위키링크 자체를 그래프 엣지로 활용해 "Sarah Chen 노트가 인증 시스템 검색에서 명시적 언급 없이 등장" 같은 발견을 만들어낸다. 사용자가 vault 에 [[교차참조]] 를 늘려놓을수록 자동으로 강해진다.

**패턴 D — Wiki 1차 + RAG Fallback**
- 위키에서 매칭이 충분하면 그 페이지로 답변. 매칭 실패 시 → Gemini File Search(원본 PDF 코퍼스) 로 fallback → 답변과 함께 "이 답은 위키에 없으니 새 위키 페이지로 정착시킬까요?" 제안.
- 사용자의 현재 환경(Gemini 챗봇 운영 중) + kouwiki(개인 vault) 통합에 가장 자연스럽게 매핑되는 패턴이다.

### 3. Karpathy LLM Wiki + 챗봇 결합 사례

Karpathy 의 원본 gist(2026.04 공개)는 챗봇을 직접 다루지 않는다. 그러나 **세 자산** — raw/(불변 원본), wiki/(LLM 큐레이션 마크다운), schema(CLAUDE.md/AGENTS.md 의 편집 규칙) — 은 챗봇 컨텍스트로 직결된다.

핵심 작동:
- 새 소스 추가 → LLM 이 읽고 요약 → wiki 페이지 생성 → 교차 참조 자동 업데이트
- 질의 시 index.md 검색 → 관련 페이지 조회 → 합성 답변 → **답변 자체를 새 위키 페이지로 저장** (지식의 재투자)
- lint 작업으로 모순·고아 페이지·끊어진 링크 자동 감지

Karpathy 본인 시스템은 100여 페이지 / 40만 단어 규모로 컴파일됐고, 본인이 직접 쓴 부분은 없다고 한다. 즉 **"챗봇이 곧 위키 편집자"** 가 표준이 됐다.

한국 사례 — Abraham Song(brunch.co.kr/@abrahamsong/164) 가 가장 실증적이다. 텔레그램 봇에 Karpathy gist 를 던지고 → FastAPI + Vanilla JS 웹 위키 구축 → graphify 통합 → 멀티세션 디버깅으로 안정화. 비용은 월 $5. 결과적으로 "파편화된 지식을 신경계를 가진 통합 체계로" 전환했다고 표현한다. 텔레그램 봇이 쿼리 인터페이스, 백엔드는 graph.json 길찾기, 답변은 위키 페이지 인용으로 돌아오는 구조다.

### 4. Graphify + 챗봇 결합

graphify(`safishamsi/graphify`) 는 폴더(코드·문서·이미지·오디오) 를 → Tree-sitter AST + Leiden 클러스터링으로 → graph.json + 인터랙티브 HTML + 마크다운 audit report 로 변환한다.

세 가지 명령:
- **query**: "show the auth flow" 같은 의미 검색
- **path**: 두 개념 사이 최단 경로 (예: UserService ↔ DatabasePool)
- **explain**: 단일 노드의 상세 정보

엣지 신뢰도: EXTRACTED(직접 발견) / INFERRED(논리 추론) / AMBIGUOUS(수동 검토). INFERRED 엣지가 cross-cutting 답변을 만든다 — Abraham Song 사례의 "OAuth2 와 PaymentGateway 가 공유 유틸리티 함수로 연결" 같은 발견이 그것.

**토큰 절감 벤치마크**: 52파일 혼합 코퍼스에서 평균 질의당 ~1.7K 토큰(그래프) vs ~123K 토큰(원본 읽기) → **71.5×** 절감. 더 큰 셋업(456 Obsidian 노트 + 65+ 영구 노트)에서는 **499×** 절감 사례도 보고됐다. 사용자 vault 가 수백 페이지가 되면 이 절감은 곧장 비용·지연 차이로 환산된다.

### 5. Obsidian + AI 챗봇 통합 사례

**Smart Connections**(brianpetro/obsidian-smart-connections, 별 표 다수)
- 로컬 임베딩(TaylorAI/bge-micro-v2, 384차원) — vault 외부로 데이터 안 나감
- "context-aware" 청킹: 헤딩 + 파일경로 구조 기반
- Smart Chat 으로 노트 컨텍스트 위에서 LLM 대화. RAG 내부적으로 사용

**Khoj**(khoj-ai/khoj, 34K+ stars, AGPL-3.0, Y Combinator W24)
- "self-hostable AI second brain". 로컬·클라우드 LLM 모두 지원
- Browser/Obsidian/Emacs/Desktop/Phone/WhatsApp 다중 클라이언트
- markdown·org-mode·PDF·notion·이미지 동시 인덱싱

**Copilot for Obsidian**(logancyang/obsidian-copilot)
- Vault Q&A 기능 — 노트 전체 의미 검색
- OpenAI/Anthropic/Google/Ollama/LM Studio 다중 백엔드. Plus 는 자체 모델 내장
- Composer 모드로 노트 수정·생성

**Infio Copilot** — Cursor 영감의 자동완성 + 선택 노트 채팅

이들 공통 한계: **개인 사용자 vault 단일 사용자 가정**이다. 300인 사무소 같은 다중 사용자·RBAC·감사 환경에서는 그대로 못 쓴다 → 패턴 D 의 Gemini File Search 같은 엔터프라이즈 경로와의 결합이 필요하다.

### 6. GraphRAG / Knowledge Graph RAG 의 위치

**Microsoft GraphRAG**: 텍스트 코퍼스 → LLM 으로 엔티티·관계·claim 추출 → community 계층 구조 + community summary → Global Search(전체적 질문) / Local Search(특정 엔티티) / DRIFT Search(혼합) / Basic Search(기본 RAG) 4가지 모드. 인덱싱 비용이 큰 게 흠이지만 "시장은 어떤 트렌드인가?" 같은 holistic 질문에 강하다.

**LightRAG**(EMNLP2025, HKUDS): 듀얼 레벨(저수준/고수준) 그래프 검색 + 증분 업데이트. GraphRAG 대비 농업·CS·법률·혼합 도메인 모두 우위(농업 45.2% → 54.8%). 다양성에서 특히 강함. Neo4J 백엔드 지원.

**Neo4j + LLM**: 정형 그래프 DB 에 LLM 을 어시스턴트로 붙이는 전통 방식. 스키마가 명확한 도메인(BIM 객체 관계, 프로젝트-담당자-납품물 관계)에 적합.

**Wiki-Backed RAG 와의 위치**:
- GraphRAG = 자동 그래프 추출(인덱싱 비용↑)
- LLM Wiki = 인간·LLM 협업 큐레이션(인덱싱 0, 큐레이션 점진)
- graphify = 폴더(코드·문서) 자동 그래프(작은 인덱싱)
- 사무소 환경: **사내 매뉴얼·정책 = LLM Wiki, 프로젝트 코드/도면/표준 = graphify, 회의록·메일 같은 원시 대량 = GraphRAG/Vector RAG fallback** 의 하이브리드가 합리적

### 7. 위키 페이지 대용량 vs LLM 컨텍스트 한계

위키가 100, 1000, 10000페이지로 늘면? 상황별 전략:

- **~200페이지**: index.md(목차만) + 매칭된 N페이지 본문 직접 주입. Claude Sonnet 4.7(1M context) 에서는 위키 전체를 항상 주입해도 무방한 규모.
- **~1000페이지**: 임베딩 매칭으로 페이지 N개 선정 → 본문 + 직접 [[링크된]] 인접 페이지 1-2 hop 까지 주입.
- **~10000페이지+**: 계층 요약 필수. Karpathy 가 제안한 index.md 의 카테고리·요약 + 페이지별 frontmatter summary 만 1차 컨텍스트로, 요청 시 풀 본문 lazy-load.

**graphify 벤치마크의 정량 데이터** (재차 강조): 71.5×(표준), 499×(최적화) 토큰 절감. 사무소 환경에서 동일 질의가 일주일에 100번 반복된다고 가정하면, 절감액은 곧장 운영비로 환산된다.

**컨텍스트 절약 전략 정리**:
1. 요약 계층화: page.md → page.summary.md → category.summary.md → index.md
2. 위키링크 그래프로 lazy expand: 1 hop 만 항상, 2 hop 은 사용자 follow-up 시
3. 그래프 압축: graphify 의 graph.json 처럼 노드·엣지 표현이 본문 대비 평균 1/30~1/100
4. 캐시: 동일 페이지 컴파일 결과 + 동일 질의 답변 캐시. Smart Connections 가 semantic cache 를 운영하는 이유

### 8. 답변 품질 — 위키 기반의 강점

**환각 감소**: WikiChat(Stanford OVAL, EMNLP 2023, Wikimedia Research Award 2024) 이 정량 증거를 제공한다. Wikipedia 그라운딩 7단계 파이프라인으로 시뮬레이션 대화 97.3% factual accuracy, 인간 사용자와 최근 토픽 대화에서 97.9% — GPT-4 대비 55% 개선. 7B LLaMA 로 distill 해도 품질 유지.

**인용·근거 추적**: 위키 페이지 = URL/파일경로가 있는 정착물. 답변에 "[[페이지 ID]]" 를 그대로 인용하면 사용자가 클릭 한 번으로 검증. Vector RAG 의 "PDF 12쪽 3번째 청크" 같은 모호한 출처와 다르다.

**사용자 검증·심화**: 사내 매뉴얼 챗봇이 "방화구획 설계 기준은 ..." 답변 후 [[방화구획 설계 기준]] 링크를 주면 사용자는 페이지를 열고 인접한 [[피난계단 설계]], [[성능위주 설계 사례]] 까지 자연 탐색할 수 있다. 챗봇이 일회성이 아닌 "지식 베이스 입구" 가 된다.

**약점**: 위키에 없는 신규 자료(어제 받은 발주처 변경 통지) 는 처리 불가 → 패턴 D 의 RAG fallback 또는 "이 자료를 위키에 추가하라" 워크플로우 필요. Epsilla 의 지적처럼 마크다운 + grep 만으로는 RBAC·동시성·감사 한계 — 엔터프라이즈는 데이터베이스 백엔드 보강이 필수.

### 9. 운영·구현 디테일

**사용자 질문 → 위키 페이지 매칭 알고리즘**:
1. 키워드(BM25/FTS5): 가장 빠르고 직관적, 한국어 형태소 분석 필요
2. 임베딩(코사인 유사도): 의미 매칭, Smart Connections 처럼 로컬 모델로 처리 가능
3. 위키링크 BFS/DFS: 매칭된 페이지에서 1-2 hop 확장
4. LLM 라우터: 질문을 받아 "이 질문은 어떤 위키 카테고리에 해당?" 을 LLM 이 분류 (느리지만 정확)
5. 하이브리드(권장): BM25 + 임베딩 → RRF → CrossEncoder 재순위화 → LLM 라우터 게이트

engraph 가 정확히 이 5계층 융합을 구현한다.

**응답 생성 시 위키링크 자동 변환**: LLM 이 응답에 "방화구획 설계 기준" 텍스트를 쓸 때 → 위키 페이지 ID 사전(slug 매핑)을 system prompt 에 동봉 → "[[방화구획 설계 기준]]" 형태로 출력하도록 지시. 프론트엔드는 이 [[...]] 를 클릭 가능 링크로 렌더링.

**사용자가 페이지 보강 요청 시**: 챗봇 대화 중 "이 답변을 위키에 추가" 버튼 → LLM 이 (1) 적절한 페이지 위치 결정 (신규/기존 보강), (2) 마크다운 패치 작성, (3) 사람의 검토 후 git commit. Karpathy 가 강조한 "답변 자체가 새 위키 페이지가 된다" 의 구체적 구현.

**자료 분류 게이트키퍼 (민감도 기반 LLM 백엔드 라우팅)**: 사내 매뉴얼은 외부 LLM(Gemini/Claude API) 가능, 인사·계약 같은 민감 자료는 로컬 LLM(Ollama + Llama 3) 강제 → frontmatter 의 `sensitivity: confidential` 필드를 보고 라우팅. 300인 규모 사무소에서는 필수.

### 10. 위키-챗봇 통합 시스템의 구체적 코드 패턴

가장 가까운 참고 구현:
- **lucasastorian/llmwiki**: Next.js + FastAPI + SQLite + MCP, Apache 2.0
- **devwhodevs/engraph**: 5계층 하이브리드 검색 + MCP + ChatGPT Actions 연동
- **kytmanov/obsidian-llm-wiki-local**: 100% 로컬 Ollama, vault 자동 링크
- **Ar9av/obsidian-wiki**: AI agent 가 Obsidian 위키 빌드·유지
- **Astro-Han/karpathy-llm-wiki**: Agent Skills 호환 wiki + lint
- **nashsu/llm_wiki**: 크로스플랫폼 데스크톱 앱

코드 패턴은 아래 별도 섹션 참조.

---

## 4가지 통합 아키텍처 패턴 비교 표

| 패턴 | 검색 방식 | 컨텍스트 주입 | 위키 규모 적합 | 구현 난이도 | 사용자 vault 매핑 |
|------|----------|---------------|----------------|-------------|------------------|
| **A. Direct-Inject** | 키워드 매칭 | 매칭 페이지 본문 그대로 | ~200 페이지 | 낮음 (FastAPI + 마크다운 파서 + LLM 호출) | kouwiki 초기 단계 |
| **B. Wiki + Graph** | graphify query/path/explain | 페이지 본문 + 그래프 경로 | 중규모, cross-cutting 강 | 중 (graphify 빌드 + JSON 쿼리) | Abraham Song wiki.ai-biz.app 동일 |
| **C. Hybrid Vector + Wikilink** | 임베딩 + BM25 + 위키링크 그래프 + CrossEncoder | 매칭 + 인접 hop | 1000+ 페이지 | 중상 (engraph 활용) | 중장기 확장형 |
| **D. Wiki 1차 + RAG Fallback** | 위키 매칭 → 실패 시 Gemini File Search | 위키 페이지 우선, 미스 시 PDF 청크 | 위키+대규모 PDF 코퍼스 병존 | 중 (라우터 1줄) | **현 환경 최적** |

---

## 사례 모음

### Abraham Song — wiki.ai-biz.app (한국 / Brunch @abrahamsong/164)

가장 실증적 한국 사례. 텔레그램 봇에 Karpathy gist 전달 → FastAPI + Vanilla JS 웹 위키 → graphify 통합 → 타임아웃 세분화(쿼리 10s, 경로 30s, 재빌드 300s) 로 안정화. 멀티모달 입력(GitHub URL, 스크린샷, 유튜브) 통합 처리. **월 $5 운영비**. 핵심 인사이트: "LLM 을 검색 엔진처럼이 아닌 사서처럼 다룬다" — Graphify 가 backend 분석, LLM Wiki 가 frontend 정착.

### Andrej Karpathy — 원본 gist (2026.04)

100여 페이지 / 40만 단어 규모로 컴파일. 본인이 직접 쓴 부분 없음. 3계층(raw/wiki/schema) + index.md + log.md 가 표준. "Obsidian = IDE, LLM = 프로그래머, wiki = 코드베이스" 비유.

### lucasastorian/llmwiki — 오픈소스 풀스택

Next.js + FastAPI + SQLite + MCP. `llmwiki init ~/research` 한 줄로 시작. PDF/마크다운/엑셀 자동 색인 → Claude Desktop 연결 → 자동 위키 작성. 워크스페이스 = MCP 서버 1:1 매핑. Apache 2.0.

### kytmanov/obsidian-llm-wiki-local — 100% 로컬

Ollama 만 사용. 마크다운 노트 drop → AI 가 개념 추출 → Obsidian 위키 자동 링크·성장. "Zero sharing. Your notes stay yours." 보안이 중요한 도메인(인사·법무·계약) 에 적합.

### devwhodevs/engraph — 5계층 하이브리드

Obsidian vault → SQLite(파일·청크·FTS5·벡터·위키링크 그래프) → MCP/HTTP REST(25 도구·26 엔드포인트). 5계층 검색을 RRF 로 융합. ChatGPT Actions 연동까지(클라우드플레어 터널). "Sarah Chen 노트가 인증 시스템 검색에서 그래프 계층 덕에 등장" 같은 발견.

### Smart Connections — Obsidian 표준 플러그인

로컬 임베딩(bge-micro-v2 384d). `.smart-env/` 에 인덱스 저장. context-aware 청킹(헤딩 기반). Smart Chat 으로 노트 위에서 직접 대화. 무료·로컬 우선. 사용자 vault 도입 시 가장 빠른 1단계.

### Khoj — Self-hosted Second Brain

34K+ stars, AGPL-3.0, Y Combinator W24. 로컬·클라우드 LLM 모두 지원. Browser/Obsidian/Emacs/Desktop/Phone/WhatsApp 다중 클라이언트. 사무소 직원 누구나 어디서든 사내 위키 질의 가능.

### WikiChat — 학술 사례 (Stanford OVAL, EMNLP 2023)

Wikipedia 그라운딩 7단계 파이프라인. **시뮬레이션 97.3%, 인간 대화 97.9% factual accuracy** (GPT-4 대비 55% 개선). 7B LLaMA 로 distill 해도 품질 유지. **위키 그라운딩이 환각을 정량적으로 잡는다는 증거**.

### Microsoft GraphRAG / LightRAG — 자동 그래프 추출

GraphRAG: 4가지 검색 모드(Global/Local/DRIFT/Basic). LightRAG: 듀얼 레벨, GraphRAG 대비 4도메인 우위. 인덱싱 비용 큼. 위키 큐레이션이 어려운 대규모 비정형 코퍼스에 적합 (회의록 10년치, 메일 수만건).

---

## 도구 생태계 비교 표

| 도구 | 카테고리 | 백엔드 | 위치 | 챗봇 통합 | 라이선스 | 사무소 도입 점수 |
|------|---------|--------|------|----------|----------|-----------------|
| **Smart Connections** | Obsidian 플러그인 | 로컬 임베딩(bge-micro-v2) | vault 내부 | Smart Chat 내장 | 오픈/Pro | 개인용 ★★★★ |
| **Copilot for Obsidian** | Obsidian 플러그인 | OpenAI/Anthropic/Google/Ollama | vault 내부 | Vault Q&A 내장 | 오픈/Plus | 개인용 ★★★★ |
| **Khoj** | Self-hosted | LLM 다중 백엔드 | 별도 서버 | 다중 클라이언트 | AGPL-3.0 | 사무소 전사 ★★★★★ |
| **graphify** | Skill / CLI | Tree-sitter + Leiden | 폴더 분석 | LLM 어시스턴트 + graph.json | 오픈 | 코드/문서 ★★★★ |
| **engraph** | 로컬 그래프 + MCP | SQLite + llama.cpp | vault → MCP/HTTP | MCP/ChatGPT Actions | 오픈 | 중급자용 ★★★★ |
| **lucasastorian/llmwiki** | Karpathy 풀스택 | FastAPI + SQLite + MCP | 워크스페이스 | Claude Desktop MCP | Apache 2.0 | 풀스택 모범 ★★★★ |
| **Microsoft GraphRAG** | KG-RAG 프레임워크 | Python + LLM 추출 | 코퍼스 분석 | API 호출 | MIT | 대규모 비정형 ★★★ |
| **LightRAG** | KG-RAG 프레임워크 | Python + Neo4j 옵션 | 코퍼스 분석 | API 호출 | EMNLP 2025 | 대규모 비정형 ★★★ |
| **Gemini File Search** | Managed RAG (현 사용 중) | Google 호스팅 | 클라우드 | API 호출 | 상용 | 사내 매뉴얼 RAG 현역 ★★★★ |
| **WikiChat** | 학술 grounding | Wikipedia + LLM | 별도 시스템 | 7단계 파이프라인 | 학술 코드 | 환각 감소 참고 ★★ |

---

## 코드 패턴 예시 (FastAPI + 마크다운 파서 + LLM)

가장 단순한 **패턴 A (Direct-Inject)** 의 미니 구현. 사용자의 kouwiki vault 를 즉시 챗봇으로 띄우는 코드 골격.

```python
# wiki_chatbot.py
# 로직 요약:
# 1. wiki/ 폴더의 .md 파일들을 frontmatter + 본문으로 파싱
# 2. 사용자 질문에 대해 BM25 + 위키링크 그래프 매칭
# 3. 매칭된 페이지 본문 + 1-hop 인접 페이지를 LLM 컨텍스트로 주입
# 4. LLM 응답에서 [[페이지명]] 자동 링크 변환
# 5. 답변 품질 낮으면 Gemini File Search 로 fallback (패턴 D)

import os
import re
from pathlib import Path
import frontmatter
from rank_bm25 import BM25Okapi
from anthropic import Anthropic  # 또는 google.generativeai
from fastapi import FastAPI
from pydantic import BaseModel
import logging

# 해마 CI 컬러 (#F58220) 는 프론트엔드 CSS 에서 적용
logging.basicConfig(level=logging.INFO, format="%(asctime)s [%(levelname)s] %(message)s")
log = logging.getLogger("wiki_chatbot")

WIKI_DIR = Path(os.environ["KOUWIKI_PATH"])  # 예: c:/Users/key/Desktop/kouwiki/kouwiki/wiki
client = Anthropic(api_key=os.environ["ANTHROPIC_API_KEY"])
app = FastAPI()

# --- 1. 위키 인덱스 로드 ---------------------------------------------------
class WikiPage:
    def __init__(self, path: Path):
        post = frontmatter.load(path)
        self.path = path
        self.id = path.stem  # slug
        self.title = post.metadata.get("title", path.stem)
        self.sensitivity = post.metadata.get("sensitivity", "internal")
        self.body = post.content
        # 위키링크 추출: [[페이지명]] 또는 [[페이지명|alias]]
        self.links = re.findall(r"\[\[([^\]\|]+)(?:\|[^\]]+)?\]\]", self.body)

def load_wiki() -> dict[str, WikiPage]:
    pages = {}
    for md in WIKI_DIR.rglob("*.md"):
        try:
            page = WikiPage(md)
            pages[page.id] = page
        except Exception as e:
            log.warning("페이지 로드 실패 %s: %s", md, e)
    log.info("위키 페이지 %d개 로드 완료", len(pages))
    return pages

PAGES = load_wiki()
CORPUS = [p.body.split() for p in PAGES.values()]
PAGE_IDS = list(PAGES.keys())
BM25 = BM25Okapi(CORPUS)

# --- 2. 질문 → 페이지 매칭 -------------------------------------------------
def match_pages(question: str, top_k: int = 3) -> list[WikiPage]:
    scores = BM25.get_scores(question.split())
    ranked = sorted(zip(PAGE_IDS, scores), key=lambda x: x[1], reverse=True)
    return [PAGES[pid] for pid, _ in ranked[:top_k]]

def expand_one_hop(seeds: list[WikiPage]) -> list[WikiPage]:
    """매칭된 페이지에서 [[링크된]] 인접 페이지를 1단계 확장."""
    visited = {p.id for p in seeds}
    expanded = list(seeds)
    for seed in seeds:
        for link in seed.links:
            slug = link.replace(" ", "_").lower()  # vault 규칙 맞춰 매핑
            if slug in PAGES and slug not in visited:
                expanded.append(PAGES[slug])
                visited.add(slug)
    return expanded

# --- 3. LLM 호출 ----------------------------------------------------------
SYSTEM_PROMPT = """당신은 해마건축사사무소의 사내 위키 챗봇이다.
아래 [WIKI_CONTEXT] 페이지만을 사실 근거로 답하라.
모르면 "위키에 정착되지 않은 내용입니다" 라고 답하라.
응답 중 다른 페이지 언급 시 반드시 [[페이지ID]] 형태로 인용하라.
"""

def build_context(pages: list[WikiPage]) -> str:
    blocks = []
    for p in pages:
        blocks.append(f"### {p.title} (id: {p.id})\n{p.body}\n")
    return "\n---\n".join(blocks)

def ask_llm(question: str, pages: list[WikiPage]) -> str:
    ctx = build_context(pages)
    try:
        msg = client.messages.create(
            model="claude-sonnet-4-7",
            max_tokens=1024,
            system=SYSTEM_PROMPT,
            messages=[{
                "role": "user",
                "content": f"[WIKI_CONTEXT]\n{ctx}\n\n[질문]\n{question}",
            }],
        )
        return msg.content[0].text
    except Exception as e:
        log.exception("LLM 호출 실패")
        return f"오류: {e}"

# --- 4. 응답 후처리: [[링크]] 를 클릭 가능한 URL 로 ---------------------------
def linkify(answer: str, base_url: str = "/wiki") -> str:
    return re.sub(
        r"\[\[([^\]\|]+)(?:\|([^\]]+))?\]\]",
        lambda m: f"[{m.group(2) or m.group(1)}]({base_url}/{m.group(1).replace(' ', '_').lower()})",
        answer,
    )

# --- 5. FastAPI 엔드포인트 -------------------------------------------------
class Question(BaseModel):
    text: str

@app.post("/ask")
def ask(q: Question):
    seeds = match_pages(q.text, top_k=3)
    if not seeds:
        # 패턴 D: Gemini File Search fallback (별도 함수, 생략)
        return {"answer": "위키 매칭 실패 — 사내 매뉴얼 RAG 로 fallback 권장", "sources": []}
    expanded = expand_one_hop(seeds)
    raw_answer = ask_llm(q.text, expanded)
    final = linkify(raw_answer)
    return {
        "answer": final,
        "sources": [{"id": p.id, "title": p.title} for p in expanded],
    }
```

**graphify graph.json 활용 패턴 (패턴 B)**:

```python
# graph_query.py — Abraham Song 패턴 핵심 부분 발췌
import json
from pathlib import Path

GRAPH = json.loads(Path("graphify-out/graph.json").read_text(encoding="utf-8"))

def find_path(src: str, dst: str, max_hops: int = 4) -> list[str]:
    """src 노드에서 dst 노드까지 BFS — graphify path 명령의 미니 구현."""
    from collections import deque
    adj = {n["id"]: [e["to"] for e in GRAPH["edges"] if e["from"] == n["id"]]
           for n in GRAPH["nodes"]}
    queue = deque([(src, [src])])
    visited = {src}
    while queue:
        node, path = queue.popleft()
        if node == dst:
            return path
        if len(path) >= max_hops:
            continue
        for nb in adj.get(node, []):
            if nb not in visited:
                visited.add(nb)
                queue.append((nb, path + [nb]))
    return []

def explain_node(node_id: str) -> dict:
    """단일 노드의 메타·이웃·신뢰도(EXTRACTED/INFERRED) 반환."""
    node = next((n for n in GRAPH["nodes"] if n["id"] == node_id), None)
    if not node:
        return {}
    edges = [e for e in GRAPH["edges"] if node_id in (e["from"], e["to"])]
    return {
        "node": node,
        "edges": edges,
        "inferred_count": sum(1 for e in edges if e.get("confidence") == "INFERRED"),
    }
```

**위키링크 → 페이지 ID 매핑 사전**:

```python
# slug_map.py
def build_slug_map(pages: dict) -> dict[str, str]:
    """[[방화구획 설계 기준]] → page_id 'bangwha_gugeok_seolgye_gijun' 같은 매핑."""
    slug_map = {}
    for pid, page in pages.items():
        slug_map[page.title] = pid
        for alias in page.path.read_text().split("\n"):  # frontmatter aliases
            if alias.startswith("alias:"):
                slug_map[alias.split(":", 1)[1].strip()] = pid
    return slug_map
```

---

## 권장 통합 아키텍처 (300인 사무소용)

사용자의 현 자산을 그대로 활용한 **단계적 도입 로드맵**:

### Phase 0 (현재 상태)
- Gemini File Search 기반 사내 매뉴얼 챗봇 운영 중
- 개인 kouwiki vault 가 Karpathy 패턴으로 큐레이션 중

### Phase 1 — 패턴 A 로 kouwiki 챗봇화 (1-2주)
- 위 코드 골격 그대로 FastAPI 1대 띄움
- vault 200페이지 미만이면 index.md + 매칭 페이지 직접 주입
- 사용자(IT 책임자) 본인이 매일 사용해 답변 품질·UX 검증
- Sensitivity 메타 도입: `internal/confidential/public` 3단계

### Phase 2 — 패턴 D: Gemini File Search 와 결합 (2-4주)
- 라우터 추가: 질문 → kouwiki 매칭 점수 임계치 미만이면 → Gemini File Search 호출
- 답변 형식 통일: 출처가 [[위키 페이지]] 또는 [PDF 파일명, p.12] 둘 다 가능
- 사내 매뉴얼 챗봇 사용자에게 "이 답을 위키에 정착시키시겠습니까?" 액션 제공
- 인사·계약 같은 confidential 페이지는 Gemini API 우회, 로컬 Ollama 라우팅

### Phase 3 — graphify 통합 (1개월)
- vault 전체에 `/graphify .` 적용 → graph.json + HTML 시각화 + audit report 생성
- 챗봇 백엔드에 graphify path/query 도입 → cross-cutting 질문 처리
- "이 프로젝트 표준은 어느 법규에서 파생됐나?" 같은 질문에 강해짐
- 71.5×~499× 토큰 절감 효과 측정 (베이스라인: 패턴 A 대비)

### Phase 4 — 사무소 전사 배포 (2-3개월)
- Khoj 또는 lucasastorian/llmwiki 기반 멀티 사용자 서버화
- RBAC: 본부별·직급별 sensitivity 접근 제어
- 감사 로그(ClawTrace 패턴): 누가 언제 어떤 질의·어떤 페이지를 봤는지 불변 로그
- 다우오피스 그룹웨어 SSO 연동
- 시놀로지 NAS 70TB 의 일부를 vault 미러링 백업

### Phase 5 — 자동 보강 워크플로우 (지속)
- 답변 자체가 새 위키 페이지가 됨 (Karpathy 원칙)
- 정기 lint: 모순·고아·누락 링크 → IT 책임자에게 알림
- 직원이 챗봇에게 "이 자료를 위키에 추가" 요청 → LLM 이 패치 PR → 사람이 검토 → git commit
- 매월 graphify 재빌드 → "신 노드(God Node)" 변화 추적 → 지식 핵심 변화 감지

### 데이터 분류 게이트키퍼 패턴
```
질문 도착
  → frontmatter sensitivity 체크
     - public/internal     → Claude Sonnet API (kouwiki) + Gemini File Search (사내 매뉴얼)
     - confidential        → 로컬 Ollama + 로컬 vault 만
     - secret(예: 임원 인사) → 챗봇 답변 차단, "권한 필요" 응답
```

### 비용·운영 추정
- Phase 1-2: 월 $20-50 (LLM API + 작은 VPS)
- Phase 3-4: 월 $200-500 (다중 사용자 + 임베딩 인프라 + 백업)
- 절감: 사내 정보 검색 시간 1인당 주 1시간 절감 가정 시 300명 × 4주 × 1시간 = **월 1,200시간 절감**, 환산 시 LLM 비용을 압도

---

## 출처 (URL + 수집일)

모두 2026-05-06 수집.

**Karpathy LLM Wiki 원본 / 해설**
- [llm-wiki gist (Karpathy 원본)](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
- [What Is Andrej Karpathy's LLM Wiki? — MindStudio](https://www.mindstudio.ai/blog/andrej-karpathy-llm-wiki-knowledge-base-claude-code)
- [Beyond RAG: Karpathy's LLM Wiki Pattern — Level Up Coding](https://levelup.gitconnected.com/beyond-rag-how-andrej-karpathys-llm-wiki-pattern-builds-knowledge-that-actually-compounds-31a08528665e)
- [LLM Wiki: Karpathy's 3-Layer Pattern — DecodeTheFuture](https://decodethefuture.org/en/llm-wiki-karpathy-pattern/)
- [LLM Wiki Tutorial — Data Science Dojo](https://datasciencedojo.com/blog/llm-wiki-tutorial/)

**Wiki vs RAG 비교**
- [LLM Wiki vs RAG: A Decision Framework — MindStudio](https://www.mindstudio.ai/blog/llm-wiki-vs-rag-knowledge-base)
- [Karpathy's LLM Wiki: 95% Less Token Use Than RAG — MindStudio](https://www.mindstudio.ai/blog/llm-wiki-vs-rag-markdown-knowledge-base-comparison)
- [When Your AI Wiki Outgrows the Context Window — DEV.to](https://dev.to/zaferdace/when-your-ai-wiki-outgrows-the-context-window-a-practical-guide-to-rag-kc2)
- [Why Karpathy is Right: RAG is Dead, Long Live the Agentic Wiki — Epsilla](https://www.epsilla.com/blogs/karpathy-agentic-wiki-beyond-rag-enterprise-memory)

**Abraham Song / 한국 사례**
- [LLM Wiki + Graphify를 직접 만들다 — Brunch @abrahamsong/164](https://brunch.co.kr/@abrahamsong/164)

**Graphify**
- [safishamsi/graphify GitHub](https://github.com/safishamsi/graphify)
- [Graphify 공식 사이트](https://graphify.net/)
- [From Karpathy's LLM Wiki to Graphify — AnalyticsVidhya](https://www.analyticsvidhya.com/blog/2026/04/graphify-guide/)
- [Graphify for Claude Code: 70x Cuts — MindStudio](https://www.mindstudio.ai/blog/graphify-claude-code-knowledge-graph-large-codebase-70x)
- [Optimizing Token Usage with Graphify — Medium](https://abhishek-iiit.medium.com/optimizing-token-usage-with-graphify-using-knowledge-graphs-31afd57a8a21)
- [claude-code-memory-setup (499x 보고)](https://github.com/lucasrosati/claude-code-memory-setup)

**Obsidian 통합**
- [How I Took Karpathy's LLM Wiki to Obsidian — AIMaker Substack](https://aimaker.substack.com/p/llm-wiki-obsidian-knowledge-base-andrej-karphaty)
- [Smart Connections GitHub](https://github.com/brianpetro/obsidian-smart-connections)
- [Smart Connections 공식](https://smartconnections.app/)
- [Copilot for Obsidian GitHub](https://github.com/logancyang/obsidian-copilot)
- [Khoj GitHub](https://github.com/khoj-ai/khoj)
- [Khoj Obsidian 클라이언트 문서](https://docs.khoj.dev/clients/obsidian/)
- [obsidian-llm-wiki-local — kytmanov](https://github.com/kytmanov/obsidian-llm-wiki-local)
- [obsidian-wiki — Ar9av](https://github.com/Ar9av/obsidian-wiki)
- [karpathy-llm-wiki — Astro-Han](https://github.com/Astro-Han/karpathy-llm-wiki)
- [engraph — devwhodevs](https://github.com/devwhodevs/engraph)
- [obsidianGraphRAG — Jinstronda](https://github.com/Jinstronda/obsidianGraphRAG)

**오픈소스 LLM Wiki 구현**
- [llmwiki — lucasastorian](https://github.com/lucasastorian/llmwiki)
- [llm_wiki — nashsu](https://github.com/nashsu/llm_wiki)
- [LLM Wiki 공식 사이트](https://llm-wiki.app/)

**GraphRAG / KG-RAG**
- [Microsoft GraphRAG GitHub](https://github.com/microsoft/graphrag)
- [Microsoft GraphRAG Docs](https://microsoft.github.io/graphrag/)
- [LightRAG GitHub (HKUDS, EMNLP2025)](https://github.com/HKUDS/LightRAG)
- [LightRAG Paper arXiv](https://arxiv.org/abs/2410.05779)
- [GraphRAG vs Vector — MachineLearningMastery](https://machinelearningmastery.com/vector-databases-vs-graph-rag-for-agent-memory-when-to-use-which/)
- [GraphRAG Explained — Zilliz](https://medium.com/@zilliz_learn/graphrag-explained-enhancing-rag-with-knowledge-graphs-3312065f99e1)

**WikiChat (학술)**
- [WikiChat arXiv 2305.14292](https://arxiv.org/abs/2305.14292)
- [stanford-oval/WikiChat GitHub](https://github.com/stanford-oval/WikiChat)
- [WikiChat at Stanford HAI](https://hai.stanford.edu/research/wikichat-stopping-the-hallucination-of-large-language-model-chatbots-by-few-shot-grounding-on-wikipedia)

**Gemini File Search (현 사용 중인 RAG)**
- [Beyond RAG: Gemini File Search Deep Dive — RAGyfied](https://ragyfied.com/articles/what-is-gemini-file-search-tool)
- [Google File Search Tool Tutorial — DataCamp](https://www.datacamp.com/tutorial/google-file-search-tool)

**한국어 사내 챗봇 / RAG 사례**
- [RAG 기반 사내 지식 챗봇 구축 — 요즘IT](https://yozm.wishket.com/magazine/)
- [기업용 LLM+RAG 챗봇 도입 가이드 — Skelter Labs](https://www.skelterlabs.com/blog/enterprise-llm-and-rag)
- [RAG 사내 규정 챗봇 프로젝트 — velog @bytee](https://velog.io/@bytee/RAG-%EC%82%AC%EB%82%B4-%EA%B7%9C%EC%A0%95-%EC%B1%97%EB%B4%87-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EA%B0%9C%EC%9A%94%EC%99%80-%EC%84%B8%ED%8C%85)

**FastAPI / 마크다운 챗봇 코드 참고**
- [LLM-Chatbot-with-FastAPI-and-Streamlit](https://github.com/samkitkankariya/LLM-Chatbot-with-FastAPI-and-Streamlit)
- [llm-fastapi-example (Hugging Face + LlamaIndex)](https://github.com/yksnilowyrahcaz/llm-fastapi-example)
- [rag_api (FastAPI + LangChain)](https://github.com/AshishSinha5/rag_api)
- [Ollama & FastAPI for Local Markdown Automations — GreenFlux](https://blog.greenflux.us/ollama-and-fastapi-for-local-markdown-automations/)
