# RPA — 300인 건축설계사무소 도입 타당성

Robotic Process Automation. GUI 화면 조작으로 단순 반복 업무를 자동화하는 소프트웨어 봇. 본 위키 결론: **전사 RPA 부적합. 정부 사이트(세움터·홈택스·4대보험 EDI) + 회계 OCR 핵심 1~2개 한정의 "선택적·보수적 도입"이 본 사무소 합리적 답.** 다우오피스 API + 별도 PMS/ERP + GPU 부재 + 운영 중인 [[Gemini_File_Search|Gemini 챗봇]] + 검토 중인 [[Wiki_Backed_Chatbot]] 의 5개 조건이 [[자동화_시나리오|n8n/Power Automate]]와 [[Wiki_Backed_Chatbot|LLM 에이전트]]로 RPA 적용 범위를 좁히는 근거.

## RPA 본질 — 결정론적 GUI 봇

기존 공장 로봇이 실체적 기계라면 RPA는 사람의 단순 반복 클릭·입력을 대신하는 소프트웨어 로봇. 핵심 적용 패턴 2가지: **(1) ERP에서 데이터 다운로드 → 엑셀 정리, (2) 엑셀/텍스트 데이터 → ERP 입력**. OCR 결합 시 PDF 처리도 가능하나 한국어 인식률 한계.

**도구 특성**: Diagram/Tree GUI 기반 → IT 비전공자가 1주~1달 교육으로 직접 개발 가능. 하지만 **GUI 의존**이 핵심 약점 → 대상 시스템 UI 변경 시 봇이 깨지고, 유지보수 부담이 [[자동화_시나리오|API 기반 워크플로우]]보다 압도적으로 큼.

**한국 도입 걸림돌 2가지** (나무위키):
1. 기술적 — SAP/Oracle 외 국내 ERP·자체 ERP는 RPA 연계 어려움 (메뉴 인식 한계로 일부 단계 수동 처리 잔존)
2. 문화적 — 글로벌 기업은 메일 기반 보고로 b→d 전체 자동화 가능하나, 한국은 대면 보고로 b만 자동화 → ROI 절반

## 진화 — RPA → IPA → CA → Agentic

| 단계 | 정의 | 본 사무소 위치 |
|------|------|---------------|
| **RPA** | 정해진 GUI 시퀀스 반복 (결정론적) | 정부 사이트만 한정 적용 |
| **IPA** | RPA + AI-OCR + ML 분류기 (비정형 입력) | AI-OCR은 SaaS 결합 (CLOVA/Upstage) |
| **CA** | NLU/지식 기반 의사결정 결합 | [[Wiki_Backed_Chatbot]] 이 일부 커버 |
| **APA / Agentic** | LLM 에이전트가 봇·도구를 자율 호출 | 24개월 후 통합 시점 |

**2025-2026 트렌드**: UiPath Maestro/Agent Builder/AI Trust Layer, Microsoft Copilot Studio + UiPath 양방향 통합, Automation Anywhere AI Agent Studio, SS&C Blue Prism의 "교체 아닌 공존" 백서. **Gartner 경고**: "Agent Washing" — 자율 추론·계획 능력 없는 RPA를 Agent로 재포장하는 사례 다수. 그리드원도 2026.04 "에이전틱 자동화 플랫폼 전략" 공개.

→ **Agentic 시대에도 RPA는 Executor 레이어로 잔존**. "사람이 봇 트리거" → "에이전트가 봇 도구 호출"로 바뀜. 봇 표준화·도구화가 미래 호환의 핵심.

## 건축·AEC 업계 도입 사례

### 글로벌
- **Skanska** — 가장 명확한 성공 사례. UiPath 기반 Shared Service Center 자동화. 지급 데이터 검증 1주 → 5분. 이후 [[해외_AEC_사내개발|Skanska Sidekick]] 챗봇 + Dusty Robotics 현장 자동 먹매김 로봇으로 단계적 진화 (백오피스 RPA → 사내 LLM 챗봇 → 현장 실물 자동화)
- **Arcadis** — "AI in AEC Code of Practice" 백서(2020). RPA를 BIM·AI와 함께 다루는 종합 전략
- **AECOM** — 변경 지시 검증·송장 처리 백오피스 자동화 (단독 사례 제한적)
- **Foster + Partners / Gensler** — Dynamo/Grasshopper 기반 BIM 자동화 집중. 클래식 RPA 사례 비공개. "디자인 자동화는 Dynamo·Python, 백오피스는 ERP·BIM 360 API 직결" 방향

### 한국
**국내 종합건축사사무소 단위 공개 RPA 사례 사실상 부재**. 시공사(현대·GS·대우)는 보도자료 단발성 언급. 체계적 사례집 없음. 시사점: **벤더 컨설팅이 도메인 무지로 들어올 위험 높음 → 사내 주도 PoC 필수**. 기업 일반에서는 LG화학·KB국민은행·LG CNS·삼성전기·하나은행 등이 수만~수십만 시간 절감 발표.

### 도면·인허가·BIM 결합
- 학술(ResearchGate, Springer 2025)에 따르면 RPA + BIM 결합 시나리오: Revit/Dynamo 자동 실행, 사양서 → BIM 자동 충진, clash detection 결과 정리, 데이터 입력 시간 90% 절감 보고
- 단 **이 영역은 클래식 RPA보다 BIM 도구 API/스크립팅(Revit API, Dynamo, Grasshopper, Forge/APS)이 훨씬 효율적**. RPA는 "BIM 결과물을 외부 시스템(ERP·관청·세금계산서)으로 옮기는 다리" 역할에 집중

## 본 사무소 적용 가능 업무 매트릭스

| 업무 | RPA 적합도 | 대안 | 비고 |
|------|----------|------|------|
| 세움터(건축행정시스템) 추적 | ★★★★★ | API 부재 → RPA 유일 | **킬러 시나리오 1순위** |
| 4대보험 EDI 신고 | ★★★★★ | API 부재 → RPA 유일 | 정부 사이트 전형 |
| 국세청 홈택스 신고·증빙 | ★★★★★ | API 일부 | 영구 가치 |
| 세금계산서 발행/수취 대사 | ★★★★★ | API 가능시 API | 메타넷 사례 |
| 건축 인허가 도서 일괄 업로드 | ★★★★ | 동일 | 첨부 표준화 관건 |
| 카드 매입전표 자동 분개 | ★★★★ | ERP API + OCR | IPA 영역 |
| 외주 설계비 청구서 처리 | ★★★★ | IDP + ERP API | 형식 편차 ↑ |
| 원가 계산·프로젝트 손익 | ★★★ | Pandas + ERP API | LLM 분석 결합 ↑ |
| **다우오피스 ↔ 급여** 근태 집계 | ★★ | **다우오피스 오픈 API** | RPA 불필요 |
| 출장 정산·증빙 분류 | ★★★ | OCR + LLM | IPA |
| PMS ↔ ERP 코드 동기화 | ★★★ | 양 시스템 API 여부 | API 우선 |
| DWG → PDF 일괄 변환 | ★★★ | **AutoCAD Script / Revit API** | 도구 내장 자동화 우선 |
| 도면 검수 (스탬프·서명) | ★★★ | Vision LLM + 스크립트 | 신기술 영역 |
| 사내 매뉴얼 검색 응대 | ★ | **이미 [[사내_챗봇_시스템]] 운영** | 중복 도입 X |

**요약**:
- ★★★★★ (RPA 강력 추천): **외부 정부 사이트 + 회계 핵심 = 5~7개 업무**
- ★★★ (조건부): API 양쪽 모두 부재 시만
- ★★ 이하 (부적합): 풍부한 API/스크립팅 이미 존재 영역

→ **본 사무소에서 RPA가 유일한 합리적 해법인 영역은 "외부 정부 시스템 + OCR 결합 회계" 두 개 정도**.

## RPA vs 워크플로우 자동화 vs API vs LLM 에이전트

| 차원 | RPA | [[워크플로우_자동화_도구\|n8n/Power Automate]] | API 직결 | LLM 에이전트 |
|------|-----|--------|---------|-------------|
| 방식 | GUI 조작 | API 노드 + 일부 GUI | HTTP/SDK | LLM이 도구 선택 |
| 변경 취약성 | 매우 높음 (UI) | 낮음 (API) | 낮음 | 중 (모델 회귀) |
| 유지비 | 높음 | 낮음~중 | 낮음 | 중 (LLM 호출) |
| 초기 비용 | 높음 (라이센스) | 낮음 (오픈소스) | 인건비만 | LLM + 인프라 |
| 적합 업무 | 정부 사이트, 레거시 GUI | SaaS 간 데이터 흐름 | 사내 시스템 통합 | 비정형 의사결정·문서 |
| 본 사무소 적합도 | 정부 사이트만 ★★★★ | SaaS·ERP 흐름 ★★★★★ | 다우오피스 API ★★★★ | 사내 챗봇·문서 ★★★★ |

**분기 원칙**:
- API 있고 데이터 흐름 핵심 → **n8n / Power Automate**
- GUI만 있고 사람 클릭 필수 → **RPA**
- 둘 다 가능하면 → **워크플로우 자동화 우선** (유지비·내구성 압도적)
- 비정형 판단 필요 → **LLM 에이전트 ([[Wiki_Backed_Chatbot]])**

→ 본 사무소 다우오피스 오픈 API 활용 시 **인사·총무·메일·결재 영역 RPA 사실상 불필요**.

## 벤더 비교 — 국내 7개 + 해외 3대

### 해외 3대

| 벤더 | 가격(2026) | 한국어 | 본 사무소 적합도 |
|------|-----------|--------|-----------------|
| **UiPath** | Pro Attended ~$135/봇·월, Unattended ~$420/봇·월 | 우수 (한국지사·교육) | 50봇 이상 확장 시 |
| **Automation Anywhere** | 1 unattended + 1 creator + control room ~$750/월 | 양호 | 회계 IDP 한정 |
| **Microsoft Power Automate** | $15-40/사용자·월 (M365 보유시 추가비 0) | 우수 (MS 한국) | **다우오피스+M365 혼용 시 비용 1위** |

### 국내 7대

| 제품 (벤더) | 강점 | 가격 | 본 사무소 적합도 |
|------------|------|------|-----------------|
| **AutomateOne (그리드원)** | 국내 최초, 30+ 고객(한국환경공단·우정사업본부·NH농협·IBK·신한카드·교보생명), 무료판 ezbot | 비공개 영업협의 | ★★★★ (국내 사례 1위) |
| **CheckMATE (Symation)** | 비주얼 로우코드, RDA 1인 1봇, AI Agent + AI OCR. LG 지분투자 | GoBizKorea/Amazon 월 구독 모델 | ★★★★ (PoC 부담 작음) |
| **Brity Automation (삼성SDS)** | 삼성 자체 11개월 82만시간 절감, 22,000+ 프로세스, 삼성전기 24만시간 | Cloud Orchestrator + Designer + Bot 월 과금 | ★★★ (대기업 표준, 중견 과대) |
| **Worktronics (이든티앤에스)** | 중소·중견 특화, 가격 유연, 괌전력청 수출 | 비공개 | ★★★ |
| **AUTOBE (링크제니시스)** | 시스템 검증 RPA 1위, 화면 녹화 → 자동 스크립트 | 비공개, 무료판 | ★★★ (검증 출신) |
| **BATEM (비에이템)** | 후발, 가성비 | 비공개 | ★★ |
| **인바이즈 (invaiz.com)** | 부산 스타트업, AI 셀프힐링 웹 드라이버, Selenium 한계 보완 | 저렴 (스타트업) | ★★★ (웹 RPA PoC) |

→ **본 사무소 현실적 후보 3개**:
1. **Microsoft Power Automate** — 다우오피스/M365 일부 환경에서 비용 압도적
2. **AutomateOne (그리드원)** — 국내 사례·정부 사이트 노하우
3. **CheckMATE (Symation)** — 1인 1봇 + AI OCR로 PoC 부담 낮음

UiPath는 50봇 이상 확장 시, Brity는 본 사무소 규모 대비 과대 투자.

## 도입 타당성 — ROI·실패 패턴

### ROI 추정 (300명 환산)
- 국내 평균 ROI **약 200%**, **12개월 내 회수** (디지털투데이·GDS Online·AImultiple)
- 표본: 초기 6,500만원 + 운영 1,800만원 → 절감 1.7억원, ROI 233.8%, 회수 4.6개월
- **본 사무소 환산**: 봇 5~10기, 라이센스 연 3,000-6,000만원(국내) 또는 500-1,500만원(Power Automate), 컨설팅·1차 개발 3,000-5,000만원, 운영비 연 1,000-2,000만원, 절감 회계 1명 + 인사·총무 0.5 + 인허가 보조 0.5 = **연 인건비 1.5억원 상당 회수**
- **보수적 ROI: 연 절감 1억원 / 연 비용 5,000만원 = 100% (12-18개월 회수)**

### 실패 패턴 (컨설팅 보고서)
- **Ernst & Young**: 초기 RPA 프로젝트 **실패율 30-50%**
- **Deloitte**: 도입 53%, 도입+계획 78%, 단 **확장 단계 진입 절반 이하**
- **McKinsey**: RPA 확장과 변화관리가 최대 장애
- **PwC**: 거버넌스 부재·내부통제 미비
- **BCG**: 디지털 전환 일반 70% 실패, 850개 기업 분석에서 목표 달성 35%
- **Gartner 2026**: "Agent Washing" — 자율성 없는 RPA를 Agent로 재포장 사례 다수

본 사무소 회피 패턴:
1. 전사 일괄 도입 (PoC 없이 확장)
2. 벤더 주도 컨설팅 (도메인 무지 → 첫 UI 변경에서 깨짐)
3. **봇 개수 KPI** — "절감 시간 + 오류율 감소"가 진짜 KPI
4. 거버넌스 부재 (계정·권한·로그·롤백 책임자 미정)

### 단계 로드맵

| 단계 | 기간 | 예산 | 핵심 과업 | 성공 지표 |
|------|------|------|----------|----------|
| 0. 사전 진단 | 1개월 | 0-500만원 | 자동화 후보 30개 식별 + 분기표 | 후보 리스트 |
| **1. PoC** | 2-3개월 | 500-1,500만원 | 4대보험 EDI 또는 홈택스 1건 | 1봇 가동, 절감 시간 측정 |
| 2. MVP | 3-6개월 | 3,000-5,000만원 | 5-7개 업무, 운영자 1명 교육 | 누적 절감 ≥ 1,000h/연 |
| 3. 확장 | 6-12개월 | 5,000-8,000만원 | 10-15개 업무, 거버넌스 정립 | ROI ≥ 100% |
| 4. 진화 | 12개월~ | 운영비 | RPA → IPA → Agentic, [[Wiki_Backed_Chatbot]] 통합 | LLM 에이전트가 봇 호출 |

**필요 인력**: PoC = 외부 컨설팅 0.5 FTE + 사내 IT 일부. MVP/확장 = **사내 RPA 운영자 1명 풀타임** (회계/IT 적성자 교육) + IT 백업. **거버넌스 위원회**: 회계·인사·인허가 각 1명 + IT 책임자 (월 1회).

## 본 사무소 권장 — 3-tier 아키텍처

### 종합 결론
**전사 RPA 부적합. 선택적·국지적 도입 강력 권장**. 5가지 근거:
1. **다우오피스 오픈 API 풍부** → 인사·총무·메일·결재 자동화는 [[자동화_시나리오|n8n/Power Automate]]가 RPA 대비 압도적 저렴·안정
2. **별도 PMS+ERP API 노출 정도**가 동기화 도구 분기 결정
3. **GPU 부재** → 자가치유 RPA·고급 AI-OCR 사내 운영 어려움 → AI-OCR은 클라우드 SaaS 결합 (CLOVA/Upstage/Google Document AI)
4. **운영 중인 [[사내_챗봇_시스템]] + 검토 중인 [[Wiki_Backed_Chatbot]]** 이 매뉴얼·규정·도면 검색 영역 커버 → RPA 중복 도입 X
5. **국내 건축사무소 RPA 사례 부재** → 사내 주도 PoC 필수

### 3-tier 분담

```
[다우오피스 ↔ NAS ↔ PMS/ERP]  → 워크플로우 자동화 (n8n / Power Automate)
[정부 사이트 (세움터·홈택스·4대보험 EDI)]  → RPA (제한적·필수 영역만)
[사내 매뉴얼·규정·도면 검색]  → LLM 에이전트 ([[Wiki_Backed_Chatbot]])
[BIM/CAD 도서 일괄 처리]  → Revit API + Dynamo + Python
[OCR 비정형 청구서·계약서]  → IDP(AI-OCR SaaS) + RPA(또는 워크플로우)
```

### 도입 시 5대 원칙
1. **봇 개수 X, "절감 시간 + 오류율 감소" 가 KPI** (Gartner Agent-washing 경고)
2. **사내 운영자 내재화** (벤더 외주는 첫 UI 변경에서 깨짐)
3. **거버넌스 위원회 운영** (계정·권한·로그·롤백 책임자 명시)
4. **RPA 봇은 도구화** (LLM 에이전트가 호출 가능한 형태로 표준화 — 24개월 후 [[Wiki_Backed_Chatbot|Agentic 통합]] 전제)
5. **민감 정보 보안** (4대보험·세금계산서는 봇 계정 분리 + 키 관리)

### 한 줄 요약

> **"전사 RPA"는 부적합. "5~10개 업무 한정 선택적 RPA + 다우오피스 API 워크플로우 자동화 + Wiki 기반 LLM 에이전트"의 3-tier 아키텍처가 본 사무소의 합리적 답.** 라이센스는 **Power Automate 또는 AutomateOne / CheckMATE** 중 PoC 후 결정. 24개월 후 Agentic 통합 전제로 봇은 도구화 가능한 형태로 설계.

## 즉시 액션 (1-3개월)
- [ ] 자동화 후보 업무 **30개 식별** + RPA/API/LLM 분기표 작성
- [ ] **다우오피스 오픈 API 인벤토리** (RPA로 풀 업무 vs API로 풀 업무 구분)
- [ ] 정부 사이트 자동화 후보 **3개 식별** (4대보험 EDI, 홈택스, 세움터)
- [ ] **Power Automate Desktop 무료 PoC** (M365 환경 일부 활용 가능 시)
- [ ] AutomateOne/CheckMATE 데모 요청 (1봇 PoC 비용·기간 비교)

## 출처
- raw/2026-05-06_rpa_namuwiki_overview.md (RPA 개관, 사용자 제공)
- raw/2026-05-06_rpa_arch_firm_feasibility_research.md (본 페이지 1차 소스, 60+ 출처)
- 핵심 사례:
  - Skanska × UiPath: <https://www.uipath.com/resources/automation-case-studies/skanska-construction-rpa>
  - Arcadis AI in AEC 백서: <https://www.arcadis.com/-/media/project/arcadiscom/com/blogs/global/susanne-knorr/2020/the-future-of-architecture-engineering-and-construction-is-digital-and-intelligent/artificial-intelligence-in-the-aec-industry-a-code-of-practice.pdf>
  - 그리드원 에이전틱 자동화 전략 (전자신문 2026.04): <https://www.etnews.com/20260427000437>
  - SS&C Blue Prism Agentic AI vs RPA: <https://www.blueprism.com/resources/blog/agentic-ai-vs-rpa-vs-ai-agents-comparing/>
  - UiPath × Microsoft Copilot Studio 양방향 통합: <https://www.microsoft.com/en-us/microsoft-copilot/blog/copilot-studio/announcing-agentic-automation-with-bidirectional-integration-between-microsoft-copilot-studio-uipath/>
  - KoreaDeep 건설/건축 AI 도입: <https://www.koreadeep.com/blog/%EA%B1%B4%EC%84%A4%EA%B1%B4%EC%B6%95%EC%97%90-ai%EB%A5%BC-%EB%8F%84%EC%9E%85%ED%95%98%EB%A9%B4-%EB%AC%B4%EC%97%87%EC%9D%B4-%EB%8B%AC%EB%9D%BC%EC%A7%88%EA%B9%8C-166657>
  - n8n vs RPA Cost & Flexibility: <https://lets-viz.com/blogs/n8n-vs-rpa-uipath-power-automate-cost-flexibility>
- 컨설팅 보고서: Deloitte Global RPA Survey, BCG, McKinsey, EY, Gartner Agent-washing
- 수집일: 2026-05-06

## 관련
- [[자동화_시나리오]] — 본 사무소 5+1개 자동화 시나리오. RPA가 적합한 영역과 워크플로우 자동화가 적합한 영역의 분기
- [[워크플로우_자동화_도구]] — n8n / Power Automate / Make / Zapier 비교. RPA 와 보완 관계
- [[Wiki_Backed_Chatbot]] — 사내 LLM 에이전트. RPA 대체가 아닌 협력 (24개월 후 Agentic 통합)
- [[사내_챗봇_시스템]] — 매뉴얼·규정 검색 영역. RPA 중복 도입 회피 근거
- [[한국어_LLM_스택]] — AI-OCR 클라우드 SaaS (CLOVA/Upstage) 와 RPA 결합
- [[건축IT_사내개발_전략]] — 본 사무소 IT 전략 위치. 1단계 가성비 영역에 선택적 RPA 추가
- [[해외_AEC_사내개발]] — Skanska Sidekick + Dusty Robotics 같은 백오피스 RPA → 사내 LLM 챗봇 → 현장 자동화 진화 사례
- [[더존_아마란스10]] — ONE AI 가 IPA 를 통합형으로 풀려는 시도
- [[영림원_K-System_Ace]] — 연동형 ERP 와 RPA 결합 패턴
- [[정보_일원화_패턴]] — RPA 가 풀려는 본질 문제 (흩어진 입력의 일원화)
- [[통합형_vs_연동형]] — RPA 도입 시 통합 RPA 플랫폼 vs 워크플로우+RPA+LLM 분담의 양극
- [[Claude_Code]] — RPA 봇 개발 보조 도구 (벤더 SDK 코딩 시)
