# 한국어 LLM 스택 — 임베딩·형태소·LLM 모델·HWP 처리

[[사내_챗봇_시스템]] 과 [[Wiki_Backed_Chatbot]] 의 공통 인프라. 한국어 환경의 RAG / 챗봇 / 위키에서 결과 품질을 좌우하는 4계층(형태소·임베딩·LLM·문서 파서)별 SOTA 도구·모델 정리. 2026-05-06 기준.

## 4계층 한국어 스택 (SOTA + 실무 권장)

| 계층 | 1순위 | 대안 | 비고 |
|------|-------|------|------|
| **형태소 분석** | **Kiwi** | MeCab-ko, Khaiii | BM25 가드레일 필수 |
| **임베딩** | **KURE-v1** (BGE-M3 기반) | OpenAI text-embedding-3-large, BGE-M3 | KURE 가 MTEB-ko 상위권 |
| **LLM (한국어 SOTA)** | **Solar Pro 2 / HyperCLOVA X** | GPT-4o, Claude Sonnet, Gemini 1.5/2.5 | 민감 자료는 국내 |
| **문서 파서 (HWP·PDF)** | **Kordoc** | Deep Parser, Upstage Document AI | LangChain 기본 미지원 |

## 1. 형태소 분석기 — Kiwi 가 신규 표준

한국어는 교착어 → BM25 같은 키워드 검색에 형태소 분석 필수. 본 위키 기존 [[건강검진|lint]] 의 한글 BM25 가드레일도 Kiwi 가 자연스러움.

| 분석기 | 속도 | 정확도 | 라이센스 | 비고 |
|--------|------|--------|----------|------|
| MeCab-ko | 최고 | 양호 | LGPL | 사전 기반 |
| **Kiwi** | 빠름 | **86.7%** (모호성 해소) | LGPL | 딥러닝 결합·Pythonic |
| Khaiii | 중간 | 양호 | Apache 2 | 카카오, 데이터 기반 |
| Okt | 느림 | 보통 | Apache 2 | 트위터 출신 |
| Kkma | 최저 | 정확 | GPL | 학술용 |

**대부분 신규 프로젝트 Kiwi 첫 선택**. GitHub: <https://github.com/bab2min/Kiwi>

## 2. 한국어 임베딩 — KURE-v1 이 SOTA

OpenAI text-embedding-3-large 로 시작해도 무방하지만 비용·데이터 보호·정확도 면에서 **고려대 NLP 연구실 KURE-v1** (BGE-M3 기반·약 200만 한국어 학습) 가 MTEB-ko 리더보드 상위권. `dragonkue/BGE-m3-ko` 와 `BAAI/bge-m3` 보다 모든 평가 지표에서 우수.

| 모델 | 차원 | 운영 | 한국어 | 비용 |
|------|------|------|--------|------|
| OpenAI text-embedding-3-large | 3072 | API | 양호 | $0.13/1M tokens |
| BGE-M3 | 1024 | Self-host | 매우 우수 | 인프라만 |
| **KURE-v1** | 1024 | Self-host | **국내 SOTA** | 인프라만 |
| KoE5 | 1024 | Self-host | 우수 (E5 기반) | 인프라만 |
| KoSimCSE-RoBERTa | 768 | Self-host | 짧은 문장 강세 | 인프라만 |

**본 사무소 적용**: GPU 없으므로 KURE-v1 자체 호스팅 시 CPU 임베딩(초당 수십 문장) — 인덱싱 단계는 충분, **실시간 쿼리 임베딩만 OpenAI API** 로 보내는 하이브리드도 충분히 가능. 매뉴얼 PDF 한 번 인덱싱 후 증분 업데이트만이라 GPU 부재 결정적 제약 아님.

GitHub: <https://github.com/nlpai-lab/KURE>

## 3. 한국어 LLM — 80/15/5 라우팅

2025 한국어 챗봇 아레나 **Gemini 3 시리즈가 1~3위 독점**. KMMLU 기준 GPT-4o(81.1) > SKT A.X 4.0(78.3) > Solar Pro 2(31B로 Claude 4 Sonnet급).

| 모델 | 한국어 품질 | 비용 (입력 1M) | 데이터 보호 | 본 사무소 용도 |
|------|------------|---------------|-------------|---------------|
| **Gemini 1.5 Flash** | 우수 | $0.075 | API+Vertex 지역 | **80% 일반 질의** |
| **Claude Sonnet 4.x** | 매우 우수 | $3 | API+Bedrock | **15% 복합 추론·법규** |
| **Solar Pro 2 (31B)** | 매우 우수 | **$0.5** | API+온프레 옵션 | **5% 민감 정보** |
| HyperCLOVA X | 매우 우수 (문체 SOTA) | API 기준 | NCP 국내 리전 | 5% 대체 (한국어 카피) |
| GPT-4o | 매우 우수 (KMMLU 81.1) | $2.50 | API | 균형 1위 |
| Gemini 2.5 Pro | 매우 우수 | $1.25 | Vertex AI | 한국어 자연스러움 |
| A.X 4.0 (SKT) | 우수 (KMMLU 78.3) | 협의 | 국내 | 통신 도메인 |
| Kanana (Kakao) | 우수 | 협의 | 국내 | 카카오 생태계 |
| Llama 3.x 70B (Ollama) | 보통 (한국어 fine-tune 필요) | 인프라만 | 100% 온프레 | GPU 부재 시 부적합 |
| **Qwen 3 9B/32B** | 우수 | 인프라만 | 100% 온프레 | **온프레 1순위** (CPU 9B 가능) |
| Gemma 2 9B | 보통 | 인프라만 | 100% 온프레 | 라이센스 우호적 |

**Solar Pro 2 의 의미**: 2025.12 조달청 "생성형 AI 업무지원 서비스" 1호 공급사. **공공 발주 비중 높은 본 사무소**에는 데이터 보호 + 가성비 + 한국어 SOTA 가 모두 충족되는 거의 유일 옵션. → [[사내_챗봇_시스템|9개 한국 사례]] 의 사례 H 참조.

**Hybrid LLM Routing (Microsoft, ICLR 2024)**: 라우터가 쿼리 난이도 판별 → 단순은 작은/저렴, 복잡은 큰 모델 → **AI 비용 39% 절감 + 단순 쿼리 지연 32-38% 단축**. 본 사무소 80/15/5 라우팅이 이 패턴의 직접 적용.

## 4. 문서 파서 — HWP 가 핵심 결정 변수

LangChain·LlamaIndex 모두 HWP 기본 미지원. 한국 기업의 사내 문서는 매뉴얼·취업규칙·계약서 다수가 HWP → **한국어 RAG 의 숨은 결정 변수**.

| 파서 | 지원 형식 | 강점 | 라이센스 |
|------|-----------|------|----------|
| **Kordoc** | HWP·PDF·DOCX → Markdown | 한국어 특화·표·수식 인식 | OSS |
| Deep Parser | PDF·HWP·이미지 | 한국딥러닝 자체 | 상용/일부 무료 |
| Upstage Document AI | PDF·이미지·표·차트 | 표·다단 레이아웃 SOTA | API 과금 |
| Naver CLOVA OCR | 이미지·PDF | 한국어 OCR 정확 | API 과금 |
| Mistral OCR / Datalab Marker | PDF·이미지 | 2026 글로벌 표준 | OSS/상용 |

**본 사무소 적용**: 매뉴얼·취업규칙·근로기준법 등 11건 PDF 는 현 [[사내_챗봇_시스템|haema-knowledge-base]] 가 처리 중이지만, 향후 사내 hwp 문서 ingest 시 **Kordoc** 도입 거의 필수. GitHub: <https://github.com/chrisryugj/kordoc>

## Hybrid Search 파이프라인 (한국어 표준)

본 4계층을 결합한 한국어 RAG 표준 파이프라인:

```
1. PDF/HWP/DOCX → Kordoc (또는 Deep Parser) → Markdown 변환
2. Kiwi → BM25 인덱스 생성
   KURE-v1 → dense 임베딩
3. Hybrid Search (RRF) → 상위 N건
4. BAAI/bge-reranker-v2-m3 (또는 한국어 특화 reranker) → 상위 5건 재정렬
5. LLM 프롬프트에 컨텍스트 + 출처 메타데이터 주입 (80/15/5 라우팅)
```

이게 2026 년 5월 기준 한국어 사내 챗봇의 baseline. 본 위키 [[CLAUDE|운영규칙]] 의 "[[위키링크]] 인용" 도 5단계 LLM 프롬프트에서 시스템 프롬프트로 강제 가능.

## 본 사무소 도입 우선순위

| 우선 | 도구 | 도입 시점 | 비용 |
|------|------|-----------|------|
| **1** | Kiwi (BM25 토크나이저) | 즉시 (Phase 1) | 0 |
| **2** | Kordoc (HWP 처리) | 사내 hwp ingest 발생 시 | 0 |
| **3** | OpenAI text-embedding-3-small (시작) | Phase 1 | API |
| **4** | KURE-v1 자체 호스팅 (전환) | Phase 2 (비용 임계 도달 시) | 인프라만 |
| **5** | Hybrid LLM Routing (80/15/5) | Phase 2 | API 절감 효과 |
| **6** | Solar Pro 2 (민감 자료) | Phase 2 후반 | $0.5/1M |
| **7** | bge-reranker-v2-m3 (재순위화) | Phase 2 후반 | 인프라만 |

## 한국어 RAG 평가·벤치마크

- **MTEB-ko 리더보드** (su-park/mteb_ko_leaderboard): 한국어 임베딩 모델 표준 벤치마크
- **Korean Embedding Benchmark** (ssisOneTeam): 한국어 검색기용 평가 셋
- **KMMLU**: 한국어 다지선다 평가 (GPT-4o 81.1, A.X 4.0 78.3, Solar Pro 2 ~70대)
- **Elice 벤치마크**: 국산 LLM 6종 비교

본 사무소 챗봇 도입 시 RAGAS + KMMLU subset (사내 매뉴얼 도메인) 자체 평가셋 구축 권장.

## 출처
- raw/2026-05-06_corporate_chatbot_development_research.md (한국어 스택 섹션 발췌)
- raw/2026-05-06_wiki_backed_chatbot_research.md (한국어 사례 부분)
- 핵심 GitHub:
  - Kiwi: <https://github.com/bab2min/Kiwi>
  - KURE: <https://github.com/nlpai-lab/KURE>
  - Kordoc: <https://github.com/chrisryugj/kordoc>
  - MTEB-ko: <https://github.com/su-park/mteb_ko_leaderboard>
- 벤치마크·해설:
  - MSAP 국내 LLM 현황: <https://www.msap.ai/blog-home/blog/korea-llm/>
  - Elice 국산 LLM 6종: <https://elice.io/ko/resources/blog/llm-benchmark-korea-elice>
  - Data Dynamics 임베딩 가이드: <https://www.data-dynamics.io/ko/blog/embedding-model-guide>
  - GeekNews 한국어 RAG BGE-M3: <https://news.hada.io/topic?id=18603>
- Microsoft Hybrid LLM (ICLR 2024): <https://arxiv.org/abs/2404.14618>
- 수집일: 2026-05-06

## 관련
- [[사내_챗봇_시스템]] — 본 스택을 채택하는 챗봇 시스템 전반
- [[Wiki_Backed_Chatbot]] — 본 스택이 위키 챗봇에서 활용되는 방식
- [[자동화_시나리오]] — 본 스택을 활용하는 사내 자동화 시나리오
- [[정보_일원화_패턴]] — Hybrid Search 가 풀려는 정보 일원화 문제
- [[Claude_Code]] — 본 스택 구현의 코딩 보조 도구
- [[Graphify]] — Tree-sitter AST + 본 스택의 임베딩 보완 관계
- [[건축IT_사내개발_전략]] — 본 스택 도입의 사무소 전략 위치
