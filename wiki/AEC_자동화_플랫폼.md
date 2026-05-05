# AEC 자동화 플랫폼 — 2026

[[해외_AEC_사내개발|Foster + Partners]] / [[정림건축_혁신본부|정림]] 처럼 사내 개발 여력이 안 되는 건축사무소를 위한 외부 플랫폼들. **Speckle / Hypar / Augmenta** 가 신생 3강. 2026년 NXT BLD 컨퍼런스에서 발표된 메가 트렌드 5가지가 향후 방향 제시.

## 신생 3강

### Speckle (오픈소스 데이터 허브)
- "**AEC data hub**" — 여러 BIM/CAD 도구 간 데이터 연결
- **오픈 데이터 인프라** — 정보 접근 가능, 실행 가능, AI-ready
- BIM 모델 변경 → QA 체크 + 코드 컴플라이언스 자동 트리거
- 시리즈 A 투자 유치 완료, 오픈소스
- **벤더 락인 회피** 가치
- → [[통합형_vs_연동형]] 의 AEC 도메인 **연동형 표상**. Autodesk Construction Cloud(통합형) 와 정확히 양극.

### Hypar (텍스트 → BIM)
- 클라우드 웹 플랫폼 + API
- Python/C# 코드 실행 → 디자인 로직 기반 **수백~수천 옵션 즉시 생성**
- "design logic 공유" 가능 → 누구나 만들고 공유
- → [[해외_AEC_사내개발|Foster Hydra]] 가 사내 도구로 만든 능력을 **외부 플랫폼**으로 제공하는 시도

### Augmenta (MEP 자동화)
- MEP(기계·전기·배관) 3D 응답을 **건물 설계로부터 야간 자동 생성**
- 일주일짜리 작업이 하룻밤
- 머신러닝 기반

## NXT BLD 2026 — 5가지 메가 트렌드

| 트렌드 | 의미 |
|--------|------|
| **Agentic BIM** | 지능형 에이전트가 워크플로우 주도, 인간 개입 최소화. BIM 도구가 정적 문서 → 실시간 지능 환경 |
| **Design Automation** | 매개변수 스크립팅 넘어 "전체 건물 시스템 수시간 내 생성·평가·최적화" |
| **Data-First** | "모델의 가치는 기하학이 아닌 구조화된 데이터" — [[정보_일원화_패턴]] AEC 적용 |
| **Applications on Demand** | 거대 통합 플랫폼 → 개방형 데이터 + 경량 작업별 도구 조합 ([[통합형_vs_연동형]] 의 연동형 흐름) |
| **AI 기반 엔지니어링** | 실시간 분석, 통합 건물 솔루션, 자동 시스템 설계 |

## 분야별 주목 플랫폼

| 분야 | 솔루션 |
|------|--------|
| **BIM 2.0** | Motif, Arcol, Autodesk Forma, **Hypar**, Snaptrude, Qonic |
| **MEP/구조** | **Augmenta**, StructureCraft(Branch3D), Hvakr, Endra |
| **자동 도면** | Graebert CAD, Swapp, Chaos, Ideatura |
| **데이터 인프라** | **Speckle**, That Open Company |
| **현실 캡처** | LiDAR, 포토그래메트리, Gaussian splatting |

## AEC 산업 미디어·커뮤니티

| 매체 | 특징 | 가치 |
|------|------|------|
| **aec+tech** (<aecplustech.com>) | 578개 도구 DB + 사례연구 + 비교 + 뉴스 | **건축 IT 담당자 1순위 구독** |
| **AEC Magazine** (<aecmag.com>) | 영국 매체, 심층 기사, NXT BLD/BIM Show Live 컨퍼런스 주최 | 트렌드·플랫폼 깊이 |
| **Foundamental** (<foundamental.com>) | AEC 스타트업 VC | 자금·시장 분석 |

## 건축설계사무소 IT 담당자 도입 가이드

### 외부 플랫폼이 적합한 영역
| 영역 | 외부 플랫폼 | 사내 개발보다 우위 |
|------|-----------|------------------|
| BIM 도구 간 데이터 연결 | **Speckle** | 표준 개방, 락인 회피 |
| 옵션 빠른 생성 | **Hypar** | 클라우드 컴퓨팅 즉시 활용 |
| MEP 자동 생성 | **Augmenta** | 머신러닝 모델 재사용 |
| 자동 도면 | Graebert / Swapp / Ideatura | 학습된 도면 변환 모델 |

### 사내 개발이 적합한 영역 (Foster 분기 기준)
- 사무소 고유 워크플로 ([[해외_AEC_사내개발]] Foster 의 4가지 분기 기준 적용)
- 사내 ERP/PMS 와의 깊은 연동 (외부 플랫폼은 아직 한국 ERP 모름)
- 한국 법규·인허가 (외부 플랫폼은 글로벌 코드 위주)

### Speckle PoC 권장 (1순위)
이유: 오픈소스 + 데이터 표준 + 즉시 도입 가능. 도입 후 사내 BIM/CAD 도구 간 데이터 흐름이 즉시 개선되며, 향후 사내 개발 시에도 Speckle 위에서 시작 가능 → [[건축IT_사내개발_전략]] 의 1순위 추천.

## 본 위키 컨텍스트와의 연결

### Speckle ↔ [[통합형_vs_연동형]] AEC 도메인
Speckle 의 "open data infrastructure, AI-ready" 는 [[통합형_vs_연동형]] 의 AEC 도메인 **연동형 표상**.
- 통합형: Autodesk Construction Cloud (Forma + Revit + Dynamo + Construction Cloud 묶음)
- 연동형: Speckle + Rhino + Revit + Hypar + Augmenta + 사내 도구

→ ERP 의 [[더존_아마란스10|아마란스10 통합형]] vs [[영림원_K-System_Ace|영림원 연동형]] 과 정확히 같은 분기.

### Hypar ↔ [[지식의_복리]] AEC 도메인
Hypar 의 "design logic 공유" 는 코드화된 설계 노하우의 누적 = [[지식의_복리]] 의 컴퓨테이셔널 디자인 도메인. 매 프로젝트마다 디자인 옵션 수동 생성하는 RAG 모델 → 한 번 짠 design logic 위에서 누적되는 위키 모델로 전환.

### Data-First 트렌드 ↔ [[정보_일원화_패턴]]
NXT BLD 의 "모델의 가치는 기하학이 아닌 구조화된 데이터" 는 본 위키 [[정보_일원화_패턴]] 의 AEC 산업 차원 표명. ERP 의 손익 일원화, 위키의 단일 정보원과 같은 메타 명제.

### Applications on Demand ↔ [[Claude_Code]] + MCP
"거대 통합 플랫폼 → 개방형 데이터 + 경량 작업별 도구 조합" — 이게 정확히 본 위키의 [[Claude_Code]] + MCP 구조. AEC 산업이 가는 방향과 본 위키 구축 방향이 같음.

## 출처
- raw/2026-05-05_aec_global_inhouse_dev.md
- NXT BLD 2026 — AEC Magazine: <https://aecmag.com/nxt-bld/nxt-bld-2026-a-decade-of-looking-around-corners>
- Hypar: <https://aecmag.com/ai/hypar-text-to-bim-and-beyond/>
- Speckle: <https://speckle.systems/>
- Speckle 분석 — AEC Magazine: <https://aecmag.com/data-management/speckle-the-open-source-cloud-data-platform>
- aec+tech: <https://www.aecplustech.com/>
- 수집일: 2026-05-05

## 관련
- [[해외_AEC_사내개발]] — Foster/Gensler 가 사내 개발한 영역을 외부 플랫폼이 채우는 관계
- [[정림건축_혁신본부]] — 한국 사례, 외부 플랫폼 도입 결정 시 참고
- [[건축IT_사내개발_전략]] — 외부 플랫폼 vs 사내 개발 분기
- [[통합형_vs_연동형]] — Speckle 이 AEC 도메인 연동형 표상
- [[정보_일원화_패턴]] — Data-First 트렌드의 본질
- [[지식의_복리]] — Hypar 의 design logic 공유 본질
- [[Claude_Code]] — Applications on Demand 와 같은 구조
