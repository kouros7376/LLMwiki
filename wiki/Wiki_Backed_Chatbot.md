# Wiki-Backed Chatbot — 위키를 컨텍스트로 쓰는 챗봇 4가지 통합 패턴

[[LLM_Wiki_개념]] (사람·LLM 큐레이션 마크다운 vault) 와 [[사내_챗봇_시스템]] (RAG 기반 매뉴얼 챗봇)을 **하나의 시스템으로 통합**할 때의 아키텍처·도구·코드 패턴. 본 사무소 컨텍스트(Gemini 챗봇 운영 + kouwiki vault 운영) 그대로 매핑되는 4가지 통합 패턴(A·B·C·D) + 5단계 도입 로드맵 + 미니 코드 골격.

## 본질 — Wiki-Backed RAG vs Vector RAG

벡터 RAG = **stateless 검색기**. 매 질의마다 PDF 청크를 임베딩 유사도로 끌어오고 LLM 이 다시 합성. 동일 질문 반복돼도 매번 같은 비용·같은 노이즈. Epsilla: "RAG is stateless. Every query is a new day."

Wiki-Backed RAG = **컴파일된 지식의 호출**. raw/ 의 원본 자료를 LLM 이 단 한 번 읽고 wiki/ 폴더에 개념 단위 마크다운 페이지로 정착. 이후 질의는 (1) 관련 위키 페이지 매칭 → (2) 페이지 본문 + [[교차참조]] 인접 페이지 컨텍스트 주입 → (3) LLM 답변. 사용자는 출처 페이지 ID 와 함께 답변 → 직접 페이지 열어 검증·심화 탐색 가능.

| 측면 | Vector RAG | Wiki-Backed |
|------|-----------|-------------|
| 자동성 | 높음 | 중 (LLM 자동화로 일부 상쇄) |
| 큐레이션 부담 | 0 | 있음 |
| 답변 추적성 | 청크 단위 (모호) | 페이지 단위 (명확) |
| 반복 질의 비용 | 일정 | 절감 (캐시·복리) |
| 환각 | 청크 잘못 매칭 시 발생 | 출처 페이지 명시로 감소 |

→ **본질 분기**: 잘 정의된 정책·매뉴얼·FAQ(사내 규정·CAD 표준·납품 매뉴얼) = Wiki, 비정형 대규모 아카이브(과거 도면 메타·고객 회의록 10년치) = RAG. 본 사무소는 **두 도메인 모두 존재** → 4가지 통합 패턴 중 **패턴 D (Wiki 1차 + RAG fallback)** 가 자연스러움.

## 4가지 통합 아키텍처 패턴

| 패턴 | 검색 방식 | 컨텍스트 주입 | 위키 규모 적합 | 구현 난이도 | 본 사무소 매핑 |
|------|-----------|---------------|----------------|-------------|---------------|
| **A. Direct-Inject** | 키워드 매칭 | 매칭 페이지 본문 그대로 | ~200 페이지 | 낮음 | kouwiki 초기 |
| **B. Wiki + Graph** | graphify query/path/explain | 본문 + 그래프 경로 | 중규모, cross-cutting 강 | 중 | [[LLM_Wiki_웹구현_사례\|Abraham Song]] 동일 |
| **C. Hybrid Vector + Wikilink Graph** | 임베딩 + BM25 + 위키링크 그래프 + CrossEncoder | 매칭 + 인접 hop | 1000+ 페이지 | 중상 | 중장기 확장형 |
| **D. Wiki 1차 + RAG Fallback** | 위키 매칭 → 실패 시 Gemini File Search | 위키 우선, 미스 시 PDF 청크 | 위키+대규모 PDF 병존 | 중 | **현 환경 최적** |

### 패턴 A — Direct-Inject (가장 단순)
사용자 질문 → 키워드/임베딩으로 위키 페이지 N개 선정 → 페이지 본문 전체 시스템 프롬프트 주입 → LLM 답변.
- 구현: FastAPI + frontmatter 파서 + LLM API 한 번 호출
- 위키 200페이지 미만 + Claude Sonnet 1M context = index.md 항상 주입 가능
- 한계: 1000+ 페이지 시 페이지 매칭 알고리즘이 병목

### 패턴 B — Wiki + Graph ([[LLM_Wiki_웹구현_사례|Abraham Song]] 패턴)
위키 마크다운 = 사람 읽는 정착물, [[Graphify]] graph.json = LLM 길찾기 구조물.
- 질문 → graphify query/path/explain 으로 노드 추적 → 연결된 위키 페이지 컨텍스트 주입 → 답변
- 그래프가 cross-cutting 질문(예: "OAuth ↔ Payment 어떻게 연결?") 자연스럽게 처리
- INFERRED 엣지가 [[정보_일원화_패턴|cross-cutting 통찰]] 발견의 알고리즘 도구

### 패턴 C — Hybrid Vector + Wikilink Graph
engraph 가 대표 사례. **5계층 검색**: 임베딩 + BM25 + 위키링크 그래프 확장 + CrossEncoder 재순위화 + 시간 점수. 상호 순위 융합(RRF).
- 위키링크 자체가 그래프 엣지 → "Sarah Chen 노트가 인증 시스템 검색에서 명시 언급 없이 등장" 같은 발견
- 사용자가 vault 에 [[교차참조]] 늘릴수록 자동으로 강해짐

### 패턴 D — Wiki 1차 + RAG Fallback (본 사무소 권장)
위키 매칭 충분 시 그 페이지 답변, 매칭 실패 시 → [[사내_챗봇_시스템|Gemini File Search 같은 RAG]] fallback → 답변과 함께 "이 답을 위키에 정착시킬까요?" 제안.
- 본 사무소 환경(Gemini 챗봇 + kouwiki) 통합에 가장 자연스러움
- 출처 형식 통일: 위키 답은 `[[페이지]]`, RAG 답은 `[PDF, p.12]`

## 도구 생태계 비교

| 도구 | 카테고리 | 백엔드 | 챗봇 통합 | 라이선스 | 본 사무소 점수 |
|------|---------|--------|----------|----------|---------------|
| **Smart Connections** | Obsidian 플러그인 | 로컬 임베딩(bge-micro-v2) | Smart Chat 내장 | 오픈/Pro | 개인용 ★★★★ |
| **Copilot for Obsidian** | Obsidian 플러그인 | OpenAI/Anthropic/Google/Ollama | Vault Q&A | 오픈/Plus | 개인용 ★★★★ |
| **Khoj** | Self-hosted | 다중 LLM 백엔드 | 다중 클라이언트 (Web/Obsidian/Phone) | AGPL-3.0 | **사무소 전사 ★★★★★** |
| **graphify** | Skill / CLI | Tree-sitter + Leiden | 어시스턴트 + graph.json | 오픈 | 코드/문서 ★★★★ |
| **engraph** | 로컬 그래프 + MCP | SQLite + llama.cpp | MCP/ChatGPT Actions | 오픈 | 중급자 ★★★★ |
| **lucasastorian/llmwiki** | Karpathy 풀스택 | FastAPI + SQLite + MCP | Claude Desktop MCP | Apache 2.0 | 풀스택 모범 ★★★★ |
| **Microsoft GraphRAG** | KG-RAG | Python + LLM 추출 | API 호출 | MIT | 대규모 비정형 ★★★ |
| **LightRAG** | KG-RAG | Python + Neo4j 옵션 | API 호출 | EMNLP 2025 | 대규모 비정형 ★★★ |
| **Gemini File Search** (현 사용) | Managed RAG | Google 호스팅 | API 호출 | 상용 | 매뉴얼 RAG 현역 ★★★★ |

→ 본 사무소 즉시 도입 가능한 조합: **kouwiki vault + 패턴 A 코드 골격 + Gemini File Search fallback** (Phase 1) → **Khoj** 또는 **lucasastorian/llmwiki** 로 멀티 사용자 서버화 (Phase 4).

## GraphRAG / Knowledge Graph RAG 위치

- **Microsoft GraphRAG**: 텍스트 → LLM 으로 엔티티·관계·claim 추출 → community 계층 → Global/Local/DRIFT/Basic 4 검색 모드. 인덱싱 비용 큼. holistic 질문 강함.
- **LightRAG** (EMNLP 2025, HKUDS): 듀얼 레벨 그래프 검색 + 증분 업데이트. GraphRAG 대비 농업·CS·법률·혼합 도메인 모두 우위 (농업 45.2% → 54.8%). Neo4J 백엔드 지원.
- **Neo4j + LLM**: 정형 그래프 DB + LLM 어시스턴트. 스키마 명확 도메인(BIM 객체 관계, 프로젝트-담당자-납품물 관계) 적합.

본 사무소 매핑:
- **사내 매뉴얼·정책·CAD 표준** → **LLM Wiki** (큐레이션 가능, 정착)
- **프로젝트 코드/도면/표준 폴더** → **graphify** (자동 그래프, 작은 인덱싱)
- **회의록·메일 같은 원시 대량** → **GraphRAG/Vector RAG fallback** (큐레이션 불가, 대량)

→ 하이브리드가 합리적. 단일 시스템으로 통합 강요 X.

## 답변 품질 — 위키 기반의 정량 증거

**WikiChat (Stanford OVAL, EMNLP 2023, Wikimedia Research Award 2024)**: Wikipedia 그라운딩 7단계 파이프라인.
- 시뮬레이션 대화 **97.3% factual accuracy**
- 인간 사용자 최근 토픽 대화 **97.9%** — **GPT-4 대비 55% 개선**
- 7B LLaMA 로 distill 해도 품질 유지

**[[Graphify]] 토큰 절감**:
- 52파일 혼합 코퍼스 평균 질의당 ~1.7K 토큰(그래프) vs ~123K 토큰(원본) → **71.5×** 절감
- 더 큰 셋업(456 Obsidian 노트 + 65+ 영구 노트)에서 **499×** 절감 사례

→ 본 사무소 vault 가 수백 페이지 되면 절감은 곧장 비용·지연 차이로 환산. 동일 질의 일주일 100번 반복 가정 시 LLM 비용 폭락.

## 위키 페이지 대용량 vs LLM 컨텍스트 한계

| 위키 규모 | 전략 |
|----------|------|
| **~200 페이지** (현 본 사무소 ~26) | index.md + 매칭 페이지 본문 직접 주입. Claude Sonnet 4.7(1M context)에서는 위키 전체 항상 주입 가능 |
| **~1000 페이지** | 임베딩 매칭으로 N페이지 선정 → 본문 + [[링크된]] 인접 페이지 1-2 hop |
| **~10000 페이지+** | 계층 요약 필수. index.md 카테고리·요약 + 페이지 frontmatter summary 만 1차 컨텍스트, 풀 본문 lazy-load |

**컨텍스트 절약 전략**:
1. 요약 계층화: page.md → page.summary.md → category.summary.md → index.md
2. 위키링크 그래프 lazy expand: 1 hop 항상, 2 hop 은 follow-up 시
3. 그래프 압축: graphify graph.json 처럼 노드·엣지 표현이 본문 대비 평균 1/30~1/100
4. 캐시: 동일 페이지 컴파일 결과 + 동일 질의 답변 캐시 (Smart Connections semantic cache)

## 운영 디테일 — 매칭·렌더·자료 분류 게이트키퍼

**사용자 질문 → 위키 페이지 매칭 (5단계 권장)**:
1. 키워드 (BM25/FTS5, 한국어는 [[한국어_LLM_스택|Kiwi]] 형태소)
2. 임베딩 (코사인 유사도, Smart Connections 같은 로컬 모델)
3. 위키링크 BFS/DFS — 매칭된 페이지에서 1-2 hop 확장
4. LLM 라우터 — 질문 받아 "어떤 카테고리?" 분류
5. 하이브리드 (BM25 + 임베딩 → RRF → CrossEncoder 재순위화 → LLM 라우터 게이트)

engraph 가 정확히 5계층 융합 구현.

**응답 생성 시 위키링크 자동 변환**: LLM 이 응답에 텍스트 쓸 때 → 페이지 ID 사전(slug 매핑)을 system prompt 동봉 → `[[페이지명]]` 형태 출력 지시 → 프론트엔드가 클릭 가능 링크로 렌더.

**자료 분류 게이트키퍼** (민감도 기반 LLM 백엔드 라우팅):

```
질문 도착
  → frontmatter sensitivity 체크
     - public/internal     → Claude Sonnet API + Gemini File Search
     - confidential        → 로컬 Ollama + 로컬 vault 만
     - secret (예: 임원 인사) → 챗봇 답변 차단, "권한 필요" 응답
```

→ 300명 사무소 필수. [[CLAUDE|운영규칙]] 에 sensitivity frontmatter 의무화 + 게이트키퍼 라우터 구현.

**사용자가 페이지 보강 요청 시**: 챗봇 대화 중 "이 답변을 위키에 추가" 버튼 → LLM 이 (1) 적절한 페이지 위치 결정 (신규/기존), (2) 마크다운 패치 작성, (3) 사람의 검토 후 git commit. Karpathy "답변이 곧 새 위키 페이지" 의 구체 구현.

## 코드 패턴 미니 골격 — 패턴 A 풀 구현

```python
# wiki_chatbot.py — kouwiki vault + Claude Sonnet 즉시 챗봇화
import os, re
from pathlib import Path
import frontmatter
from rank_bm25 import BM25Okapi
from anthropic import Anthropic
from fastapi import FastAPI
from pydantic import BaseModel
import logging

logging.basicConfig(level=logging.INFO,
                    format="%(asctime)s [%(levelname)s] %(message)s")
log = logging.getLogger("wiki_chatbot")

WIKI_DIR = Path(os.environ["KOUWIKI_PATH"])  # c:/Users/key/Desktop/kouwiki/kouwiki/wiki
client = Anthropic(api_key=os.environ["ANTHROPIC_API_KEY"])
app = FastAPI()

class WikiPage:
    def __init__(self, path: Path):
        post = frontmatter.load(path)
        self.path = path
        self.id = path.stem  # slug
        self.title = post.metadata.get("title", path.stem)
        self.sensitivity = post.metadata.get("sensitivity", "internal")
        self.body = post.content
        self.links = re.findall(r"\[\[([^\]\|]+)(?:\|[^\]]+)?\]\]", self.body)

def load_wiki() -> dict[str, WikiPage]:
    pages = {}
    for md in WIKI_DIR.rglob("*.md"):
        try:
            page = WikiPage(md)
            pages[page.id] = page
        except Exception as e:
            log.warning("페이지 로드 실패 %s: %s", md, e)
    log.info("위키 페이지 %d개 로드", len(pages))
    return pages

PAGES = load_wiki()
CORPUS = [p.body.split() for p in PAGES.values()]
PAGE_IDS = list(PAGES.keys())
BM25 = BM25Okapi(CORPUS)

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
            slug = link.replace(" ", "_").lower()
            if slug in PAGES and slug not in visited:
                expanded.append(PAGES[slug])
                visited.add(slug)
    return expanded

SYSTEM_PROMPT = """당신은 해마건축사사무소 사내 위키 챗봇이다.
아래 [WIKI_CONTEXT] 페이지만을 사실 근거로 답하라.
모르면 "위키에 정착되지 않은 내용입니다" 라고 답하라.
다른 페이지 언급 시 반드시 [[페이지ID]] 형태로 인용하라.
"""

def ask_llm(question: str, pages: list[WikiPage]) -> str:
    ctx = "\n---\n".join(f"### {p.title} (id: {p.id})\n{p.body}\n" for p in pages)
    try:
        msg = client.messages.create(
            model="claude-sonnet-4-7",
            max_tokens=1024,
            system=SYSTEM_PROMPT,
            messages=[{"role": "user",
                       "content": f"[WIKI_CONTEXT]\n{ctx}\n\n[질문]\n{question}"}],
        )
        return msg.content[0].text
    except Exception as e:
        log.exception("LLM 호출 실패")
        return f"오류: {e}"

def linkify(answer: str, base_url: str = "/wiki") -> str:
    return re.sub(
        r"\[\[([^\]\|]+)(?:\|([^\]]+))?\]\]",
        lambda m: f"[{m.group(2) or m.group(1)}]({base_url}/{m.group(1).replace(' ', '_').lower()})",
        answer,
    )

class Question(BaseModel):
    text: str

@app.post("/ask")
def ask(q: Question):
    seeds = match_pages(q.text, top_k=3)
    if not seeds:
        # 패턴 D: Gemini File Search fallback
        return {"answer": "위키 매칭 실패 — 사내 매뉴얼 RAG fallback 권장",
                "sources": []}
    expanded = expand_one_hop(seeds)
    raw_answer = ask_llm(q.text, expanded)
    return {
        "answer": linkify(raw_answer),
        "sources": [{"id": p.id, "title": p.title} for p in expanded],
    }
```

→ 50줄 안되는 코드로 즉시 본 kouwiki vault + Claude API 챗봇화 가능. **Phase 1 완료에 1-2주**.

[[Graphify]] graph.json 활용 (패턴 B) 코드는 raw/2026-05-06_wiki_backed_chatbot_research.md 의 `graph_query.py` 섹션 참조 — BFS path finding 미니 구현.

## 5단계 도입 로드맵 (300명 사무소)

### Phase 0 — 현재
- 기존 [[사내_챗봇_시스템|haema-knowledge-base]] (Gemini File Search) 운영
- kouwiki vault 가 [[CLAUDE|Karpathy 패턴]] 으로 큐레이션 중

### Phase 1 — 패턴 A 로 kouwiki 챗봇화 (1-2주)
- 위 코드 골격 그대로 FastAPI 1대
- vault 200페이지 미만 → index.md + 매칭 페이지 직접 주입
- IT 책임자 본인 매일 사용해 답변 품질·UX 검증
- Sensitivity 메타 도입 (`internal/confidential/public` 3단계)

### Phase 2 — 패턴 D: Gemini File Search 결합 (2-4주)
- 라우터 추가: 질문 → kouwiki 매칭 점수 임계치 미만 → Gemini File Search 호출
- 답변 형식 통일: `[[위키 페이지]]` 또는 `[PDF 파일명, p.12]`
- 사내 매뉴얼 챗봇 사용자에게 "이 답을 위키에 정착시키시겠습니까?" 액션 제공
- confidential 페이지는 Gemini API 우회, 로컬 Ollama 라우팅

### Phase 3 — [[Graphify]] 통합 (1개월)
- vault 전체에 `/graphify .` → graph.json + HTML 시각화 + audit report
- 챗봇 백엔드에 graphify path/query 도입 → cross-cutting 질문 강해짐
- "이 프로젝트 표준은 어느 법규 파생?" 같은 질문 처리
- 71.5× ~ 499× 토큰 절감 효과 측정

### Phase 4 — 사무소 전사 배포 (2-3개월)
- Khoj 또는 lucasastorian/llmwiki 기반 멀티 사용자 서버화
- RBAC: 본부별·직급별 sensitivity 접근 제어
- 감사 로그 (ClawTrace 패턴): 누가 언제 어떤 질의·페이지 봤는지 불변 로그
- 다우오피스 그룹웨어 SSO 연동
- Synology NAS 70TB 의 일부 vault 미러링 백업

### Phase 5 — 자동 보강 워크플로우 (지속)
- 답변 자체가 새 위키 페이지 (Karpathy 원칙)
- 정기 [[건강검진]]: 모순·고아·누락 링크 → IT 책임자 알림
- 직원 챗봇 요청 → LLM 패치 PR → 사람 검토 → git commit
- 매월 [[Graphify]] 재빌드 → 신 노드 변화 추적 → 지식 핵심 변화 감지

## 비용·운영 추정

| Phase | 월 비용 | 주요 항목 |
|-------|---------|----------|
| Phase 1-2 | $20-50 | LLM API + 작은 VPS |
| Phase 3-4 | $200-500 | 다중 사용자 + 임베딩 인프라 + 백업 |

**ROI 추정**: 사내 정보 검색 시간 1인당 주 1시간 절감 가정 시 **300명 × 4주 × 1시간 = 월 1,200시간 절감** → LLM 비용 압도.

## 출처
- raw/2026-05-06_wiki_backed_chatbot_research.md (본 페이지 1차 소스, 4가지 패턴 + 사례 + 코드)
- 핵심 사례:
  - Karpathy LLM Wiki gist: <https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f>
  - Abraham Song wiki.ai-biz.app: <https://brunch.co.kr/@abrahamsong/164>
  - lucasastorian/llmwiki: <https://github.com/lucasastorian/llmwiki>
  - kytmanov/obsidian-llm-wiki-local: <https://github.com/kytmanov/obsidian-llm-wiki-local>
  - devwhodevs/engraph: <https://github.com/devwhodevs/engraph>
  - Smart Connections: <https://github.com/brianpetro/obsidian-smart-connections>
  - Khoj: <https://github.com/khoj-ai/khoj>
  - WikiChat (Stanford OVAL, EMNLP 2023): <https://arxiv.org/abs/2305.14292>
  - Microsoft GraphRAG: <https://github.com/microsoft/graphrag>
  - LightRAG (EMNLP 2025): <https://github.com/HKUDS/LightRAG>
  - graphify (사용자 vault 도입): <https://github.com/safishamsi/graphify>
- 참고 비교 글:
  - MindStudio "LLM Wiki vs RAG": <https://www.mindstudio.ai/blog/llm-wiki-vs-rag-knowledge-base>
  - Epsilla "Karpathy Agentic Wiki Beyond RAG": <https://www.epsilla.com/blogs/karpathy-agentic-wiki-beyond-rag-enterprise-memory>
- 수집일: 2026-05-06

## 관련
- [[LLM_Wiki_개념]] — 본 챗봇이 컨텍스트로 사용하는 위키 패턴
- [[LLM_Wiki_운영_패턴]] — 본 챗봇 운영 시 admin 큐레이션 모델
- [[LLM_Wiki_웹구현_사례]] — 패턴 B 의 검증된 사례 (Abraham Song)
- [[사내_챗봇_시스템]] — 본 챗봇이 fallback 하는 RAG 시스템
- [[한국어_LLM_스택]] — 본 챗봇 매칭·재순위화의 한국어 인프라
- [[Graphify]] — 패턴 B/C 의 그래프 엔진
- [[지식의_복리]] — 답변 자체가 위키 페이지로 누적될 때의 복리
- [[정보_일원화_패턴]] — 위키+챗봇 통합이 풀려는 일원화 문제
- [[자동화_시나리오]] — 본 챗봇 도입의 사내 자동화 시나리오 위치
- [[건강검진]] — 챗봇 답변 품질 모니터링 = 위키 lint
- [[CLAUDE]] — 챗봇 답변 시 [[페이지]] 인용 강제 규칙
- [[Claude_Code]] — 본 챗봇 백엔드 개발의 코딩 보조 환경
- [[건축IT_사내개발_전략]] — 본 챗봇 도입의 사무소 전략 위치
