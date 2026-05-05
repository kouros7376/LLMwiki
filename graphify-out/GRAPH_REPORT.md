# Graph Report - ./wiki  (2026-05-05)

## Corpus Check
- Corpus is ~13,823 words - fits in a single context window. You may not need a graph.

## Summary
- 34 nodes · 179 edges · 6 communities
- Extraction: 91% EXTRACTED · 9% INFERRED · 0% AMBIGUOUS · INFERRED: 17 edges (avg confidence: 0.85)
- Token cost: 98,375 input · 24,594 output

## Community Hubs (Navigation)
- [[_COMMUNITY_ERP·PMS·정보 일원화|ERP·PMS·정보 일원화]]
- [[_COMMUNITY_통합형↔연동형 도구 스펙트럼|통합형↔연동형 도구 스펙트럼]]
- [[_COMMUNITY_위키 운영·사내 자동화 전략|위키 운영·사내 자동화 전략]]
- [[_COMMUNITY_해외 AEC·AI 도구 카탈로그|해외 AEC·AI 도구 카탈로그]]
- [[_COMMUNITY_Karpathy LLM Wiki·복리 명제|Karpathy LLM Wiki·복리 명제]]
- [[_COMMUNITY_정림건축 혁신본부 사례|정림건축 혁신본부 사례]]

## God Nodes (most connected - your core abstractions)
1. `정보 일원화 패턴` - 22 edges
2. `통합형 vs 연동형` - 22 edges
3. `kouwiki — 전체 목차` - 21 edges
4. `작업 이력` - 21 edges
5. `지식의 복리` - 19 edges
6. `Claude Code` - 18 edges
7. `건축IT 사내개발 전략 — 300명 규모 사무소` - 16 edges
8. `더존 아마란스10 (Amaranth 10)` - 16 edges
9. `AEC 자동화 플랫폼 — 2026` - 15 edges
10. `건설/건축 PMS (PMIS)` - 15 edges

## Surprising Connections (you probably didn't know these)
- `정림 BIM 규모검토 Package` --semantically_similar_to--> `건설/건축 PMS (PMIS)`  [INFERRED] [semantically similar]
  wiki/정림건축_혁신본부.md → wiki/건설_PMS.md
- `Foster + Partners Hydra` --semantically_similar_to--> `지식의 복리`  [INFERRED] [semantically similar]
  wiki/해외_AEC_사내개발.md → wiki/지식의_복리.md
- `JAdU (정림 아카데미 디지털 유닛)` --semantically_similar_to--> `지식의 복리`  [INFERRED] [semantically similar]
  wiki/정림건축_혁신본부.md → wiki/지식의_복리.md
- `Hypar (텍스트→BIM 클라우드 플랫폼)` --semantically_similar_to--> `지식의 복리`  [INFERRED] [semantically similar]
  wiki/AEC_자동화_플랫폼.md → wiki/지식의_복리.md
- `NXT BLD 2026 컨퍼런스 — 5가지 메가 트렌드` --semantically_similar_to--> `Claude Code`  [INFERRED] [semantically similar]
  wiki/AEC_자동화_플랫폼.md → wiki/Claude_Code.md

## Hyperedges (group relationships)
- **메타/cross-cutting 패턴 묶음** — 정보_일원화_패턴, 통합형_vs_연동형, 지식의_복리, 건강검진 [EXTRACTED 1.00]
- **통합형 vs 연동형 1차 분기 동형 — ERP/AEC** — 더존_아마란스10, 영림원_K-System_Ace, ERP_벤더_비교, AEC_자동화_플랫폼, 건축IT_사내개발_전략 [INFERRED 0.85]
- **Karpathy 직계 도구 체인** — LLM_Wiki_개념, Claude_Code, Graphify, CLAUDE [EXTRACTED 1.00]

## Communities (6 total, 0 thin omitted)

### Community 0 - "ERP·PMS·정보 일원화"
Cohesion: 0.94
Nodes (9): ERP / PMS RFP 체크리스트, ERP 벤더 비교 — 더존 vs 영림원, kouwiki — 전체 목차, 작업 이력, 건설/건축 PMS (PMIS), 더존 아마란스10 (Amaranth 10), 영림원 K-System Ace, 워크플로우 자동화 도구 (+1 more)

### Community 1 - "통합형↔연동형 도구 스펙트럼"
Cohesion: 0.25
Nodes (9): AEC 자동화 플랫폼 — 2026, Augmenta (MEP 자동화), Hypar (텍스트→BIM 클라우드 플랫폼), MCP (Model Context Protocol), NXT BLD 2026 컨퍼런스 — 5가지 메가 트렌드, ONE AI (더존 통합 생성형 AI), Speckle (오픈소스 AEC 데이터 허브), n8n (오픈소스 워크플로우) (+1 more)

### Community 2 - "위키 운영·사내 자동화 전략"
Cohesion: 0.9
Nodes (5): CLAUDE 운영규칙, Claude Code, 건강검진 (Lint), 건축IT 사내개발 전략 — 300명 규모 사무소, 자동화 시나리오 — 건축설계사무소 IT 담당자용 5개 + 로드맵

### Community 3 - "해외 AEC·AI 도구 카탈로그"
Cohesion: 0.6
Nodes (5): Foster + Partners Hermes, Foster + Partners Hydra, Graphify, 바이브 코딩 도구 — 2026, 해외 AEC 사내개발 — Foster + Partners, Gensler

### Community 4 - "Karpathy LLM Wiki·복리 명제"
Cohesion: 1.0
Nodes (3): Andrej Karpathy LLM Wiki Gist, LLM Wiki 개념, 지식의 복리

### Community 5 - "정림건축 혁신본부 사례"
Cohesion: 0.67
Nodes (3): 정림 BIM 규모검토 Package, JAdU (정림 아카데미 디지털 유닛), 정림건축 혁신본부 + JAdU

## Knowledge Gaps
- **1 isolated node(s):** `Augmenta (MEP 자동화)`
  These have ≤1 connection - possible missing edges or undocumented components.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **Why does `통합형 vs 연동형` connect `통합형↔연동형 도구 스펙트럼` to `ERP·PMS·정보 일원화`, `위키 운영·사내 자동화 전략`, `해외 AEC·AI 도구 카탈로그`, `Karpathy LLM Wiki·복리 명제`, `정림건축 혁신본부 사례`?**
  _High betweenness centrality (0.135) - this node is a cross-community bridge._
- **Why does `지식의 복리` connect `Karpathy LLM Wiki·복리 명제` to `ERP·PMS·정보 일원화`, `통합형↔연동형 도구 스펙트럼`, `위키 운영·사내 자동화 전략`, `해외 AEC·AI 도구 카탈로그`, `정림건축 혁신본부 사례`?**
  _High betweenness centrality (0.125) - this node is a cross-community bridge._
- **Why does `AEC 자동화 플랫폼 — 2026` connect `통합형↔연동형 도구 스펙트럼` to `ERP·PMS·정보 일원화`, `위키 운영·사내 자동화 전략`, `해외 AEC·AI 도구 카탈로그`, `Karpathy LLM Wiki·복리 명제`, `정림건축 혁신본부 사례`?**
  _High betweenness centrality (0.115) - this node is a cross-community bridge._
- **Are the 3 inferred relationships involving `정보 일원화 패턴` (e.g. with `건설/건축 PMS (PMIS)` and `Foster + Partners Hermes`) actually correct?**
  _`정보 일원화 패턴` has 3 INFERRED edges - model-reasoned connections that need verification._
- **Are the 5 inferred relationships involving `통합형 vs 연동형` (e.g. with `더존 아마란스10 (Amaranth 10)` and `영림원 K-System Ace`) actually correct?**
  _`통합형 vs 연동형` has 5 INFERRED edges - model-reasoned connections that need verification._
- **Are the 3 inferred relationships involving `지식의 복리` (e.g. with `Foster + Partners Hydra` and `JAdU (정림 아카데미 디지털 유닛)`) actually correct?**
  _`지식의 복리` has 3 INFERRED edges - model-reasoned connections that need verification._
- **What connects `Augmenta (MEP 자동화)` to the rest of the system?**
  _1 weakly-connected nodes found - possible documentation gaps or missing edges._