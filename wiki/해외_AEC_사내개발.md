# 해외 AEC 사내개발 — Foster + Partners, Gensler

세계 1티어 건축사무소들의 사내 R&D 팀 구조와 자체 개발 도구. 특히 **Foster + Partners Applied R+D** 가 가장 풍부한 정보 공개.

## Foster + Partners — Applied R+D 팀

### 조직 구성
- **다학제 통합팀**: 건축가 + 엔지니어 + 프로그래머
- 전문분야: 컴퓨터과학, 조경, 구조공학, 응용수학, 미술
- 과거 단일 직급(Design Systems Analyst) → 다양화

> "There is a lot of cross-pollination in the team" — Khaled Elashry (Partner)

### 6개 전문 영역 + 사내 도구

| 영역 | 사내 도구 | 기능 |
|------|----------|------|
| **성능주도 설계** | **Hydra** | 영화산업 기술 차용. 도시 규모 모델 수시간 내 수천 옵션 생성. 2019년 이후 50개 프로젝트, 250,000+ 옵션, **130만+ 시뮬레이션** |
| **조망/일조 분석** | **Cyclops** | GPU 레이트레이싱 |
| **협업/상호운용** | **Hermes** | 클라우드 실시간 데이터 교환, 설계 반복본/이전 버전 빠른 접근 |
| **XR 협업** | **Glaucon** | VR/AR 협업 설계 검토, 원격 이해관계자 참여 |
| **디지털 트윈** | **Campus Twin** | 런던 본부 기능형 디지털 트윈, IoT 센싱(온습도, CO₂), 운영 데이터 → 설계 피드백 |
| **AI** | (미명명) | 자연어 사내 검색, Stable Diffusion → Rhino 통합 |

### 기술 스택
- **클라우드 인프라** (Hermes 기반)
- **GPU 컴퓨팅** (렌더링, 시뮬레이션)
- **분산 컴퓨팅** (다목적 최적화 병렬)
- **머신러닝** (추론 API 배포)
- **API 기반 아키텍처** (도구 간 데이터 흐름)

### 사내 개발 vs 외부 도구 분기 기준 ★

> "The group's strong focus on developing specialist products for design teams makes it difficult to rely on off-the-shelf software" — Dominik Holzer

**4가지 분기 기준**:
1. **최종 사용자(설계팀) 중심 맞춤** 필요
2. **상호운용성** 필요 (여러 도구 간 데이터 교환)
3. **클라우드 인프라 통합** 필요
4. **장기 피드백 순환** (설계팀과 지속 협업)

→ 외부 도구는 **Rhino + 상용 BIM** 만 활용. 나머지는 사내 개발.

이 분기 기준은 [[바이브_코딩_도구]] 시장에서 도구 선택 시에도, [[ERP_벤더_비교]] 의 통합형/연동형 분기에서도 같은 형태로 적용됨 → [[건축IT_사내개발_전략]].

### 운영 철학

**알파/베타 테스팅 문화**
- 설계팀이 즉시 피드백
- 양방향 대화로 도구 개선

**AI 실용성 중심** (이미지 생성 같은 표면적 AI 회피)
> "Teams don't react well when you give them a hundred good options. First you need to have an honest conversation about what it is they consider good." — Marcin Kosicki

→ 이 원칙은 [[ERP_RFP_체크리스트]] 의 "데모 시 본인 데이터로 시연 요청" 과 같은 맥락. **마케팅 멋진 것 ≠ 실무 가치**.

## Gensler — NFORM Ecosystem

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
- 기술 자체가 차별화 — "The Most Timeless Design Differentiator Is Technology"

## HOK
- 검색 결과상 사내 컴퓨테이셔널 디자인 정보 제한적
- HOK BIMSolutions, HOK Sustainable Solutions 등 전문 그룹 운영
- **별도 인터뷰·발표자료 추가 수집 필요** (정보 갭)

## 다른 건축사가 참고할 6가지 (Foster 종합)

1. **통합 학제적 팀** — 건축가 + 엔지니어 + 프로그래머 깊은 협업
2. **장기 R&D 투자** — 프로젝트 예산 밖 전략 기금
3. **단편 도구 도입보다 상호연결된 도구 에코시스템** ([[정보_일원화_패턴]] 의 사내 R&D 적용)
4. **사용자 경험 중심** — 설계팀 지속 피드백
5. **기술 민주화** — 특정 전문가 → 전 설계팀
6. **AI 실용성 중심** — 운영 최적화 우선, 표면적 AI 회피

## 의외의 연결 — 본 위키 메타 패턴

### Foster Hermes ↔ [[정보_일원화_패턴]] AEC 도메인
Hermes 의 "클라우드 단일 데이터원, 모든 도구가 같은 데이터에서 출발" 은 본 위키 [[정보_일원화_패턴]] 의 AEC 도메인 구현. ERP 의 [[건설_PMS|손익 일원화]] 와 본질이 같음.

### Foster Hydra ↔ [[지식의_복리]] 컴퓨테이셔널 디자인 적용
Hydra 가 **130만 시뮬레이션을 자산화** — 매 프로젝트마다 새로 돌리지 않고 이전 시뮬레이션 위에서 시작. 이게 정확히 [[지식의_복리]] 의 시뮬레이션 도메인 구현. RAG vs 위키 구도가 시뮬레이션에도 그대로.

### Foster 의 "AI 실용성, 이미지 생성 회피" ↔ [[ERP_RFP_체크리스트]] 데모 검증 원칙
두 원칙 모두 **"마케팅용 멋진 기능 ≠ 실무에서 가치 있는 기능"** 이라는 같은 본질. Foster 는 R&D 단에서, RFP 체크리스트는 도입 단에서 같은 원칙을 적용.

### 외부 도구는 Rhino + 상용 BIM 만 ↔ [[통합형_vs_연동형]] 의 하이브리드
Foster 는 순수 연동형이 아니라 **선별적 하이브리드** — 외부 도구는 두 가지로만 한정하고 그 외는 사내 개발. 이는 [[통합형_vs_연동형]] 마지막 섹션의 하이브리드 가능성을 정확히 구현.

## 출처
- raw/2026-05-05_aec_global_inhouse_dev.md
- Foster + Partners: <https://www.fosterandpartners.com/insights/plus-journal/design-technology-and-the-team-between>
- Gensler 기술 차별화: <https://www.gensler.com/blog/the-most-timeless-differentiator-is-technology>
- Gensler Graph: <https://www.gensler.com/blog/graph-by-gensler-spatial-analytics-design>
- 수집일: 2026-05-05

## 관련
- [[정림건축_혁신본부]] — 한국 1위 사례, Foster 모델의 한국형 적용
- [[AEC_자동화_플랫폼]] — Foster 가 사내 개발하는 영역을 외부 플랫폼이 채우려는 시도 (Speckle/Hypar 등)
- [[건축IT_사내개발_전략]] — Foster 모델을 300명 사무소에 적용
- [[정보_일원화_패턴]] — Hermes 가 구현하는 AEC 도메인
- [[지식의_복리]] — Hydra 가 구현하는 시뮬레이션 도메인
- [[통합형_vs_연동형]] — Foster 의 선별적 하이브리드
- [[바이브_코딩_도구]] — Foster 의 분기 기준이 도구 선택에도 동일 적용
