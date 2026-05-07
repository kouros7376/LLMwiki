# Graph Report - C:\Users\key\Desktop\kouwiki\kouwiki\wiki  (2026-05-07)

## Corpus Check
- 32 files · ~99,999 words
- Verdict: corpus is large enough that graph structure adds value.

## Summary
- 35 nodes · 233 edges · 10 communities (4 shown, 6 thin omitted)
- Extraction: 93% EXTRACTED · 7% INFERRED · 0% AMBIGUOUS · INFERRED: 16 edges (avg confidence: 0.84)
- Token cost: 0 input · 0 output

## Community Hubs (Navigation)
- [[_COMMUNITY_Community 0|Community 0]]
- [[_COMMUNITY_Community 1|Community 1]]
- [[_COMMUNITY_Community 2|Community 2]]
- [[_COMMUNITY_Community 3|Community 3]]
- [[_COMMUNITY_Community 4|Community 4]]
- [[_COMMUNITY_Community 5|Community 5]]
- [[_COMMUNITY_Community 6|Community 6]]
- [[_COMMUNITY_Community 7|Community 7]]
- [[_COMMUNITY_Community 8|Community 8]]
- [[_COMMUNITY_Community 9|Community 9]]

## God Nodes (most connected - your core abstractions)
1. `kouwiki Index` - 27 edges
2. `작업 이력 로그` - 27 edges
3. `정보 일원화 패턴` - 25 edges
4. `건축IT 사내개발 전략` - 22 edges
5. `Graphify` - 21 edges
6. `통합형 vs 연동형` - 21 edges
7. `Claude Code` - 20 edges
8. `자동화 시나리오` - 20 edges
9. `지식의 복리` - 19 edges
10. `LLM Wiki 웹구현 사례` - 16 edges

## Surprising Connections (you probably didn't know these)
- `AEC 자동화 플랫폼` --semantically_similar_to--> `LLM Wiki 웹구현 사례`  [INFERRED] [semantically similar]
  wiki/AEC_자동화_플랫폼.md → wiki/LLM_Wiki_웹구현_사례.md
- `Graphify` --semantically_similar_to--> `건설 PMS`  [INFERRED] [semantically similar]
  wiki/Graphify.md → wiki/건설_PMS.md
- `Graphify` --semantically_similar_to--> `한국어 LLM 스택`  [INFERRED] [semantically similar]
  wiki/Graphify.md → wiki/한국어_LLM_스택.md
- `해외 AEC 사내개발` --semantically_similar_to--> `정보 일원화 패턴`  [INFERRED] [semantically similar]
  wiki/해외_AEC_사내개발.md → wiki/정보_일원화_패턴.md
- `해외 AEC 사내개발` --semantically_similar_to--> `지식의 복리`  [INFERRED] [semantically similar]
  wiki/해외_AEC_사내개발.md → wiki/지식의_복리.md

## Hyperedges (group relationships)
- **사내 자동화 도구 3-tier (RPA · 워크플로우 · LLM 에이전트)** — RPA, 워크플로우_자동화_도구, Wiki_Backed_Chatbot [INFERRED 0.95]
- **본 사무소 1단계 PoC 도구군** — RPA, 자동화_시나리오, Claude_Code, AEC_자동화_플랫폼, 워크플로우_자동화_도구 [INFERRED 0.85]

## Communities (10 total, 6 thin omitted)

### Community 0 - "Community 0"
Cohesion: 0.94
Nodes (9): CLAUDE 운영규칙, Claude Code, LLM Wiki 운영 패턴, 건강검진, 사내 챗봇 시스템, 자동화 시나리오, 정보 일원화 패턴, 지식의 복리 (+1 more)

### Community 1 - "Community 1"
Cohesion: 0.86
Nodes (8): ERP RFP 체크리스트, ERP 벤더 비교, kouwiki Index, 건설 PMS, 바이브 코딩 도구, 정림건축 혁신본부, 통합형 vs 연동형, 해외 AEC 사내개발

### Community 2 - "Community 2"
Cohesion: 0.9
Nodes (7): AEC 자동화 플랫폼, RPA — 300인 건축설계사무소 도입 타당성, 작업 이력 로그, 건축IT 사내개발 전략, 더존 아마란스10, 영림원 K-System Ace, 워크플로우 자동화 도구

### Community 3 - "Community 3"
Cohesion: 1.0
Nodes (5): Graphify, LLM Wiki 개념, LLM Wiki 웹구현 사례, Wiki-Backed Chatbot, 교차참조

## Knowledge Gaps
- **4 isolated node(s):** `사내_인쇄_복합기_가이드`, `사내_인쇄_복합기_가이드_20260507`, `워처_Race_가드_테스트`, `graphify_20260507`
  These have ≤1 connection - possible missing edges or undocumented components.
- **6 thin communities (<3 nodes) omitted from report** — run `graphify query` to explore isolated nodes.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **Why does `kouwiki Index` connect `Community 1` to `Community 0`, `Community 2`, `Community 3`?**
  _High betweenness centrality (0.043) - this node is a cross-community bridge._
- **Why does `작업 이력 로그` connect `Community 2` to `Community 0`, `Community 1`, `Community 3`?**
  _High betweenness centrality (0.043) - this node is a cross-community bridge._
- **Why does `정보 일원화 패턴` connect `Community 0` to `Community 1`, `Community 2`, `Community 3`?**
  _High betweenness centrality (0.028) - this node is a cross-community bridge._
- **Are the 2 inferred relationships involving `정보 일원화 패턴` (e.g. with `해외 AEC 사내개발` and `정림건축 혁신본부`) actually correct?**
  _`정보 일원화 패턴` has 2 INFERRED edges - model-reasoned connections that need verification._
- **Are the 3 inferred relationships involving `Graphify` (e.g. with `지식의 복리` and `건설 PMS`) actually correct?**
  _`Graphify` has 3 INFERRED edges - model-reasoned connections that need verification._
- **What connects `사내_인쇄_복합기_가이드`, `사내_인쇄_복합기_가이드_20260507`, `워처_Race_가드_테스트` to the rest of the system?**
  _4 weakly-connected nodes found - possible documentation gaps or missing edges._