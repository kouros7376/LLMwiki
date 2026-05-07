---
sensitivity: public
captured_at: 2026-05-07
---

# Graphify 심층 가이드: AI 코딩 어시스턴트용 지식 그래프

[[Graphify]]는 코드, 문서, 이미지를 AI 코딩 어시스턴트가 활용할 수 있는 질의 가능한 지식 그래프로 변환하는 파이썬 라이브러리입니다. 프로젝트 자산을 구조화된 지도로 만들어 AI가 효율적으로 맥락을 이해하고 추론 비용을 절감하도록 돕습니다.

## Graphify란 무엇인가

Graphify는 GitHub 레포지토리 `safishamsi/graphify`를 기반으로, 파일 모음을 **질의 가능한 지식 그래프**로 변환하여 AI 코딩 도구의 한계를 극복하려는 도구입니다. 기존 방식이 원문 재탐색에 의존하여 비용과 시간이 증가하는 문제를 해결하며, 특히 [[Claude_Code]], Codex 등 AI 코딩 어시스턴트와 함께 사용하도록 설계되었습니다. 코드뿐만 아니라 PDF, 마크다운, 스크린샷, 다이어그램, 화이트보드 사진까지 하나의 그래프로 연결하는 멀티모달 입력 처리가 핵심입니다.

## 핵심 특징

Graphify는 다음과 같은 독특한 특징들을 통해 AI의 프로젝트 이해도를 높입니다.

*   **멀티모달 입력 처리**: PDF, Markdown, 스크린샷, 다이어그램, 화이트보드 사진 등 다양한 형식의 프로젝트 자산을 그래프에 통합합니다.
*   **AST 기반 코드 분석**: 코드 파일은 LLM 없이 tree-sitter 기반 AST(추상 구문 트리)를 통해 구조를 결정론적으로 추출합니다.
*   **병렬 서브에이전트 활용**: 문서와 이미지에서는 Claude subagent와 같은 병렬 에이전트를 사용하여 개념, 관계, 설계 의도를 추출합니다.
*   **그래프 토폴로지 기반 클러스터링**: 임베딩 없이 NetworkX 그래프 위에 Leiden community detection을 적용하여 커뮤니티 단위로 묶습니다. 별도의 벡터 DB가 필수는 아닙니다.
*   **관계 신뢰도 레이블**: EXTRACTED (직접 추출), INFERRED (추론), AMBIGUOUS (모호) 세 가지 레이블로 관계의 근거 수준을 구분하여 AI 답변 검증을 돕습니다.
*   **실무 친화적 출력**: `graph.html`, `GRAPH_REPORT.md`, `graph.json`, 캐시 디렉터리 등을 생성하여 시각화, 요약, 재질의, 증분 업데이트를 지원합니다.

## 실제 효과 및 활용 시나리오

Graphify는 AI가 "어떤 단어가 어디에 있나"를 넘어 "무엇이 무엇과 어떻게 연결되는가"를 먼저 파악하게 하여, [[지식의_복리]] 효과를 극대화합니다.

*   **반복 질문 비용 감소**: 그래프와 캐시가 남기 때문에, 같은 프로젝트에 대한 반복 질문 시 원문 재탐색 비용을 크게 줄일 수 있습니다. README에 따르면 "raw 파일을 직접 읽는 것 대비 질의당 71.5배 적은 토큰 사용"을 주장합니다.
*   **구조 중심 탐색 가능**: `GRAPH_REPORT.md`가 god node, community, surprising connection 같은 요약을 제공하여 AI가 프로젝트의 큰 구조를 먼저 이해하도록 돕습니다.
*   **정확한 깊이 탐색 지원**: `/graphify query`, `/graphify path`, `/graphify explain` 같은 명령으로 특정 연결 경로와 관계를 깊게 추적할 수 있습니다.
*   **원본 근거와 추론의 분리**: 관계마다 신뢰도 라벨이 있어 AI 답변의 사실 여부를 검증하기 용이합니다.

특히 **대형 코드베이스**, **설계 문서가 많은 프로젝트**, **논문·이미지·메모가 섞인 연구형 저장소**, **AI에게 반복적으로 아키텍처 질문을 던지는 팀 환경**에서 그 가치가 큽니다.

## 동작 원리 및 구조

Graphify의 파이프라인은 다음과 같은 단계로 구성됩니다.

1.  **detect**: 입력 디렉터리에서 분석할 파일을 수집하며, `.graphifyignore`를 통해 제외 패턴을 적용합니다.
2.  **extract**: 각 파일에서 노드와 엣지를 추출합니다. 코드 파일은 tree-sitter 기반 AST로, 문서 및 이미지는 별도 추출 흐름으로 처리됩니다.
3.  **build_graph**: 파일별 추출 결과를 NetworkX 그래프로 통합합니다.
4.  **cluster**: Leiden community detection을 사용하여 그래프를 커뮤니티 단위로 묶습니다.
5.  **analyze**: god node, surprising connection, suggested question 등 분석 결과를 생성합니다.
6.  **report / export**: `GRAPH_REPORT.md`, `graph.html`, `graph.json` 등의 보고서를 생성하고, 필요시 SVG, GraphML, Neo4j용 cypher, Obsidian vault 등으로 내보냅니다.

각 단계는 별도 모듈 함수로 분리되어 있으며, `dict`와 NetworkX 그래프를 통해 데이터를 주고받습니다. 관계 스키마는 `nodes`와 `edges`를 반환하며, `edge`는 `relation`과 `confidence`를 포함하여 관계의 출처와 신뢰도를 추적할 수 있습니다.

## 설치 및 사용 방법

Graphify는 Python 3.10 이상을 요구하며, [[바이브_코딩_도구]] 중 Claude Code, Codex, OpenCode, OpenClaw, Factory Droid 중 하나와 연동하여 사용합니다.

### 1) 설치

```bash
pip install graphifyy && graphify install
```

플랫폼별 설치 플래그를 다르게 지정할 수 있습니다 (예: `graphify install --platform windows` for Claude Code). Codex의 경우 병렬 추출을 위해 `~/.codex/config.toml`에 `multi_agent = true` 설정이 추가로 필요합니다.

### 2) 첫 그래프 만들기

```bash
/graphify .
# Codex에서는 $graphify .
```

실행 후 `graphify-out/` 디렉터리에 `graph.html`, `GRAPH_REPORT.md`, `graph.json`, `cache/` 등이 생성됩니다.

### 3) 어시스턴트가 항상 그래프를 먼저 보게 만들기

`graphify claude install` 명령을 실행하면 [[Claude_Code]]의 `CLAUDE.md`와 `PreToolUse hook`을 설치하여, AI가 raw 파일을 직접 읽기 전에 `GRAPH_REPORT.md`를 먼저 읽도록 유도합니다. 이는 AI가 구조 기반으로 프로젝트에 접근하게 만드는 핵심 설정입니다.

## 한계 및 주의할 점

Graphify는 강력한 도구이지만, 다음과 같은 한계와 주의할 점이 있습니다.

*   **추론의 한계**: `INFERRED`와 `AMBIGUOUS`로 표시된 관계는 AI의 추론이므로, 사람의 검토가 필요합니다. 특히 설계 의도나 의미 관계는 과신하지 않아야 합니다.
*   **플랫폼 종속성**: 단독 라이브러리로도 사용 가능하지만, 특정 AI 코딩 어시스턴트 환경과의 결합을 중심으로 설계되어 있습니다.
*   **문서와 실제 구현의 차이**: README에는 19개 언어 지원이라고 적혀 있지만, `pyproject.toml`에는 `tree-sitter-julia`도 보이는 등 문서와 실제 패키지 메타데이터 간 시점 차이가 있을 수 있습니다. 최신 버전의 문서를 확인하는 것이 안전합니다.
*   **클러스터링 품질**: 임베딩 없이 그래프 토폴로지 기반으로 군집화하므로, 초기에 추출된 노드와 엣지 품질이 결과에 큰 영향을 미칩니다.
*   **선택적 의존성**: `mcp`, `neo4j`, `pdf`, `watch`, `leiden`, `office` 등 일부 기능은 선택적 의존성(optional dependency)으로 분류되어 있어, 기능에 따라 별도 설치가 필요할 수 있습니다.

## 출처
- raw/Graphify 카파시의 위키 구현 버전.md
- (수집일: 2026-05-07)

## 관련
- [[Graphify]] — 카파시가 제안한 자동 그래프 도구의 상세 구현 및 활용 가이드
- [[LLM_Wiki_개념]] — 지식 그래프 기반 위키 시스템의 일반적인 개념
- [[Claude_Code]] — Graphify와 연동 가능한 AI 코딩 어시스턴트
- [[바이브_코딩_도구]] — Graphify가 활용될 수 있는 다양한 AI 코딩 도구들
- [[지식의_복리]] — 구조화된 지식 축적이 가져오는 효율성 증대 효과