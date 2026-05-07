**Notice**
- [\[팁\]주옥같은 안드로이드 팁](https://javaexpert.tistory.com/notice/135)

**Recent Posts**
- [Codex를 UI/UX 마법사처럼 만드는 디자인 ⋯](https://javaexpert.tistory.com/1758)
- [Open Design → Flutter 앱으로 디⋯](https://javaexpert.tistory.com/1757)
- [당신은 하나의 텍스트 파일입니다](https://javaexpert.tistory.com/1756)
- [mattpocock/skills — AI 코딩 에⋯](https://javaexpert.tistory.com/1755)

**Recent Comments**
- [행복한 수지아빠님 도움이 많이 되는 글 감사합니다! 💖⋯](https://javaexpert.tistory.com/1758#comment23027023)
- [행복한 수지아빠님 글을 읽는 동안 공감이 계속 쌓였네요⋯](https://javaexpert.tistory.com/1758#comment23026707)
- [행복한 수지아빠님 읽자마자 메모하고 싶은 부분이 많았습⋯](https://javaexpert.tistory.com/1757#comment23024193)
- [행복한 수지아빠님 꼼꼼하게 정리된 글을 보니 정말 정성⋯](https://javaexpert.tistory.com/1757#comment23024029)

**Link**
- [ambientmp/ambientmp Changelog](http://ambientmp.hg.sourceforge.net/hgweb/ambientmp/ambientmp/)
- [Flash & Flask](https://quadflask.tistory.com/)

| 일 | 월 | 화 | 수 | 목 | 금 | 토 |
| --- | --- | --- | --- | --- | --- | --- |
|  |  |  |  |  | 1 | [2](https://javaexpert.tistory.com/archive/20260502) |
| [3](https://javaexpert.tistory.com/archive/20260503) | [4](https://javaexpert.tistory.com/archive/20260504) | 5 | [6](https://javaexpert.tistory.com/archive/20260506) | 7 | 8 | 9 |
| 10 | 11 | 12 | 13 | 14 | 15 | 16 |
| 17 | 18 | 19 | 20 | 21 | 22 | 23 |
| 24 | 25 | 26 | 27 | 28 | 29 | 30 |
| 31 |  |  |  |  |  |  |

**Tags**
- [무료](https://javaexpert.tistory.com/tag/%EB%AC%B4%EB%A3%8C)
- [스타](https://javaexpert.tistory.com/tag/%EC%8A%A4%ED%83%80)
- [Flutter](https://javaexpert.tistory.com/tag/Flutter)
- [코인](https://javaexpert.tistory.com/tag/%EC%BD%94%EC%9D%B8)
- [자동갱신](https://javaexpert.tistory.com/tag/%EC%9E%90%EB%8F%99%EA%B0%B1%EC%8B%A0)
- [nestjs](https://javaexpert.tistory.com/tag/nestjs)
- [FIGMA](https://javaexpert.tistory.com/tag/FIGMA)
- [MCP](https://javaexpert.tistory.com/tag/MCP)
- [미니앱](https://javaexpert.tistory.com/tag/%EB%AF%B8%EB%8B%88%EC%95%B1)
- [결제](https://javaexpert.tistory.com/tag/%EA%B2%B0%EC%A0%9C)
- [telegram](https://javaexpert.tistory.com/tag/telegram)
- [무료 텔레그램 채굴](https://javaexpert.tistory.com/tag/%EB%AC%B4%EB%A3%8C%20%ED%85%94%EB%A0%88%EA%B7%B8%EB%9E%A8%20%EC%B1%84%EA%B5%B4)
- [megamine](https://javaexpert.tistory.com/tag/megamine)
- [Nginx](https://javaexpert.tistory.com/tag/Nginx)
[more](https://javaexpert.tistory.com/tag)

Today

57

Total

2,279,025

**관리 메뉴**
- [글쓰기](https://javaexpert.tistory.com/manage/entry/post "글쓰기")
- [방명록](https://javaexpert.tistory.com/guestbook "방명록")
- [관리](https://javaexpert.tistory.com/manage "관리")

## 오늘도 공부

## Graphify: 카파시의 위키 구현 버전 본문

**[AI](https://javaexpert.tistory.com/category/AI)**

### Graphify: 카파시의 위키 구현 버전

행복한 수지아빠 2026. 4. 9. 09:27

이 글은 GitHub 레포지토리 **graphify** 를 기준으로, 이 도구가 왜 필요한지부터 핵심 개념, 동작 방식, 설치 방법, 실무 활용 포인트까지 한 번에 정리한 글입니다. 공개된 README와 아키텍처 문서상 확인되는 범위에서 설명합니다. ([GitHub](https://github.com/safishamsi/graphify))

요즘 AI 코딩 도구를 쓰다 보면 비슷한 문제가 자주 생깁니다. 파일은 많고, 문서와 코드와 이미지가 섞여 있고, 모델은 매번 원본 파일을 다시 훑느라 느리고 비싸고 맥락을 놓칩니다. graphify는 이 문제를 “파일 모음”을 **질의 가능한 지식 그래프** 로 바꾸는 방식으로 풀려는 도구입니다. ([GitHub](https://github.com/safishamsi/graphify))

특히 이 도구는 Claude Code, Codex, OpenCode, OpenClaw, Factory Droid 같은 AI 코딩 어시스턴트와 함께 쓰는 것을 전제로 설계되어 있습니다. 코드만이 아니라 PDF, 마크다운, 스크린샷, 다이어그램, 화이트보드 사진까지 하나의 그래프로 연결한다는 점이 핵심입니다. ([GitHub](https://github.com/safishamsi/graphify))

## 왜 이 문제가 중요한가

기존 방식은 대부분 **원문 재탐색 중심** 입니다. 질문이 들어올 때마다 AI가 grep, glob, 파일 열기, 문서 재읽기를 반복합니다. 코드베이스가 작을 때는 괜찮지만, 프로젝트가 커질수록 비용과 시간이 급격히 늘어납니다. graphify README도 이 문제를 “raw 파일을 계속 다시 읽는 방식”의 한계로 설명합니다. ([GitHub](https://github.com/safishamsi/graphify))

실무에서 특히 불편한 지점은 다음과 같습니다. ([GitHub](https://github.com/safishamsi/graphify))

- **코드와 문서가 분리되어 있다**  
	구현은 코드에 있고, 의사결정 배경은 README·설계 문서·주석·이미지에 흩어져 있습니다. AI가 한쪽만 보면 “무엇을 하는지”는 알아도 “왜 이렇게 만들었는지”를 놓치기 쉽습니다. ([GitHub](https://github.com/safishamsi/graphify))
- **질문할 때마다 원본을 다시 읽는다**  
	같은 프로젝트를 여러 번 물어보면 비슷한 파일을 반복해서 읽게 됩니다. graphify는 이를 피하기 위해 graph.json과 캐시를 남겨 재사용하도록 설계되어 있습니다. ([GitHub](https://github.com/safishamsi/graphify))
- **키워드 검색은 구조를 잘 보여주지 못한다**  
	“이 함수가 왜 여기 있지?”, “이 컴포넌트와 저 모듈은 어떻게 연결되지?” 같은 질문은 단순 텍스트 검색보다 관계 구조가 중요합니다. graphify는 이런 관계를 노드와 엣지로 정리합니다. ([GitHub](https://github.com/safishamsi/graphify))
- **멀티모달 자료를 함께 이해하기 어렵다**  
	코드, 논문, PDF, 스크린샷, 다이어그램을 같이 보는 작업은 일반적인 코드 인덱서만으로 처리하기 어렵습니다. graphify는 이 입력들을 하나의 그래프로 합치려 합니다. ([GitHub](https://github.com/safishamsi/graphify))
- **AI가 추론한 내용과 실제 근거를 구분하기 어렵다**  
	답은 그럴듯하지만 어디까지가 사실인지 애매한 경우가 많습니다. graphify는 관계를 EXTRACTED, INFERRED, AMBIGUOUS로 태깅해 구분합니다. ([GitHub](https://github.com/safishamsi/graphify))

## graphify란 무엇인가

**graphify는 코드·문서·이미지 같은 프로젝트 자산을 읽어, AI가 재사용 가능한 지식 그래프로 변환해 주는 AI 코딩 어시스턴트용 스킬이자 Python 라이브러리입니다.** ([GitHub](https://github.com/safishamsi/graphify))

쉽게 말하면, “프로젝트 폴더를 그냥 파일 집합으로 두지 말고, 개념·구성요소·관계가 연결된 지도처럼 바꾸자”는 접근입니다. 그래서 AI가 매번 원문을 뒤지는 대신, 먼저 그래프와 리포트를 보고 전체 구조를 파악한 뒤 필요한 부분만 깊게 들어갈 수 있습니다. ([GitHub](https://github.com/safishamsi/graphify))

기존 방식과의 차이는 분명합니다. 일반적인 검색 기반 접근이 “어떤 단어가 어디에 있나”를 찾는 데 강하다면, graphify는 “무엇이 무엇과 어떻게 연결되는가”를 먼저 드러냅니다. README는 이를 “assistant gets a map, then navigates precisely”라는 식으로 설명합니다. ([GitHub](https://github.com/safishamsi/graphify))

## 핵심 특징

- **코드 + 문서 + 이미지까지 처리하는 멀티모달 입력**  
	PDF, markdown, screenshot, diagram, whiteboard photo까지 그래프에 통합할 수 있습니다. ([GitHub](https://github.com/safishamsi/graphify))
- **코드는 AST 기반으로 먼저 분석**  
	코드 파일은 LLM 없이 deterministic AST pass로 구조를 추출합니다. ([GitHub](https://github.com/safishamsi/graphify))
- **문서와 이미지에는 병렬 서브에이전트 사용**  
	Claude subagent가 병렬로 개념, 관계, 설계 의도를 추출합니다. ([GitHub](https://github.com/safishamsi/graphify))
- **임베딩 없이 그래프 토폴로지로 클러스터링**  
	NetworkX 그래프 위에 Leiden community detection을 적용합니다. 별도 벡터 DB가 필수는 아닙니다. ([GitHub](https://github.com/safishamsi/graphify))
- **관계 신뢰도 레이블 제공**  
	EXTRACTED, INFERRED, AMBIGUOUS를 통해 근거 수준을 구분합니다. ([GitHub](https://github.com/safishamsi/graphify))
- **출력물이 실무 친화적**  
	graph.html, GRAPH\_REPORT.md, graph.json, 캐시 디렉터리를 생성해 시각화·요약·재질의·증분 업데이트를 지원합니다. ([GitHub](https://github.com/safishamsi/graphify))

## 실제로 어떤 효과가 있는가

README에는 **“raw 파일을 직접 읽는 것 대비 질의당 71.5배 적은 토큰 사용”** 이라는 표현이 나옵니다. 다만 이 수치는 레포지토리에서 제시한 값이므로, 일반적인 모든 프로젝트에서 동일하게 재현된다고 단정하기보다는 **제공된 자료 기준의 주장** 으로 보는 것이 맞습니다. ([GitHub](https://github.com/safishamsi/graphify))

실제 효과는 크게 네 가지로 이해할 수 있습니다. ([GitHub](https://github.com/safishamsi/graphify))

1. **반복 질문 비용 감소**  
	그래프와 캐시가 남기 때문에, 같은 프로젝트를 여러 번 물어볼수록 이점이 커집니다. ([GitHub](https://github.com/safishamsi/graphify))
2. **구조 중심 탐색 가능**  
	GRAPH\_REPORT.md가 god node, community, surprising connection 같은 요약을 제공해, AI가 처음부터 구조를 보고 접근하게 만듭니다. ([GitHub](https://github.com/safishamsi/graphify))
3. **정확한 깊이 탐색 지원**  
	/graphify query, /graphify path, /graphify explain 같은 명령으로 특정 연결 경로와 관계를 더 깊게 추적할 수 있습니다. ([GitHub](https://github.com/safishamsi/graphify))
4. **원본 근거와 추론의 분리**  
	관계마다 confidence 라벨이 있어, AI 답변을 검증하기 쉬워집니다. ([GitHub](https://github.com/safishamsi/graphify))

효과가 특히 큰 상황은 다음과 같습니다. 코드만 있는 작은 스크립트보다, **대형 코드베이스**, **설계 문서가 많은 프로젝트**, **논문·이미지·메모가 섞인 연구형 저장소**, **AI에게 반복적으로 아키텍처 질문을 던지는 팀 환경** 에서 더 유리합니다. 이 평가는 README와 아키텍처 문서의 설계 방향을 바탕으로 한 해석입니다. ([GitHub](https://github.com/safishamsi/graphify))

## 동작 원리 / 구조

graphify의 파이프라인은 아키텍처 문서에 비교적 명확하게 나와 있습니다. 전체 흐름은 다음과 같습니다. ([GitHub](https://github.com/safishamsi/graphify/blob/v3/ARCHITECTURE.md))

1. **detect**  
	입력 디렉터리에서 분석할 파일을 수집합니다..graphifyignore를 통해 제외 패턴도 적용할 수 있습니다. ([GitHub](https://github.com/safishamsi/graphify))
2. **extract**  
	각 파일에서 노드와 엣지를 뽑아냅니다. 코드 파일은 tree-sitter 기반 AST로, 문서·이미지는 별도 추출 흐름으로 처리됩니다. ([GitHub](https://github.com/safishamsi/graphify))
3. **build\_graph**  
	파일별 추출 결과를 NetworkX 그래프로 합칩니다. ([GitHub](https://github.com/safishamsi/graphify))
4. **cluster**  
	그래프를 커뮤니티 단위로 묶습니다. README는 Leiden community detection을 사용한다고 설명합니다. ([GitHub](https://github.com/safishamsi/graphify))
5. **analyze**  
	god node, surprising connection, suggested question 같은 분석 결과를 만듭니다. ([GitHub](https://github.com/safishamsi/graphify))
6. **report / export**  
	GRAPH\_REPORT.md, graph.html, graph.json, 필요 시 graph.svg, GraphML, Neo4j용 cypher, Obsidian vault 등으로 내보냅니다. ([GitHub](https://github.com/safishamsi/graphify))

아키텍처 문서 기준으로 각 단계는 별도 모듈 함수로 분리되어 있고, dict와 NetworkX graph를 통해 데이터를 주고받습니다. 공유 상태를 최소화하고, 출력은 graphify-out/ 중심으로 관리합니다. ([GitHub](https://github.com/safishamsi/graphify/blob/v3/ARCHITECTURE.md))

또 하나 중요한 점은 **관계 스키마** 입니다. 각 extractor는 nodes와 edges를 반환하고, edge는 relation과 confidence를 가집니다. 그래서 “어디서 나온 관계인지”, “직접 추출인지 추론인지”를 뒤에서 다시 추적할 수 있습니다. ([GitHub](https://github.com/safishamsi/graphify/blob/v3/ARCHITECTURE.md))

## 설치 / 사용 방법

문서상 요구사항은 **Python 3.10 이상** 이며, Claude Code, Codex, OpenCode, OpenClaw, Factory Droid 중 하나가 필요합니다. 현재 PyPI 패키지 이름은 임시로 graphifyy이고, CLI 명령은 여전히 graphify입니다. ([GitHub](https://github.com/safishamsi/graphify))

### 1) 설치

```cmake
pip install graphifyy && graphify install
```

Claude Code 외 플랫폼은 다음처럼 설치 플래그를 다르게 줍니다. ([GitHub](https://github.com/safishamsi/graphify))

```sql
# Claude Code (Windows 자동 감지 가능)
graphify install --platform windows

# Codex
graphify install --platform codex

# OpenCode
graphify install --platform opencode

# OpenClaw
graphify install --platform claw

# Factory Droid
graphify install --platform droid
```

Codex는 병렬 추출을 위해 ~/.codex/config.toml의 \[features\] 아래에 multi\_agent = true 설정이 추가로 필요하다고 README에 적혀 있습니다. OpenClaw는 병렬 에이전트 지원이 아직 이른 단계라 순차 추출을 사용한다고 안내합니다. ([GitHub](https://github.com/safishamsi/graphify))

### 2) 첫 그래프 만들기

```erlang
/graphify .
```

Codex에서는 / 대신 $를 사용합니다. ([GitHub](https://github.com/safishamsi/graphify))

```bash
$graphify .
```

실행 후에는 보통 다음 출력물이 생깁니다. ([GitHub](https://github.com/safishamsi/graphify))

```csharp
graphify-out/
├── graph.html
├── GRAPH_REPORT.md
├── graph.json
└── cache/
```

### 3) 제외할 폴더 설정

```bash
# .graphifyignore
vendor/
node_modules/
dist/
*.generated.py
```

.graphifyignore 문법은.gitignore와 같습니다. 실행한 루트 기준 상대 경로로 매칭됩니다. ([GitHub](https://github.com/safishamsi/graphify))

### 4) 자주 쓰는 실행 옵션

```lua
/graphify ./raw --mode deep
/graphify ./raw --update
/graphify ./raw --cluster-only
/graphify ./raw --no-viz
/graphify ./raw --watch
/graphify ./raw --wiki
/graphify ./raw --svg
/graphify ./raw --graphml
/graphify ./raw --neo4j
/graphify ./raw --mcp
```

문서상 이 옵션들은 각각 더 공격적인 추론, 변경 파일만 재추출, 재클러스터링, HTML 생략, 파일 변경 감시, 에이전트가 읽기 쉬운 wiki 생성, SVG/GraphML/Neo4j/MCP 출력 등에 대응합니다. ([GitHub](https://github.com/safishamsi/graphify))

### 5) 어시스턴트가 항상 그래프를 먼저 보게 만들기

README는 이 방식을 권장합니다. 플랫폼별로 install 명령을 한 번 더 실행하면, Claude Code는 CLAUDE.md와 PreToolUse hook을 설치하고, 다른 플랫폼은 AGENTS.md에 규칙을 작성합니다. 목적은 AI가 raw 파일을 먼저 뒤지기 전에 GRAPH\_REPORT.md를 읽게 만드는 것입니다. ([GitHub](https://github.com/safishamsi/graphify))

```cmake
graphify claude install
graphify codex install
graphify opencode install
graphify claw install
graphify droid install
```

## 자주 쓰는 예시 / 활용 시나리오

### 1) 대형 레거시 코드베이스 온보딩

새 팀원이 들어왔을 때 가장 어려운 건 파일 위치가 아니라 **구조와 의사결정 배경** 입니다. graphify는 call graph, imports, docstrings, rationale comments, 문서 관계를 함께 묶어 구조 지도를 만들기 때문에 온보딩 초반에 유용합니다. ([GitHub](https://github.com/safishamsi/graphify))

예를 들면 이런 질문이 가능합니다. ([GitHub](https://github.com/safishamsi/graphify))

```lua
/graphify query "authentication flow is connected to which modules?"
/graphify path "DigestAuth" "Response"
```

### 2) 논문 + 코드 + 메모가 섞인 연구 저장소 정리

README는 논문, 트윗, 스크린샷, 노트를 한 폴더에 쌓아두는 워크플로를 예로 듭니다. 이럴 때 graphify는 URL을 ingest하고 그래프를 업데이트하는 방식으로 자료를 연결합니다. ([GitHub](https://github.com/safishamsi/graphify))

```csharp
/graphify add https://arxiv.org/abs/1706.03762
/graphify add https://x.com/karpathy/status/...
/graphify query "what connects attention to the optimizer?"
```

### 3) 아키텍처 질문에 답하는 AI 보조 도구

항상 켜진 규칙을 설치해 두면, AI가 grep부터 하는 대신 GRAPH\_REPORT.md를 먼저 읽고 큰 구조를 잡습니다. README는 이것을 “구조 기반 탐색”으로 설명합니다. ([GitHub](https://github.com/safishamsi/graphify))

실무에서는 다음과 같은 질문에서 가치가 큽니다.

- 어떤 모듈이 사실상 중심 허브인가
- 문서상 설계 의도와 실제 코드 연결이 맞는가
- 서로 멀어 보이는 두 컴포넌트 사이 경로는 무엇인가 ([GitHub](https://github.com/safishamsi/graphify))

### 4) 증분 업데이트가 필요한 프로젝트

README에 따르면 캐시는 SHA256 기반이고, --update 옵션으로 변경된 파일만 다시 처리할 수 있습니다. 자주 바뀌는 저장소에서 전체 재분석 비용을 줄이는 데 유리합니다. ([GitHub](https://github.com/safishamsi/graphify))

```lua
/graphify ./project --update
```

### 5) 다른 도구로 내보내는 그래프 파이프라인

GraphML, SVG, Neo4j, Obsidian vault, MCP server 같은 출력이 문서에 보입니다. 즉 graphify를 단순 시각화 도구가 아니라, **다른 분석 도구로 넘기는 중간 포맷 생성기** 로도 볼 수 있습니다. ([GitHub](https://github.com/safishamsi/graphify))

## 한계 / 주의할 점

이 도구가 만능은 아닙니다. 문서상 확인되는 범위에서 주의할 점도 분명합니다. ([GitHub](https://github.com/safishamsi/graphify))

- **추론은 어디까지나 추론이다**  
	INFERRED와 AMBIGUOUS는 도움이 되지만, 사람이 검토해야 할 여지가 남습니다. 특히 설계 의도나 의미 관계는 과신하면 안 됩니다. ([GitHub](https://github.com/safishamsi/graphify))
- **지원 플랫폼 전제가 있다**  
	단독 Python 라이브러리로도 쓸 수 있지만, README는 특정 AI 코딩 어시스턴트 환경과의 결합을 중심으로 설명합니다. 즉 “일반적인 CLI 인덱서”와는 포지션이 조금 다릅니다. ([GitHub](https://github.com/safishamsi/graphify))
- **언어/기능 지원은 문서 기준으로 봐야 한다**  
	README에는 19개 언어 지원이라고 적혀 있지만, pyproject.toml에는 tree-sitter-julia도 보입니다. 이런 차이는 문서와 패키지 메타데이터 간 시점 차이일 수 있으므로, 실제 사용 전에는 현재 버전 기준 문서를 다시 확인하는 편이 안전합니다. ([GitHub](https://github.com/safishamsi/graphify))
- **클러스터링 품질은 그래프 품질에 의존한다**  
	임베딩 없이 그래프 토폴로지 기반으로 군집화한다는 점은 단순하고 명확하지만, 결국 초기에 추출된 노드·엣지 품질이 결과를 크게 좌우합니다. 이 평가는 문서에 적힌 구조를 바탕으로 한 해석입니다. ([GitHub](https://github.com/safishamsi/graphify))
- **추가 기능은 optional dependency가 필요할 수 있다**  
	mcp, neo4j, pdf, watch, leiden, office 등은 optional dependency로 나뉘어 있습니다. 기능에 따라 별도 설치가 필요할 수 있습니다. ([GitHub](https://github.com/safishamsi/graphify/blob/v3/pyproject.toml))

## 마무리

graphify의 핵심은 단순합니다. **AI가 프로젝트를 파일 단위로 매번 다시 읽게 하지 말고, 먼저 구조화된 그래프를 보게 하자** 는 것입니다. 이 발상 덕분에 코드, 문서, 이미지, 메모가 섞인 저장소도 하나의 연결된 지도로 다룰 수 있습니다. ([GitHub](https://github.com/safishamsi/graphify))

실무적으로 보면 이 도구는 “정확한 검색”보다 “전체 구조 이해”가 더 중요한 팀에 잘 맞습니다. 특히 대형 코드베이스, 문서가 많은 프로젝트, 연구형 저장소, 그리고 AI 코딩 어시스턴트를 반복적으로 쓰는 환경에서 가치가 커집니다. 반대로 작은 프로젝트나 단순 텍스트 검색만 필요한 경우에는 체감 이점이 상대적으로 작을 수 있습니다. 이 역시 공개된 자료의 설계 의도를 바탕으로 한 정리입니다. ([GitHub](https://github.com/safishamsi/graphify))

결국 graphify는 **AI에게 더 많은 파일을 보여주는 도구** 가 아니라, **AI가 더 적은 비용으로 더 구조적으로 이해하게 만드는 도구** 에 가깝습니다. “왜 이 코드가 이렇게 생겼는지”까지 빠르게 파악해야 하는 개발자에게 특히 유용한 접근입니다. ([GitHub](https://github.com/safishamsi/graphify))

## 핵심 요약

- graphify는 코드·문서·이미지를 **질의 가능한 지식 그래프** 로 바꾸는 AI 코딩 어시스턴트용 도구입니다. ([GitHub](https://github.com/safishamsi/graphify))
- 코드 구조는 AST 기반으로, 문서·이미지는 서브에이전트 기반으로 추출한 뒤 하나의 그래프로 합칩니다. ([GitHub](https://github.com/safishamsi/graphify))
- 출력물은 graph.html, GRAPH\_REPORT.md, graph.json, 캐시 등이며, 반복 질의와 증분 업데이트에 유리합니다. ([GitHub](https://github.com/safishamsi/graphify))
- EXTRACTED, INFERRED, AMBIGUOUS 레이블로 **사실과 추론을 구분** 할 수 있습니다. ([GitHub](https://github.com/safishamsi/graphify))
- 대형 코드베이스, 문서가 많은 프로젝트, 연구 저장소처럼 **구조 이해가 중요한 환경** 에서 특히 강점을 보입니다. ([GitHub](https://github.com/safishamsi/graphify))

#### 'AI' 카테고리의 다른 글

| [DeskRPG: AI 에이전트의 ‘2D 가상 오피스](https://javaexpert.tistory.com/1720) (0) | 2026.04.09 |
| --- | --- |
| [Claude Code를 시작시 초반 구조 자동으로 잡아보자](https://javaexpert.tistory.com/1719) (1) | 2026.04.09 |
| [VoiceStar 정리: 제로샷 TTS에서 음성 길이까지 제어하는 모델은 왜 주목받는가](https://javaexpert.tistory.com/1717) (0) | 2026.04.09 |
| [TypeScript로 AI 에이전트를 만들 때 VoltAgent를 볼 만한 이유](https://javaexpert.tistory.com/1716) (2) | 2026.04.09 |
| [RedditVideoMakerBot: Reddit 영상을 한 번의 명령으로 만드는 자동화 도구](https://javaexpert.tistory.com/1715) (0) | 2026.04.08 |