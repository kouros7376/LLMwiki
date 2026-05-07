[![Graphify: 복잡한 코드베이스를 한눈에 - AI 코딩 어시스턴트를 위한 지식 그래프 도구](https://discuss.pytorch.kr/uploads/default/original/3X/6/8/68b5f8e45b30e01f2e6186a2f0dcdfcfb71850a1.png)

Graphify: 복잡한 코드베이스를 한눈에 - AI 코딩 어시스턴트를 위한 지식 그래프 도구876×451 35.3 KB

](https://discuss.pytorch.kr/uploads/default/original/3X/6/8/68b5f8e45b30e01f2e6186a2f0dcdfcfb71850a1.png "Graphify: 복잡한 코드베이스를 한눈에 - AI 코딩 어시스턴트를 위한 지식 그래프 도구")

## Graphify 소개

소프트웨어 개발 환경에서 Claude Code, OpenAI Codex, OpenCode, OpenClaw와 같은 AI 코딩 어시스턴트의 활용이 점차 기본이 되어가고 있습니다. 그러나 프로젝트의 규모가 커지고 아키텍처가 복잡해짐에 따라, 수많은 코드 파일과 설계 문서, 논문, 그리고 아키텍처 다이어그램 등을 AI가 한 번에 이해하고 분석하는 데에는 뚜렷한 한계가 존재합니다. 기존의 단순한 RAG(검색 증강 생성) 방식이나 컨텍스트 윈도우에 모든 텍스트를 밀어 넣는 방식은 토큰 비용을 기하급수적으로 증가시킬 뿐만 아니라, 시스템의 구조적인 맥락을 놓치게 만들곤 합니다. 실제로 많은 개발자들은 코드 외에도 관련된 논문이나 트윗, 스크린샷 등을 모아둔 폴더를 가지고 있지만, 이를 AI 어시스턴트가 유기적으로 파악하게 만드는 것은 매우 어려운 일이었습니다.

**Graphify**는 이러한 문제를 해결하기 위해 등장한 **오픈소스 지식 그래프 스킬(Knowledge Graph Skill) 도구**입니다. 이 프로젝트는 멀티모달(Multi-modal) 코드베이스를 AI 코딩 어시스턴트가 구조적으로 쉽게 이해할 수 있도록 돕습니다. 소스 코드뿐만 아니라 마크다운 문서, PDF 논문, 심지어 시스템 아키텍처를 그린 이미지 다이어그램까지 모두 분석하여 상호 연결된 쿼리 가능한 지식 그래프(Knowledge Graph)를 구축해 냅니다.

이 도구의 가장 큰 특징은 단순히 '어떤 코드가 있는지'를 넘어서, 코드가 '왜(Why)' 그렇게 설계되었는지를 추적할 수 있게 해준다는 점입니다. Graphify를 활용하면 개발자와 AI는 시스템의 중심이 되는 '갓 노드(God nodes)'를 식별하고, 코드와 문서 사이의 예상치 못한 의존성을 발견할 수 있습니다. 수만 단어에 달하는 방대한 프로젝트에서도 토큰 사용량을 혁신적으로 줄이면서 전체 아키텍처의 큰 그림을 탐색할 수 있게 해주는, 강력하고 효율적인 개발 보조 도구입니다.

## Graphify가 만들어진 계기: 안드레이 카파시의 `/raw 디렉토리` 워크플로우

Graphify라는 도구가 탄생하게 된 배경에는 인공지능 분야의 저명한 연구자인 안드레이 카파시(Andrej Karpathy)의 개인적인 작업 방식이 자리 잡고 있습니다. 최근 카파시는 복잡한 분류 체계 없이 프로젝트 디렉토리 내에 `/raw`라는 이름의 하위 디렉토리를 하나 만들고, 그 안에 관련 논문(PDF), 영감을 주는 트윗, 아키텍처 스크린샷, 개인적인 메모, 참고할 코드 스니펫 등을 가리지 않고 던져 넣는 방식을 공유했습니다. 그리고 필요할 때마다 이 폴더 전체를 AI 어시스턴트의 컨텍스트 윈도우에 밀어 넣어 질문하는 방식을 사용했습니다.

하지만 이 직관적인 `/raw` 디렉토리 워크플로우에는 치명적인 실무적 한계가 존재했습니다. 수십 개의 파일, 수만 단어에 달하는 텍스트와 다이어그램을 매 질문마다 AI에게 읽히다 보니 기하급수적인 토큰 비용이 발생했습니다. 또한, 파일들이 병렬로 나열되어 있을 뿐 서로 어떤 연관이 있는지 AI가 명확히 파악하기 어려워 환각(Hallucination)을 일으킬 여지도 있었습니다.

AI 연구원인 사피 샴시(Safi Shamsi)는 이 워크플로우의 한계를 극복하고자 Graphify를 개발했습니다. 개발자가 자료를 무작위로 던져 넣는 편리함은 유지하되, 파편화된 자료들 사이의 관계를 추출하여 하나의 유기적인 멀티모달 지식 그래프로 엮어냅니다. 원본 텍스트 전체를 매번 읽히는 대신 압축되고 명확하게 관계가 정의된 그래프 데이터만 AI에게 전달함으로써, 말뭉치 벤치마크 테스트(Corpus Benchmark Test)에서 매 쿼리 당 토큰 사용량을 **약 71.5배**나 줄이는 혁신적인 결과를 증명해 냈습니다.

## 다른 코드 탐색 도구와 비교

코드베이스를 분석하고 검색하는 영역에는 이미 여러 도구들이 존재하지만, Graphify는 지식 그래프와 멀티모달 분석 측면에서 확실한 차별점을 갖습니다.

- **Sourcegraph:** 훌륭한 엔터프라이즈급 크로스 리포지토리(Cross-repo) 코드 검색 및 내비게이션 기능을 제공합니다. 하지만 Graphify와 같이 구조화된 '지식 그래프'를 형성하는 것은 아니며, 설계의 의미론적(Semantic) 맥락을 제한적으로만 추출합니다.
- **Code2Vec:** 함수 수준의 임베딩과 벡터 검색 및 분류에 특화된 프로젝트입니다. 반면 Graphify는 벡터 임베딩 과정 자체를 생략하고 명시적인 그래프 구조를 형성하며, 코드 외에 문서나 다이어그램 등 멀티모달 입력을 처리한다는 점에서 큰 차이가 있습니다.
- **Neo4j:** 강력한 Cypher 쿼리를 지원하는 범용 그래프 데이터베이스입니다. 그래프 구조 저장에는 탁월하지만, 코드를 분석하여 지식 그래프를 '자동으로 생성'해 주는 도구가 아니므로 Graphify와는 목적과 사용 방식이 다릅니다. (단, Graphify는 `--neo4j` 옵션 등을 통해 Neo4j 연동을 지원합니다.)

## Graphify의 동작 및 특징

[![image](https://discuss.pytorch.kr/uploads/default/optimized/3X/c/b/cb8c92f63ad40c52f30665da524bb3f817465100_2_1028x560.jpeg)

image1408×768 239 KB

](https://discuss.pytorch.kr/uploads/default/original/3X/c/b/cb8c92f63ad40c52f30665da524bb3f817465100.jpeg "image")

### 벡터 임베딩 없는 2단계(Two-Pass) 아키텍처

Graphify는 무거운 벡터 데이터베이스나 임베딩 과정 없이 토폴로지(Topology) 기반의 그래프 클러스터링을 수행합니다. 이 과정은 **2단계의 파이프라인**으로 구성됩니다:

- **결정론적 AST 추출 (로컬 분석):** Tree-sitter를 활용하여 Python, JavaScript, Go, Java 등 다양한 언어로 작성된 코드 파일에서 클래스, 함수, 임포트, 호출 그래프(Call graphs), 독스트링, 주석 등을 추출합니다. 이 과정에서는 LLM이 사용되지 않으며 오직 로컬 환경에서 코드를 구문 분석합니다.
- **의미론적 추출 (LLM 서브에이전트 활용):** 공식 문서, PDF 형식의 논문, 혹은 시스템 다이어그램 등 멀티모달 자료들에 대해 Claude 등의 LLM 비전 모델이 병렬로 실행됩니다. 이를 통해 핵심 개념과 설계 근거, 노드 간의 관계를 자연어 의미론적으로 추출합니다.

추출된 데이터는 NetworkX 그래프로 병합되며, 에지(Edge)의 밀도를 활용하는 Leiden 알고리즘을 통해 커뮤니티(Community)로 클러스터링됩니다. 시맨틱 유사성 시그널 자체가 그래프 구조에 반영되므로 별도의 벡터 스토어가 필요하지 않습니다.

### 핵심 기능: 멀티모달 추출과 갓 노드 식별

- **신뢰도 태깅 시스템:** 코드, 문서, 이미지에서 추출된 모든 관계(Edge)는 출처에 따라 명확히 태깅됩니다. 소스코드에서 직접 찾은 구조적 연결은 `EXTRACTED`(신뢰도 1.0)로, 문서나 이미지에서 추론된 의미론적 연결은 신뢰도 점수와 함께 `INFERRED`로, 불확실한 연결은 `AMBIGUOUS`로 분류되어 투명성을 제공합니다.
- **갓 노드(God Nodes):** 전체 시스템 그래프 내에서 가장 높은 차수(Degree)를 가진, 즉 가장 많은 요소들과 연결된 핵심 개념을 자동으로 식별합니다. 아키텍처의 중심축을 파악하는 데 유용합니다.
- **깜짝 연결성(Surprising connections):** 단순히 함수가 함수를 호출하는 뻔한 연결을 넘어, 복합 점수 시스템을 통해 흥미로운 교차 연결을 찾아냅니다. 코드와 논문 간의 연결이나 문서 내 숨겨진 설계 의도를 포착하여 그 이유를 일반 언어로 설명해 줍니다.

### 출력 포맷과 캐싱 시스템

Graphify를 실행하면 `graphify-out/` 폴더 하위에 다음과 같은 유용한 파일들이 생성됩니다.

- `graph.html`: 시각적으로 탐색, 검색, 필터링이 가능한 대화형 뷰어입니다.
- `GRAPH_REPORT.md`: 갓 노드, 깜짝 연결성, 그래프가 대답하기 좋은 추천 질문 4-5개가 담긴 요약 보고서입니다.
- `graph.json`: 며칠이 지난 후에도 코드를 다시 읽을 필요 없이 즉시 쿼리할 수 있도록 만들어진 영구 그래프 데이터입니다.
- `cache/`: SHA256 캐싱을 통해 파일을 수정할 경우 변경된 파일만 다시 처리하여 성능을 극대화합니다.

### 로컬 중심의 보안 및 프라이버시

Graphify는 보안을 최우선으로 설계되었습니다. 소스 코드에 대한 분석(AST 파싱)은 전적으로 로컬에서 Tree-sitter를 통해 이루어지며, 사용자의 원시 소스 코드는 외부의 서드파티 LLM 모델 서버로 절대 전송되지 않습니다. 문서나 이미지의 의미론적 분석을 위해 네트워크 호출이 발생할 때는 사용자가 이미 구성해 둔 자체 API 키만을 사용합니다.

또한 입력 크기 및 시간제한, 경로 캡슐화 방어, 노드 라벨의 HTML 이스케이프 등을 철저하게 적용하여 SSRF(서버 측 요청 위조) 및 크로스 사이트 스크립팅(XSS), YAML 인젝션 공격을 사전에 차단합니다.

## Graphify 설치 및 사용 방법

Graphify는 로컬 환경에서 작동하며 터미널 명령어를 통해 직관적으로 사용할 수 있습니다. 현재 Claude Code, OpenAI Codex, OpenCode, OpenClaw 등의 주요 AI 코딩 어시스턴트를 지원합니다.

### 사전 요구 사항 및 기본 설치

Graphify를 실행하기 위해서는 **Python 3.10 이상**의 환경과 지원되는 AI 코딩 어시스턴트 중 하나가 필요합니다.

현재 PyPI에 등록된 공식 패키지 이름은 `graphifyy`이지만, 설치 후 실행하는 CLI 명령어는 `graphify`를 사용합니다.

```bash
# 기본 설치 명령어
pip install graphifyy && graphify install
```

사용 중인 어시스턴트 플랫폼에 따라 최초 설치 시 아래와 같이 `--platform` 옵션을 지정해야 할 수 있습니다. (Claude Code의 경우 기본값이므로 생략 가능합니다.)

- **Claude Code:** `graphify install`
- **Codex:** `graphify install --platform codex`
- **OpenCode:** `graphify install --platform opencode`
- **OpenClaw:** `graphify install --platform claw`

> **참고:** Codex 사용자의 경우, 병렬 추출 기능을 위해 `~/.codex/config.toml` 파일의 `[features]` 항목 아래에 `multi_agent = true`를 추가해야 합니다.

### 어시스턴트에 '항상 켜기(Always-on)' 연동하기

Graphify의 진정한 위력은 어시스턴트가 코드를 탐색하기 전에 전체 아키텍처(지식 그래프)를 먼저 참고하도록 강제할 때 나타납니다. 프로젝트 폴더로 이동한 후, 사용하는 플랫폼에 맞는 명령어를 한 번만 실행해 두면 됩니다.

| **플랫폼** | **설치 명령어** | **삭제 명령어** |
| --- | --- | --- |
| **Claude Code** | `graphify claude install` | `graphify claude uninstall` |
| **Codex** | `graphify codex install` | `graphify codex uninstall` |
| **OpenCode** | `graphify opencode install` | `graphify opencode uninstall` |
| **OpenClaw** | `graphify claw install` | `graphify claw uninstall` |

이 설정을 완료하면, AI 어시스턴트는 무작정 전체 파일을 검색(Grep)하는 대신, 먼저 `GRAPH_REPORT.md`를 읽고 전체 구조와 핵심 노드(God nodes)를 파악한 뒤 더 똑똑하게 답변을 생성하게 됩니다.

### 주요 명령어 및 실전 활용법

AI 어시스턴트 프롬프트 창이나 터미널에서 직접 그래프를 생성하고 질의할 수 있습니다.

**기본 그래프 생성 및 관리**

- `/graphify .`: 현재 위치한 디렉터리 전체를 스캔하여 지식 그래프를 빌드합니다.
- `/graphify ./raw`: 특정 폴더(예: 문서나 논문을 모아둔 폴더)만 지정하여 빌드합니다.
- `/graphify ./raw --update`: 변경된 파일만 캐시(SHA256)를 통해 식별하여 빠르고 효율적으로 기존 그래프를 업데이트합니다.
- `/graphify ./raw --watch`: 백그라운드에 띄워두면 코드나 문서가 수정될 때마다 실시간으로 그래프를 자동 동기화합니다.
- `graphify hook install`: Git 훅(Post-commit, Post-checkout)을 설치하여 커밋이나 브랜치 이동 시 그래프를 자동으로 재빌드합니다.

**지식 그래프 직접 탐색 (Deep Dive)**

항상 켜기(Always-on) 모드가 전체적인 '지도'를 제공한다면, 아래 명령어들은 그 지도를 바탕으로 '구체적인 경로'를 탐색할 때 사용합니다.

- `/graphify query "질문 내용"`: 전체 파일이 아닌, 구축된 지식 그래프를 바탕으로 아키텍처에 대한 질의응답을 수행합니다.
- `/graphify path "출발노드" "도착노드"`: 예를 들어 `/graphify path "DigestAuth" "Response"`와 같이 입력하면 두 개념이나 모듈 사이의 연결 경로를 정확하게 추적해 줍니다.
- `/graphify explain "개념/모듈명"`: 특정 클래스, 논문의 개념, 아키텍처 모듈이 그래프 내에서 어떤 역할과 의존성을 가지는지 상세히 설명해 줍니다.

## 라이선스

Graphify 프로젝트는 [MIT License](https://github.com/safishamsi/graphify/blob/v3/LICENSE?utm_source=pytorchkr&ref=pytorchkr)로 공개 및 배포되고 있습니다. 개인 및 상업적 용도 모두 무료로 사용할 수 있으며, NetworkX 및 Tree-sitter와 같은 핵심 종속성 도구들도 충돌 없는 허용적 라이선스를 사용합니다.

## ![:house:](https://discuss.pytorch.kr/images/emoji/fluentui/house.png?v=15 ":house:") Graphify 공식 홈페이지

[Graphify](https://graphify.net/?utm_source=pytorchkr&ref=pytorchkr)

![](https://graphify.net/og.png)

### [Graphify — Knowledge Graph Skill for AI Coding Assistants](https://graphify.net/?utm_source=pytorchkr&ref=pytorchkr)

Graphify builds queryable, multi-modal knowledge graphs from code, docs, papers and diagrams to supercharge AI coding assistants.

## ![:github:](https://discuss.pytorch.kr/uploads/default/original/2X/7/70a6220c603eed42089b4f67366225849e119e20.svg?v=15 ":github:") Graphify 프로젝트 GitHub 저장소

[github.com](https://github.com/safishamsi/graphify?utm_source=pytorchkr&ref=pytorchkr)

![](https://opengraph.githubassets.com/9a46d8aeaa0bd6c5464c529bc1881863/safishamsi/graphify)

### [GitHub - safishamsi/graphify: AI coding assistant skill (Claude Code, Codex,...](https://github.com/safishamsi/graphify?utm_source=pytorchkr&ref=pytorchkr)

AI coding assistant skill (Claude Code, Codex, OpenCode, OpenClaw, Factory Droid, Trae). Turn any folder of code, docs, papers, or images into a queryable knowledge graph

  
  

---

*이 글은 GPT 모델로 정리한 글을 바탕으로 한 것으로, 원문의 내용 또는 의도와 다르게 정리된 내용이 있을 수 있습니다. 관심있는 내용이시라면 원문도 함께 참고해주세요! 읽으시면서 어색하거나 잘못된 내용을 발견하시면 덧글로 알려주시기를 부탁드립니다.* ![:hugs:](https://discuss.pytorch.kr/images/emoji/fluentui/hugs.png?v=15 ":hugs:")

[![:pytorch:](https://discuss.pytorch.kr/uploads/default/original/2X/f/fa98c2196c22febe7475e503792febf39ba7a0de.svg?v=15 ":pytorch:")파이토치 한국 사용자 모임![:south_korea:](https://discuss.pytorch.kr/images/emoji/fluentui/south_korea.png?v=15 ":south_korea:")](https://pytorch.kr/)이 정리한 이 글이 유용하셨나요? [회원으로 가입](https://discuss.pytorch.kr/signup)하시면 주요 글들을 이메일![:love_letter:](https://discuss.pytorch.kr/images/emoji/fluentui/love_letter.png?v=15 ":love_letter:")로 보내드립니다! (기본은 Weekly지만 [Daily로 변경도 가능](https://discuss.pytorch.kr/my/preferences/emails)합니다.)

![:wrapped_gift:](https://discuss.pytorch.kr/images/emoji/fluentui/wrapped_gift.png?v=15 ":wrapped_gift:") 아래![:down_right_arrow:](https://discuss.pytorch.kr/images/emoji/fluentui/down_right_arrow.png?v=15 ":down_right_arrow:")쪽에 좋아요![:+1:](https://discuss.pytorch.kr/images/emoji/fluentui/+1.png?v=15 ":+1:")를 눌러주시면 새로운 소식들을 정리하고 공유하는데 힘이 됩니다~ ![:star_struck:](https://discuss.pytorch.kr/images/emoji/fluentui/star_struck.png?v=15 ":star_struck:")