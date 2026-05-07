---
source_type: deep_research
topic: LLM Wiki 운영 방법
captured_at: 2026-05-06
contributor: Claude (Sonnet 기반 리서치)
---

# LLM Wiki 운영 방법 — 1인 vault 에서 사내 5~10명 admin 큐레이션 모델로 확장하기 위한 딥리서치

## 리서치 범위 + 방법

본 리서치는 Andrej Karpathy 가 2026년 4월 GitHub Gist 로 제안한 **LLM Wiki 패턴**(raw/wiki/schema 의 3 레이어, ingest/query/lint 의 3 동작 모드)을 출발점으로 한다. kouwiki 운영자(약 300명 규모 한국 건축설계사무소의 전산실 총괄)는 이미 1인 Obsidian vault 운영 경험을 가지고 있고, 이를 사내 5~10명 admin 큐레이션 모델로 확장하려 한다.

리서치 방법은 다음과 같다.
- WebSearch 로 한국어 / 영어 자료를 모두 검색 (검색어: "Andrej Karpathy LLM wiki", "LLM wiki operations curation ingest query lint", "Obsidian AI agent knowledge graph", "team wiki curation admin editor reader role ACL", "knowledge wiki drift decay stale page archival", "Obsidian 한국어 LLM 위키 운영 사례", "건축설계 지식 관리 사내 위키 사례 한국").
- WebFetch 로 본문 전체를 가져와서 정독 (Karpathy 의 원 gist, LLM Wiki v2, Medium / Substack / GeekNews 한국 토론, claude-obsidian, llm-wiki, agentic knowledge management, continuous context 등 10여 개 소스).
- 단순 요약이 아니라 "사내 5~10명 admin 큐레이션 모델"에 적용 가능한 패턴·교훈을 추출.
- kouwiki 의 기존 자료(`raw/LLM Wiki + Graphify를 직접 만들다.md` 등)와 교차 검증.

---

## 핵심 발견 8개

### 1. 운영 모델 비교 — 1인 vs 소규모 팀(5~10명) vs 전사

LLM Wiki 패턴은 원래 **1인 운영을 전제**로 설계됐다. Karpathy 본인의 운영 방식은 "한쪽에 LLM 에이전트(Claude Code, Cursor), 다른 한쪽에 Obsidian 을 열어두고, 새 소스가 들어오면 LLM 이 위키 전체를 업데이트하고 본인은 읽기·질문·방향 제시만 하는" 구조다(아래 [사례 1]). 단일 의사결정자 모델이라 권한 충돌·동시 편집·합의 비용이 0 이다.

**5~10명 admin 큐레이션 모델**(kouwiki 가 지향하는 형태)은 이 단순함이 깨진다. GeekNews 한국 토론에서 반복적으로 지적된 핵심 위험은 다음 세 가지다.
- **컨텍스트 한계로 인한 기억 손실** — 20~30만 토큰을 넘어가면 LLM 이 위키 전체를 일관되게 유지하지 못한다는 보고가 다수.
- **새로운 형태의 기술 부채** — admin 마다 ingest 스타일이 다르면 위키가 "여러 사람의 다른 사고 흐름" 으로 갈라진다.
- **사고 정리 가치의 손실** — "문서 작성의 진정한 가치는 결과물이 아니라 사고 정리 과정인데, LLM 에 위임하면 그 깊이가 사라진다" 는 의견.

**전사 단위(300명 모두 reader, 일부 editor)** 운영은 본 리서치에서 본격 사례를 찾지 못했다. AFFiNE 의 "Corporate Wiki Playbook" 정도가 일반론을 제시할 뿐이며, LLM Wiki 패턴 자체가 전사 도입 사례는 아직 공개적으로 보고된 바가 없다. 이는 곧 kouwiki 가 5~10명 admin 모델을 정착시키면, **국내 건축설계업계에서 사실상 첫 번째 운영 레퍼런스**가 될 가능성이 높다는 뜻이기도 하다.

| 항목 | 1인 | 소규모 팀(5~10 admin) | 전사(300명) |
|---|---|---|---|
| 운영 부담 | 낮음 (본인이 schema 하나만 유지) | 중간 (admin 합의·편집 충돌 조율) | 높음 (부서별 ACL·온보딩 비용) |
| 권한 모델 | 단일 owner | admin / contributor 2-tier | admin / editor / reader 3-tier + 부서별 ACL |
| 드리프트 위험 | 낮음 (본인 머릿속 schema 가 일관) | 중간 (스타일 표준화 필요) | 높음 (부서 간 용어 충돌) |
| 의사결정 비용 | 0 | 소규모 동기 회의 (주1회) | governance board 필요 |
| LLM 컨텍스트 부담 | 본인 vault 만 | admin 분산 큐레이션으로 줄어듦 | 위키 분할 불가피 |

### 2. Ingest 운영 패턴

**자동 vs 수동 vs 하이브리드**.
- **완전 자동 ingest** (Obsidian Web Clipper + AI Interpreter 가 /raw-inbox 에 자동 적재 → LLM 이 위키 컴파일) 는 1인 운영에서 잘 동작하지만, 모델 신뢰도 문제로 잘못된 분류·중복·할루시네이션이 누적된다는 댓글 지적이 다수다.
- **완전 수동 큐레이션**(전통 위키 방식) 은 Karpathy 패턴의 가장 큰 장점인 "지루한 작업의 LLM 위임" 을 포기하는 셈이다.
- **하이브리드 — Human-in-the-Loop "AI proposes, human approves"** 가 현재 가장 강하게 권고되는 모델이다. dsebastien.net 의 Agentic KM 글은 "AI 가 1분마다 heartbeat 으로 변경 감지 → 수정안을 노트에 직접 제안 → 사용자가 승인/거부 → 실행" 이라는 워크플로우를 제시한다.

**분류·태깅 시스템(출처·신뢰도·민감도)**.
- LLM Wiki v2 는 모든 ingest 결과에 **신뢰도 점수**를 부여하고, **수집 단계에서 API 키·토큰·민감 데이터를 자동 필터링** 하는 것을 필수 게이트로 본다.
- Graphify 는 관계에 `EXTRACTED`(100% 확실) / `INFERRED`(점수 부여) / `AMBIGUOUS`(사람 검토 필요) 라는 3-tier 신뢰도 라벨을 붙인다 — 이 타입 시스템이 큐레이션 우선순위를 잡아주는 핵심이다.
- 건축설계사무소 맥락에서는 **민감도(공개/사내한정/프로젝트한정/대외비)** 가 추가로 필요하다. ERP 통합이나 발주처 정보가 위키에 흘러들면 곧바로 보안 사고가 된다.

**중복 검출과 통합**. Karpathy 패턴은 **명시적 폐기(Supersession)** 를 강조한다. 새 정보가 옛 정보를 대체하면 옛 페이지를 삭제하지 말고 "superseded_by" 메타데이터로 마크해 둔다. LLM Wiki v2 는 이를 git 기반 감사 추적과 결합해서 "자동 망각 곡선보다 더 안정적" 이라고 평가한다.

**멀티모달 ingest**. Graphify 가 가장 성숙한 레퍼런스를 제공한다.
- PDF/이미지: LLM + OCR 파이프라인 (Mistral OCR, Datalab Marker 등이 2026 년 표준급)
- 영상: faster-whisper 로 로컬 전사 → 마크다운으로 ingest
- 코드: Tree-sitter AST 결정론적 추출 (LLM 토큰 낭비 0, 할루시네이션 0)
- 한국 건축설계사무소 맥락에서는 **CAD 도면(.dwg)·BIM 모델(.rvt)·hwp 의 멀티모달 ingest** 가 거의 미개척지다. 우선 PDF·이미지부터 시작하고 CAD/BIM 은 별도 어댑터를 단계적으로 만드는 게 현실적이다.

### 3. Query 운영 패턴

**검색 방식 3계층**.
- **단순 검색**: `index.md` 의 카테고리 트리에서 페이지 식별. kouwiki 의 현재 schema 가 이 단계.
- **의미 검색**: 임베딩 기반(현재 RAG 방식). LLM Wiki 패턴은 의도적으로 이 단계를 줄이고 "LLM 이 위키를 직접 읽게" 하는 방향이다.
- **그래프 순회**: Graphify 같은 그래프 백엔드가 "A 에서 B 까지의 경로" 를 시각화하면서 단순 검색·의미 검색이 놓치는 구조적 연결을 드러낸다.

**출처 인용·근거 추적**. Karpathy 패턴의 핵심 규약은 "답변에 항상 `[[위키링크]]` 로 근거 페이지를 인용" 하는 것이다. 이는 단순 매너가 아니라 **드리프트 방지의 1차 방어선** 이다 — 인용이 깨지면 자동으로 lint 가 잡아낸다. kouwiki schema 가 이미 이 규칙을 명시하고 있다는 점은 잘 잡혀 있다.

**캐싱·반복 질의**. claude-obsidian 의 **hot.md (hot cache)** 패턴이 흥미롭다. 세션 종료 시점에 "최근 컨텍스트 요약" 을 hot.md 에 갱신하고, 다음 세션 시작 시 자동 로드. 반복 질문에서 위키 전체를 다시 스캔하는 비용을 줄이는 단순한 메커니즘이다.

### 4. Lint(건강검진) 운영

**탐지 항목**.
- 깨진 `[[위키링크]]` (대상 페이지 부재)
- 고립 노드 (어떤 페이지에서도 링크되지 않는 페이지)
- 중복/유사 페이지 (병합 후보)
- 스텁 페이지 (본문 빈약·출처 불명·오래됨)
- 모순 (서로 상충하는 주장)
- 누락된 교차참조 (개념이 본문에 등장하지만 `[[위키링크]]` 가 없음)
- 데이터 갭 (web search 로 채울 수 있는 누락)

**자동화 도구**. 2026 년 5월 시점에 사용 가능한 도구들은 다음과 같다.
- `tenfoldmarc/wiki-brain-skill`: Claude Code 용 lint 스킬, "contradictions, stale claims, orphan pages, missing concepts, broken links" 를 한 번에 스캔
- `hellohejinyu/llm-wiki`: orphan 검출 + 정적 분석 + LLM 의미 분석 결합
- **graphify**: 그래프 중심성으로 "신(God) 노드" 와 "뜻밖의 연결(Surprising Connections)" 을 탐지 — 이는 lint 를 넘어 **메타 인사이트 발견 도구** 이기도 하다.
- `claude-obsidian` 의 `lint the wiki` 명령

**정기 실행 주기**. 1인 운영 사례들의 권고는 대체로 **주 1회 lint** 다. kouwiki 의 admin 큐레이션 모델로 확장한다면:
- 일별: ingest 직후 자동 lint (해당 페이지·교차참조만)
- 주별: 전체 lint, admin 회의에서 결과 리뷰
- 월별: 카테고리 재편·schema 갱신 검토
알림 채널은 사내 그룹웨어(다우오피스) 또는 슬랙·디스코드. 사용자 환경에서는 다우오피스 오픈 API 로 게시물 생성이 자연스럽다.

### 5. 드리프트·노이즈 누적 방지

**시간 경과에 따른 위키 품질 저하 원인**.
- DataHub 의 "Continuous Context" 분석은 문서 품질 저하의 본질을 "문서가 묘사하는 세계가 계속 변하기 때문" 이라고 정의한다.
- 사람은 패턴 매칭으로 오래된 정보를 보정하지만, **AI 에이전트는 "doc 에 적힌 그대로 자신감 있게 동작" 하므로 stale 한 위키는 obvious failure 가 아니라 confidently wrong output 을 낳는다.**
- 이 점이 LLM Wiki 운영에서 가장 무서운 실패 모드다. "정보가 없는" 것보다 "낡은 정보가 있는" 것이 훨씬 위험하다.

**Decay·아카이브 처리**.
- 옛 페이지를 **삭제 금지, 아카이브 폴더 이동** (kouwiki 라면 `wiki/_archive/`).
- 메타데이터에 `superseded_by`, `deprecated_at`, `last_validated_at` 추가.
- LLM Wiki v2 는 신뢰도가 시간에 따라 자동 감쇠하고 새 자료로 강화되는 모델을 제안한다.

**운영 규칙의 진화**. schema(CLAUDE.md) 자체도 정기 리뷰 대상이다. 사용처 schema 의 "새로운 주제가 들어오면 사용자와 논의 후 카테고리를 추가" 라는 조항은 좋은 출발점이지만, admin 5~10명 모델에서는 schema 변경이 곧 **운영 규칙의 governance** 가 된다. git PR + admin 합의 방식이 자연스럽다.

**3가지 컨텍스트 분리**. DataHub 의 분류가 admin 큐레이션 모델에 그대로 적용 가능하다.
- **Declared Context**: 사람이 작성한 제도적 지식 (사규·매뉴얼). 천천히 변하지만 정기 검증 필요.
- **Derived Context**: 시스템 상태에서 자동 계산 (ERP 스키마·릴리즈 노트). 변경 시 자동 갱신.
- **Observed Context**: 사용 패턴에서 추론 (어떤 페이지가 자주 참조되는가). 자동 수집.

### 6. 권한·접근 제어

**역할 분리**. DokuWiki GroupManager 모델이 가장 단순하면서 검증된 레퍼런스다.
- **reader**: 해당 namespace 의 콘텐츠 조회만
- **editor**: 해당 namespace 편집·업로드 (단, 공개·admin namespace 는 예외)
- **group-admin**: admin namespace 읽기 + 그룹 사용자 관리
- 일단 ACL 이 셋업되면 group-admin 이 자율적으로 그룹 운영, super-admin 개입 최소화.

**부서별·민감도별 ACL** (건축설계사무소 맥락).
- 본부별로 NAS 가 분산 운영중인 상황(70TB)을 그대로 매핑: 본부 = namespace.
- 민감도 라벨: `public` / `internal` / `project` / `confidential`. confidential 은 admin 만 조회.
- LLM 자체에 대한 ACL: 일부 페이지는 LLM 컨텍스트에 절대 들어가지 않게 frontmatter 에 `llm_access: deny` 같은 플래그 부여.

**LDAP/SSO 통합**. 다우오피스 그룹웨어가 SSO 를 제공하므로, 위키 프론트엔드도 같은 SSO 게이트를 쓰는 게 admin 큐레이션 모델의 표준이다. 별도 패스워드 관리는 곧 보안 부채.

**감사 추적**. LLM Wiki v2 는 "모든 작업(수집·편집·삭제·조회) 을 타임스탬프와 함께 기록, 대량 작업은 가역성 보장" 을 강조한다. git 백엔드를 쓰면 자연히 충족된다 — 모든 admin 의 액션이 commit log 에 남는다.

### 7. 메타 인사이트 누적 방법

LLM Wiki 의 진짜 가치는 "자료를 읽지 않고도 답하기" 가 아니라 **"자료를 읽었기 때문에 보이는 cross-cutting 통찰을 별도 페이지로 결정화(Crystallize)" 하는 데 있다**. 이 점이 본 리서치에서 가장 일관되게 강조된 메시지였다.

**메타 페이지 설계 패턴**.
- **Hub 페이지** (Benjamin's Dev Blog 사례): "한 도메인의 시작점" — 카테고리 인덱스 + 핵심 개념 다이어그램.
- **Comparison 페이지**: 동일 문제를 푸는 여러 도구·접근법 비교 표 (kouwiki 의 `2026-05-05_amaranth10_vs_ksystem_comparison.md` 가 이미 이 패턴).
- **Lessons 페이지**: 프로젝트·인시던트 회고 — 시간이 지나면서 가장 가치가 압축되는 형태.
- **Map of Content (MOC)**: Obsidian 커뮤니티 표준 패턴. 개별 노트를 주제별로 묶어주는 메타 페이지.

**도메인 동형성(Isomorphism) 발견**. Graphify 의 "신 노드" 와 "뜻밖의 연결" 이 이 단계의 핵심 도구다. kouwiki 의 운영자가 직접 경험한 사례 ([사례 1] 의 OAuth2 ↔ PaymentGateway 공유 유틸리티 발견) 는 코드 도메인이지만, 같은 패턴이 ERP / 자동화 / Claude 활용 사이에서도 발생할 가능성이 높다 — 예: "다우오피스 API 호출 패턴" 이 "ERP 연동 패턴" 과 동형 구조를 가진다는 발견.

**그래프 시각화로 통찰 보강**. 텍스트 위주의 위키에서는 보이지 않는 클러스터·다리(bridge) 노드·고립 클러스터 가 그래프 뷰에서 즉시 드러난다. 이는 사람이 매주 5분 그래프 뷰를 보는 것만으로 강력한 메타 인사이트 생성 메커니즘이 된다.

### 8. 실제 사례 분석

본 리서치에서 수집한 사례를 아래 "사례 모음" 섹션에 별도 정리한다.

---

## 사례 모음

### [사례 1] Karpathy 본인 — 100개 페이지·40만 단어 위키

- **출발**: 2026년 4월 GitHub Gist 로 LLM Wiki 패턴 공개 → 16M views.
- **운영 방식**: 한쪽에 Claude Code / Cursor, 다른 쪽에 Obsidian. 새 소스가 들어오면 LLM 이 위키 전체를 업데이트, 본인은 읽기·질문·방향 제시만.
- **규모**: 단일 연구 주제 위키가 100 articles, 400,000 words 까지 성장 (PhD 논문보다 길음).
- **교훈**: 본인이 글을 직접 쓰지 않고도 위키가 컴파일됨. "Obsidian = IDE, LLM = programmer, wiki = codebase" 메타포.
- **한계**: 개인 단위라 권한·동시편집·드리프트 governance 가 검증되지 않음. 그대로 팀에 적용하면 깨질 가능성.

### [사례 2] Abraham Song — LLM Wiki + Graphify 직접 구현

(kouwiki/raw 에 이미 저장된 "LLM Wiki + Graphify를 직접 만들다" 한국어 글)
- **배경**: Visa Inc. 에서 지식 관리자 경험. 1인 PKM 시스템 필요. Hetzner CX23 저렴 리눅스 서버 사용.
- **스택**: FastAPI + Vanilla JS SPA + Caddy TLS 1.3 + bcrypt 베이직 인증 + 관리자 IP 화이트리스트 (적정 보안).
- **핵심 발견**:
  - LLM Wiki 만으로는 "선형적·구조 결여·시각 맥락 부족" 한계.
  - Graphify 그래프 계층을 덧입혀 "신 노드(평균 차수 + 2σ)", "뜻밖의 연결" 발견 가능.
  - 큐 처리 멈춤 / 망가진 마크다운에 Graphify 가 체함 / 타임아웃 세분화 (쿼리 10s, 경로 30s, 전체 재빌드 300s) 등 운영 디버깅 경험 풍부.
- **kouwiki 적용 함의**: 5~10명 admin 모델에서도 동일하게 "위키(사람이 읽는 프론트) + 그래프(분석 백엔드)" 의 분리 구조가 유효.

### [사례 3] Balu Kosuri — 1주일 만에 15-20 페이지 누적

- **세팅**: Cursor + Obsidian, 3개 프롬프트로 부트스트랩.
- **운영**: ingest / query / lint 의 3 모드로만 운영.
- **핵심 인사이트**: "지식베이스의 어려운 부분은 읽기·사고가 아니라 항상 bookkeeping(교차참조·용어 일관성·인덱스 갱신)이었다. LLM 이 그걸 가져가준다."
- **한계 보고**: 1주 운영으로는 드리프트 검증 부족.

### [사례 4] Benjamin (한국 개발자 블로그)

- **사례**: 본인 블로그 글 수백 개를 Obsidian vault 로 옮겨 LLM Wiki 컴파일.
- **vault 구조**: Sources / Concepts / Projects / Maps / Comparisons 5개 디렉토리.
- **핵심 결론**: "schema 문서의 품질이 모델 성능만큼 결과에 영향" — kouwiki 가 이미 잘 갖춘 영역.
- **권고**: 기존 블로그·문서 보유자에게 가장 잘 맞는 패턴.

### [사례 5] GeekNews 한국 개발자 토론

- **수집 사례**: Codex / Gemini 파서 추가, 한글 검색 BM25 가드레일 적용, GitHub 백업 자동화.
- **반복적으로 나온 우려**:
  - 컨텍스트 한계로 200~300k 토큰 부근부터 LLM 이 위키를 일관되게 유지 못함.
  - "새로운 형태의 기술 부채" 누적.
  - 사고 정리 가치 손실.
- **kouwiki 함의**: 한글 위키 운영에서는 BM25 가드레일·토크나이저 선택이 별도 신경 써야 할 영역.

### [사례 6] claude-obsidian (AgriciDaniel/claude-obsidian)

- **명령 체계**: `/wiki` (부트스트랩), `ingest [source]`, `what do you know about X?`, `/autoresearch [topic]`, `lint the wiki`.
- **hot cache 패턴**: 세션 종료 시 hot.md 에 컨텍스트 요약 갱신, 다음 세션 자동 로드.
- **autoresearch 루프**: search → fetch → synthesize → file 의 3단계 반복.
- **kouwiki 적용 함의**: hot.md 패턴은 admin 모델에서 admin 별 hot 캐시가 충돌할 수 있어 신중. 단일 공유 hot.md 를 admin 회의 후 갱신하는 방식이 안전.

### [사례 7] nvk/llm-wiki — 멀티 에이전트·thesis-driven

- **구조**: `~/wiki/topics/<topic>` 단위 격리 vault.
- **명령 체계**: `/wiki:research --new-topic --deep --min-time 2h`, `/wiki:thesis "claim"`, `/wiki:audit`.
- **특징**: 5/8/10 병렬 에이전트, "Retardmaxxing(act first, refine later)" 철학.
- **kouwiki 적용 함의**: 5~10명 admin = 5~10개 병렬 에이전트로 보면 관리 메커니즘이 유사. 다만 사람 admin 이 LLM 보다 느리므로 "동기 회의 + 비동기 ingest" 의 하이브리드가 현실적.

### [사례 8] dsebastien.net — Agentic KM, AI heartbeat

- **메커니즘**: AI 가 1분마다 vault 변경 감지 → 노트에 직접 수정안 제안 → 사용자 승인/거부 → 실행.
- **핵심 원칙**: "AI proposes before it executes. You maintain control."
- **kouwiki 적용 함의**: 5~10 admin 모델에서 admin 의 인지 부담을 줄이려면 이 heartbeat + propose 모델이 거의 필수. 다우오피스 게시물·이메일 알림으로 propose 를 라우팅 가능.

### [사례 9] LLM Wiki v2 (rohitg00) — agentmemory 교훈

- **핵심 패턴**:
  - 명시적 폐기(Supersession) — 자동 망각보다 안정적.
  - 신뢰도 점수 + 모순 자동 감지.
  - 인간 검증 게이트 (자동 ingest 시 반드시).
  - 메시 동기화 — 병렬 작업 세션의 관찰값을 공유 위키로 병합.
  - 결정화(Crystallization) — 완료된 작업을 "질문·발견·관련 엔티티·교훈" 으로 추출, 새 위키 페이지 생성.
- **kouwiki 적용 함의**: 결정화 프로세스가 5~10 admin 모델에서 메타 페이지 생산의 표준 워크플로우가 될 수 있음.

### [사례 10] DokuWiki GroupManager — 검증된 ACL 모델

- 본 리서치에서 발견한, 가장 단순하면서 운영 검증된 wiki ACL 패턴.
- reader / editor / group-admin 의 3-tier, namespace 분리.
- "ACL 셋업 후 group-admin 자율 운영, super-admin 개입 최소" — 5~10 admin 모델의 좋은 청사진.

### [실패 사례·교훈]

- **컨텍스트 폭발**: 200~300k 토큰 위키에서 LLM 의 일관성 유지 능력이 무너짐 → 위키를 주제별로 분할(nvk 의 topics 격리), 또는 admin 별 namespace 분할.
- **자동 ingest 의 신뢰도 문제**: 사람 검증 게이트 없이 자동 ingest 만 돌리면 잘못된 분류·중복·환각이 누적 → Human-in-the-Loop 필수.
- **stale 위키가 confidently wrong output 을 만든다**: AI 에이전트가 위키를 그대로 믿기 때문에, 정기 lint 와 last_validated_at 메타데이터가 안전망.
- **schema 변경의 비공식화**: 운영 중 schema 가 수시로 바뀌면 admin 간 합의가 깨짐 → schema 변경은 git PR + admin 회의 통과 필수.
- **그래프 백엔드 디버깅 비용**: Graphify 통합에서 큐 멈춤·메모리 누수·타임아웃 미스매치가 반복 보고됨 → 도입 시 별도 운영 모니터링 채널 필요.

---

## 운영 패턴 종합 표 — 1인 / 소규모(5~10 admin) / 전사

| 영역 | 1인 | 소규모(5~10 admin) | 전사(300명) |
|---|---|---|---|
| **Ingest 방식** | 자동 우선, 본인이 사후 검토 | 하이브리드(LLM 제안 + admin 승인) | 부서별 자동 + 분기 admin 검토 |
| **분류/태깅** | 본인 머릿속 schema | git PR 로 schema 합의 | governance board + 공식 분류체계 |
| **신뢰도·민감도** | public 위주 | public/internal/project/confidential 4-tier | + 발주처별 NDA 라벨 |
| **권한 모델** | 단일 owner | DokuWiki 식 reader/editor/admin 3-tier + namespace | + 부서별 ACL + 다우오피스 SSO |
| **Query** | 자유 | admin 은 모든 페이지, reader 는 권한 내 | LDAP 기반 페이지별 ACL |
| **Lint 주기** | 주1회 수동 | 일별 자동(부분) + 주별 전체 + 월별 schema | + 분기 governance 리뷰 |
| **Lint 알림** | 본인 채널 | 다우오피스 그룹 게시물 + 슬랙 | 다우오피스 + 부서장 메일 |
| **드리프트 방지** | last_validated_at 메타만 | + Supersession + git 감사 | + 분기 stale audit |
| **메타 인사이트** | 그래프 뷰 매주 5분 | admin 회의 어젠다에 "신 노드 / 뜻밖의 연결" 항상 포함 | + 분기 cross-cutting 리포트 |
| **실패 시 영향** | 본인만 | admin 간 신뢰 손상 | 발주처 클레임 가능 |
| **추천 LLM** | Claude Code | Claude Code + 다우오피스 API 라우팅 | + 자체 게이트웨이·로깅 |

---

## 권장 운영 규칙 — kouwiki(5~10 admin 큐레이션 모델) 실무 적용형

### A. 폴더·권한 구조

1. 현재의 플랫 구조(`raw/` + `wiki/`) 유지하되, **민감도 라벨을 frontmatter** 에 명시: `sensitivity: public | internal | project | confidential`.
2. confidential 페이지는 LLM 컨텍스트 차단을 위해 `llm_access: deny` 추가.
3. **admin namespace 도입** (선택): 카테고리 prefix 로 구현 — `core/`, `bonbu-a/`, `bonbu-b/` 등 본부별 prefix. 단, kouwiki 의 "플랫 + index.md 카테고리" 철학을 깨기 싫으면 frontmatter 의 `owner_team` 으로 대체.
4. 다우오피스 SSO 게이트 + git 백엔드(자동 감사 추적).

### B. Ingest 게이트

5. **모든 자동 ingest 는 "AI proposes, admin approves"**. heartbeat 으로 변경 감지 → 노트 또는 다우오피스 게시물에 제안 → admin 1명 이상 승인 후 commit.
6. 자동 ingest 출력에 신뢰도 점수와 출처 강제. 신뢰도 < 0.7 인 항목은 별도 큐로 보내서 사람 검토 강제.
7. 멀티모달은 단계 도입: PDF·이미지(즉시) → 영상 전사(faster-whisper, 다음 분기) → CAD/BIM(별도 어댑터, 장기).
8. **민감 정보 수집 단계 자동 필터** — API 키·발주처명·개인정보 정규식 차단.

### C. Schema 변경 governance

9. CLAUDE.md(schema) 변경은 git PR + admin 2명 이상 승인 필수. 카테고리 추가도 동일.
10. 새로운 카테고리는 `category_proposal_<날짜>.md` 페이지로 먼저 토론, 합의 후 schema 반영.

### D. Lint 주기·알림

11. **일별 자동 lint** (해당 ingest 페이지 + 교차참조만): 깨진 링크·고립·신규 스텁 검출.
12. **주별 전체 lint** (전 위키): 중복·모순·stale·gap 탐지. 다우오피스 그룹 게시물로 admin 통보.
13. **월별 schema 리뷰**: lint 결과를 모아 카테고리·schema 변경 안건 도출.
14. **stale 기준**: `last_validated_at` 이 90일 초과 + 본문에 시점 의존 문장(가격·버전·법령·인사) 있는 경우 우선순위 상위.

### E. 드리프트 방지

15. **삭제 금지, 아카이브 이동**. `wiki/_archive/` 에 옮기고 `superseded_by: [[새페이지]]` 메타로 연결.
16. Supersession 체인을 lint 가 매주 시각화 (어떤 페이지가 가장 많이 덮어쓰였나).
17. 인용 끊긴 페이지는 30일 grace period 후 archive 로 이동.

### F. 메타 인사이트 누적

18. **graphify 를 분기 1회 정기 실행**해서 신 노드·뜻밖의 연결 리포트 생성 → admin 회의 어젠다 고정.
19. 3건 이상 페이지에서 같은 개념이 등장하면 **자동으로 "메타 페이지 후보"** 로 등재 → admin 1명이 결정화(Crystallization) 담당.
20. 분기마다 **cross-cutting 회고 페이지** 작성: "지난 분기 가장 자주 참조된 페이지 10개", "가장 오래 stale 했던 페이지 5개", "신 노드 변동", "운영 규칙 변경 제안".

### G. Query 운영

21. 답변에 `[[위키링크]]` 인용 강제 (현재 schema 유지).
22. 위키에 없으면 "위키에 없음" 명시 후 raw/ → web search 순으로 폴백 (현재 schema 유지).
23. 반복 질의용 **공유 hot.md** 는 admin 회의 직후 갱신, 비동기 단독 갱신 금지.

### H. 사내 확장 단계 로드맵 (제안)

- **Phase 0 (현재, M1~M2)**: 1인 운영 안정화, schema 확정.
- **Phase 1 (M3~M5)**: admin 2~3명 추가 (전산실 + 한 본부 코디네이터). 권한 모델·git PR 흐름 검증.
- **Phase 2 (M6~M9)**: admin 5~7명. 본부별 namespace, 다우오피스 SSO 통합, 자동 lint 알림.
- **Phase 3 (M10~M12)**: admin 8~10명. 부서별 reader 권한 개방, 멀티모달 ingest, graphify 분기 운영.
- **Phase 4 (12M+)**: 전사 reader 개방 검토. 단, governance board 설치 전제.

---

## 출처 (수집일: 2026-05-06)

- [llm-wiki · GitHub (Karpathy 원 gist)](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
- [LLM Wiki v2 — extending Karpathy's LLM Wiki pattern with lessons from building agentmemory (rohitg00)](https://gist.github.com/rohitg00/2067ab416f7bbe447c1977edaaa681e2)
- [What Is Andrej Karpathy's LLM Wiki? (MindStudio)](https://www.mindstudio.ai/blog/andrej-karpathy-llm-wiki-knowledge-base-claude-code)
- [Andrej Karpathy's LLM Knowledge Bases explained (Medium / Mehul Gupta)](https://medium.com/data-science-in-your-pocket/andrej-karpathys-llm-knowledge-bases-explained-2d9fd3435707)
- [I used Karpathy's LLM Wiki to build a knowledge base that maintains itself with AI (Balu Kosuri)](https://medium.com/@k.balu124/i-used-karpathys-llm-wiki-to-build-a-knowledge-base-that-maintains-itself-with-ai-df968e4f5ea0)
- [How I Took Karpathy's LLM Wiki and Built an AI-Powered Second Brain in Obsidian (aimaker.substack)](https://aimaker.substack.com/p/llm-wiki-obsidian-knowledge-base-andrej-karphaty)
- [LLM Wiki: Karpathy's Local Knowledge Base Setup 2026 Guide (kunalganglani.com)](https://www.kunalganglani.com/blog/llm-wiki-karpathy-local-knowledge-base)
- [GitHub - AgriciDaniel/claude-obsidian](https://github.com/AgriciDaniel/claude-obsidian)
- [GitHub - nvk/llm-wiki](https://github.com/nvk/llm-wiki)
- [GitHub - tenfoldmarc/wiki-brain-skill](https://github.com/tenfoldmarc/wiki-brain-skill)
- [GitHub - hellohejinyu/llm-wiki](https://github.com/hellohejinyu/llm-wiki)
- [Agentic Knowledge Management: The Next Evolution of PKM (dsebastien.net)](https://www.dsebastien.net/agentic-knowledge-management-the-next-evolution-of-pkm/)
- [Mastering Personal Knowledge Management with Obsidian and AI (ericmjl.github.io)](https://ericmjl.github.io/blog/2026/3/6/mastering-personal-knowledge-management-with-obsidian-and-ai/)
- [Continuous Context: Why AI Docs Decay (DataHub)](https://datahub.com/blog/continuous-context/)
- [LLM Wiki는 무엇이고, 왜 지금 주목받는가 (Medium / Dreamwalker 박제창)](https://medium.com/@aristojeff/llm-wiki%EB%8A%94-%EB%AC%B4%EC%97%87%EC%9D%B4%EA%B3%A0-%EC%99%9C-%EC%A7%80%EA%B8%88-%EC%A3%BC%EB%AA%A9%EB%B0%9B%EB%8A%94%EA%B0%80-5c274bdf70ce)
- [LLM Wiki: 블로그 글을 살아있는 지식 베이스로 바꾸는 방법 (Benjamin's Dev Blog)](https://benjamin0326.github.io/ai/2026/04/19/LLM_Wiki.html)
- [LLM-Wiki - LLM을 활용하여 개인 지식저장소 구축 하기 (GeekNews)](https://news.hada.io/topic?id=28208)
- [plugin:groupmanager (DokuWiki)](https://www.dokuwiki.org/plugin:groupmanager)
- [Where Provenance Ends, Knowledge Decays (jessicatalisman.substack)](https://jessicatalisman.substack.com/p/where-provenance-ends-knowledge-decays)
- [LLM Wiki + Graphify를 직접 만들다 (kouwiki/raw 기존 자료, Abraham Song / brunch)](https://brunch.co.kr/@abrahamsong)
- [Karpathy의 LLM과 Obsidian 지식 결합 (projectresearch.co.kr)](https://projectresearch.co.kr/karpathy%EC%9D%98-llm%EA%B3%BC-obsidian-%EC%A7%80%EC%8B%9D-%EA%B2%B0%ED%95%A9/)
