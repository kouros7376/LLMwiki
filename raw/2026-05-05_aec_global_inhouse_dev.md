# 해외 건축사무소 사내 개발 + AEC 자동화 플랫폼 — 2026 레퍼런스

수집일: 2026-05-05
출처:
- <https://www.fosterandpartners.com/insights/plus-journal/design-technology-and-the-team-between>
- <https://www.gensler.com/blog/the-most-timeless-differentiator-is-technology>
- <https://www.gensler.com/blog/graph-by-gensler-spatial-analytics-design>
- <https://aecmag.com/nxt-bld/nxt-bld-2026-a-decade-of-looking-around-corners>
- <https://aecmag.com/ai/hypar-text-to-bim-and-beyond/>
- <https://speckle.systems/>
- <https://www.aecplustech.com/>
- <https://aecmag.com/data-management/speckle-the-open-source-cloud-data-platform>

## 한 줄 요약
세계 1티어 건축사(Foster, Gensler)는 **사내 R&D 팀이 자체 도구를 적극 개발**. AEC 시장은 2026년 **agentic AI + 클라우드 BIM + 데이터 개방** 으로 재편 중. Speckle/Hypar/Augmenta 가 대표 신생 플랫폼.

---

## 1. Foster + Partners — Applied R+D 팀

### 조직 구성
- **다학제 통합팀**: 건축가 + 엔지니어 + 프로그래머
- 전문분야: 컴퓨터과학, 조경, 구조공학, 응용수학, 미술
- 과거 단일 직급(Design Systems Analyst) → 다양화

### 6개 전문 영역 + 사내 개발 도구

| 영역 | 사내 도구 | 기능 |
|------|----------|------|
| **성능주도 설계** | **Hydra** | 영화산업 기술 활용, 도시 규모 모델 수시간 내 수천 옵션 생성. 2019년 이후 50개 프로젝트 250,000+ 옵션, 130만+ 시뮬레이션 |
| **조망/일조 분석** | **Cyclops** | GPU 레이트레이싱 |
| **협업/상호운용** | **Hermes** | 클라우드 기반 실시간 데이터 교환, 설계 반복본/이전 버전 빠른 접근 |
| **XR 협업** | **Glaucon** | VR/AR 협업 설계 검토, 원격 이해관계자 참여 |
| **디지털 트윈** | **Campus Twin** | 런던 본부 디지털 트윈, IoT 센싱(온습도, CO₂) 통합, 운영 데이터 → 설계 피드백 |
| **AI** | (미명명) | 자연어 기반 사내 검색 엔진, Stable Diffusion → Rhino 통합 |

### 기술 스택
- **클라우드 인프라**: Hermes 기반
- **GPU 컴퓨팅**: 렌더링·물리 시뮬레이션
- **분산 컴퓨팅**: 다목적 최적화 병렬 처리
- **머신러닝**: 추론 API 배포
- **API 기반 아키텍처**: 도구 간 데이터 흐름

### 사내 개발 vs 외부 도구 분기 기준
> "The group's strong focus on developing specialist products for design teams makes it difficult to rely on off-the-shelf software" — Dominik Holzer

1. **최종 사용자(설계팀) 중심 맞춤**
2. **상호운용성 필요** (여러 도구 간 데이터 교환)
3. **클라우드 인프라 통합**
4. **장기 피드백 순환** (설계팀과 지속 협업)

→ 외부 도구는 **Rhino, 상용 BIM** 만 활용. 나머지는 사내 개발.

### 운영 철학
- **알파/베타 테스팅 문화** — 설계팀이 즉시 피드백
- **AI 실용성 중심** — 이미지 생성 같은 표면적 AI 회피, **실제 업무 효율성·운영 최적화** 초점
- "Teams don't react well when you give them a hundred good options. First you need to have an honest conversation about what it is they consider good." — Marcin Kosicki

### 다른 건축사가 참고할 6가지
1. 통합 학제적 팀 (건축가 + 엔지니어 + 프로그래머 깊은 협업)
2. 장기 R&D 투자 (프로젝트 예산 밖 전략 기금)
3. **단편 도구 도입보다 상호연결된 도구 에코시스템**
4. 사용자 경험 중심 개발 (설계팀 지속 피드백)
5. 기술 민주화 (특정 전문가 → 전 설계팀)
6. AI 실용성 중심 (운영 최적화 우선)

---

## 2. Gensler — NFORM Ecosystem

### Design Technology 팀
- 글로벌 협업 그룹: 건축가 + 기술자
- "도구 + 워크플로 + 방법론" 묶음을 NFORM Ecosystem 으로 패키징

### 사내 도구
| 도구 | 기능 |
|------|------|
| **gFloorz** | 클라이언트 실시간 참여 + 시뮬레이션 데이터 환경에서 테스트 핏 |
| **gBlox (Blox)** | 디자인 + 분석 도구 |
| **Graph** | Spatial Analytic Model 기반, 다중 데이터 통합·연산·시각화 |

### 차별점
- **데이터 기반 디자인 의사결정** 을 시각적으로 클라이언트와 공유
- 기술 자체가 차별화 요소 — "The Most Timeless Design Differentiator Is Technology"

---

## 3. HOK
- 검색 결과상 사내 컴퓨테이셔널 디자인 정보 명시 적음
- 일반적으로 **HOK BIMSolutions, HOK Sustainable Solutions** 등 전문 그룹 운영
- → 별도 인터뷰·발표자료 추가 수집 필요

---

## 4. AEC 자동화 플랫폼 (NXT BLD 2026 발표)

### 5가지 메가 트렌드
1. **Agentic BIM** — 지능형 에이전트가 워크플로우 주도, 인간 개입 최소화. BIM 도구가 정적 문서 → 실시간 지능 환경
2. **Design Automation** — 매개변수 스크립팅 넘어 "전체 건물 시스템 수시간 내 생성·평가·최적화"
3. **Data-First** — "모델의 가치는 기하학이 아닌 구조화된 데이터"
4. **Applications on Demand** — 거대 통합 플랫폼 → 개방형 데이터 + 경량 작업별 도구 조합
5. **AI 기반 엔지니어링** — 실시간 분석, 통합 건물 솔루션, 자동 시스템 설계

### 주목 플랫폼·도구

| 분야 | 솔루션 |
|------|--------|
| **BIM 2.0** | Motif, Arcol, Autodesk Forma, **Hypar**, Snaptrude, Qonic |
| **MEP/구조** | **Augmenta**, StructureCraft(Branch3D), Hvakr, Endra |
| **자동 도면** | Graebert CAD, Swapp, Chaos, Ideatura |
| **데이터 인프라** | **Speckle**, That Open Company |
| **현실 캡처** | LiDAR, 포토그래메트리, Gaussian splatting |

---

## 5. 핵심 신생 플랫폼 3강

### Speckle (오픈소스)
- "AEC data hub" — 여러 BIM/CAD 도구 간 데이터 연결
- **오픈 데이터 인프라** — 정보 접근 가능, 실행 가능, AI-ready
- BIM 모델 변경 → QA 체크 + 코드 컴플라이언스 자동 트리거
- 시리즈 A 투자 유치 완료, 오픈소스
- **벤더 락인 회피** 가치 제공
- → [[통합형_vs_연동형]] 의 AEC 도메인 연동형 표상

### Hypar (텍스트 → BIM)
- 클라우드 기반, 웹 플랫폼 + API
- Python/C# 코드 실행 → 디자인 로직 기반 수백~수천 옵션 즉시 생성
- "design logic 공유" 가능 → 누구나 만들고 공유

### Augmenta (MEP 자동화)
- MEP(기계·전기·배관) 3D 응답을 **건물 설계로부터 야간 자동 생성**
- → 일주일짜리 작업이 하룻밤
- 머신러닝 기반

---

## 6. AEC 산업 미디어·커뮤니티

| 매체 | 특징 | 가치 |
|------|------|------|
| **aec+tech** (<aecplustech.com>) | 578개 도구 DB + 사례연구 + 비교 + 뉴스 | **건축 IT 담당자 1순위 구독 매체** |
| **AEC Magazine** (<aecmag.com>) | 영국 기반 매체, 심층 기사 | NXT BLD/BIM Show Live 컨퍼런스 주최 |
| **Foundamental** (<foundamental.com>) | AEC 스타트업 VC, 시장 분석 | 자금 흐름 추적 |

---

## 7. 건축설계사무소 IT 담당자 시사점

### 사내 개발 vs 외부 도구 — Foster 모델 적용
중견~중소 건축사무소(300명 규모)는 Foster 처럼 풀스케일 R&D 팀 운영 어려움. 대신:
- **소규모 디지털 디자인 그룹 1~3명** + **외부 플랫폼(Speckle/Hypar) 활용**
- 사내 개발은 "**Foster 의 분기 기준**" 그대로 적용 가능:
  1. 설계팀 맞춤 필요
  2. 여러 도구 데이터 교환 필요
  3. 사내 ERP/PMS 통합 필요

### 본 위키 컨텍스트와의 연결
- [[통합형_vs_연동형]] — Speckle 의 오픈 데이터 인프라가 **AEC 의 연동형 표상**, Autodesk Construction Cloud 가 **통합형 표상**
- [[정보_일원화_패턴]] — Foster 의 Hermes 가 "단일 정보원" 구현, 사내 도구 간 데이터 일원화
- [[지식의_복리]] — Foster 의 Hydra 가 **130만 시뮬레이션을 자산화** — 매 프로젝트마다 새로 돌리지 않고 위에서 시작
- Foster 의 "AI 실용성 중심, 이미지 생성 같은 표면적 AI 회피" 원칙은 [[ERP_RFP_체크리스트]] 의 데모 검증 원칙과 같은 맥락 (마케팅 멋진 거 ≠ 실무 가치)

### 추천 다음 행동
1. aec+tech 사이트 정기 구독 (월 1회 신규 도구 동향 체크)
2. Speckle PoC — 사내 BIM 도구들 간 데이터 허브로 검증
3. Hypar 데모 — 텍스트→BIM 으로 빠른 옵션 생성 가능성 검증
4. 정림건축 JAdU 교육 견학 (한국 사례) — [[2026-05-05_aec_korea_inhouse_dev|국내 레퍼런스]]
