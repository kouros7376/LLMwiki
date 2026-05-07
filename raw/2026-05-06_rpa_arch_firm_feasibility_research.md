---
source_type: deep_research
topic: 건축설계회사 RPA 도입 타당성
captured_at: 2026-05-06
contributor: Claude (Sonnet 기반 리서치)
---

# 건축설계사무소(300인 규모) RPA 도입 타당성 딥리서치 — 2026.05

## 0. 리서치 범위 + 방법

본 문서는 [[RPA]] 나무위키 발췌본([[2026-05-06_rpa_namuwiki_overview|raw/2026-05-06_rpa_namuwiki_overview.md]])을 토대로, 약 300명 규모의 국내 건축설계사무소(해마종합건축사사무소)가 RPA(Robotic Process Automation)를 정식 도입할 가치가 있는지를 평가하기 위해 작성되었다. 사무소의 실제 인프라(Synology NAS 70TB, [[다우오피스]] 그룹웨어 + 오픈 API, 별도 [[PMS]]·[[ERP]], GPU 서버 부재, 운영 중인 [[Gemini_File_Search|Gemini File Search]] 기반 사내 챗봇 `haema-knowledge-base`, 검토 중인 [[Wiki-Backed_Chatbot]])를 모두 변수로 반영한다.

**리서치 방법**:
- WebSearch 한국어/영어 병행 (12회)
- 글로벌 컨설팅 보고서(Deloitte, BCG, McKinsey, Gartner, Ernst & Young) 인용 발췌
- 국내 7개 RPA 벤더 + 해외 3대 벤더(UiPath, Automation Anywhere, Microsoft Power Automate) 공식 자료
- 건설·AEC 도메인 적용 사례(Skanska, Arcadis, AECOM 등)
- 2025-2026 IPA·Agentic AI 트렌드 자료(UiPath AI Trust Layer, Microsoft Copilot Studio, SS&C Blue Prism 등)

본 리서치의 결론은 **단순 예/아니오가 아니라, "어떤 작업에는 RPA, 어떤 작업에는 워크플로우 자동화([[n8n]]/[[Power_Automate]]), 어떤 작업에는 LLM 에이전트([[Wiki-Backed_Chatbot]] 확장)가 적합한가"의 분기점을 명시**하는 데 있다.

---

## 1. 건축·AEC 업계 RPA 도입 현황 (사례 모음)

### 1.1 글로벌 AEC 기업

#### Skanska — 가장 명확한 RPA 성공 사례
북유럽계 글로벌 건설사 Skanska는 2017년 Shared Service Center에서 [[UiPath]] 기반 RPA를 도입했다. 첫 자동화 프로세스는 "지급 데이터 품질 검증"으로, 기존에는 직원 1명이 일주일을 들여 수백만 건의 트랜잭션을 점검했으나, 봇 도입 후 매일 5분 만에 완료되었다. 또한 협력사 프로젝트 등록 프로세스를 자동화해 수작업 오류를 제거하고 등록 속도를 단축했다.

Skanska는 이후 자체 AI 챗봇 `Skanska Sidekick`을 운영하며 콘텐츠 생성·정보 검색·의사결정 지원을 통합하고, 현장에서는 Dusty Robotics의 다공종(MEP·골조·건식벽체) 자동 먹매김 로봇을 도입했다. 즉 "백오피스 RPA → 사내 LLM 챗봇 → 현장 실물 자동화"로 단계적 진화를 보여준다.

#### Arcadis — 백서(白書) 수준의 AI/AEC 전략
네덜란드계 Arcadis(34,000명, 30개국)는 2020년 "Artificial Intelligence in the AEC Industry — A Code of Practice" 백서를 발간해, 클래식 RPA를 [[BIM]] 기반 데이터 흐름·AI 기반 의사결정과 함께 다루는 종합 전략을 제시했다. RPA 단독 도입 사례보다는 "AEC 전사 디지털 변환의 일부"로 자리매김시킨 것이 특징이다.

#### AECOM
글로벌 인프라 컨설팅사 AECOM(150개국)에서 공개된 단독 RPA 사례는 제한적이나, ENR 매체에서 다뤄진 바와 같이 변경 지시(change order) 검증, 공급업체 송장 처리 등 백오피스 자동화에 RPA를 활용 중인 것으로 알려져 있다.

#### Foster + Partners, Gensler
두 회사 모두 사내 R+D 팀(Foster Applied R&D, Gensler Research Institute)을 통해 [[Dynamo]]/[[Grasshopper]] 기반의 BIM 자동화에 집중하는 편으로, 클래식 RPA 사용 사례는 공개되지 않았다. 즉 "디자인 자동화"는 Dynamo·Python으로, "백오피스 자동화"는 ERP·BIM 360 API 직결로 푸는 방향이 일반적이다.

### 1.2 한국 건축사무소·건설사

검색 결과, **국내 종합건축사사무소 단위에서 공개된 RPA 도입 사례는 거의 없다**. 건설업 영역에서도 현대건설·GS건설·대우건설 등 시공사 사례는 보도자료 수준의 단발성 언급뿐이며, 체계적인 사례집은 부재하다. 단편적 정보:
- LG화학·LG유플러스·KB국민은행·LG CNS·한화토탈·삼성전기 등은 수만~수십만 시간 단위의 절감 효과를 공식 발표
- 농심켈로그·하나은행·삼성바이오로직스·경기도청 등이 후속 도입
- "건축·건설 도메인"에서 한국어로 잡히는 사례는 시공사의 SAP·ERP 자동입력 정도

이 공백 자체가 본 사무소에 시사하는 바는 크다 — **국내 건축업계는 RPA 후발주자이며, 잘 만든 사례가 없다는 것은 곧 벤더 컨설팅이 도메인 지식 없이 들어올 가능성**이 높다는 뜻이다.

### 1.3 도면·인허가·견적·발주 등 건축 특화 자동화

[[koreadeep_blog|코리아딥(KoreaDeep)]] 등 국내 IDP 솔루션 기업의 사례 정리에 따르면, 건설·건축 분야 AI/RPA 적용은 다음 순서로 효과가 검증된다:
1. **초기 단계**: 매월 반복되는 기성청구서, 하도급 계약서
2. **확장 단계**: 공사도급계약서, 준공서류
3. **고도화 단계**: 형식 편차가 큰 견적서·내역서

설계사무소 관점에서는 "기성청구서·하도급 계약서" 대신 **세금계산서·외주 설계비 청구서·관청 인허가 첨부 도서·국토부/지자체 통계 입력**이 같은 자리에 들어간다.

### 1.4 BIM·CAD 도구와 RPA의 결합

학술 자료(ResearchGate, Springer Building Pathology 2025 등)에 따르면 RPA + BIM 결합의 대표 시나리오는:
- Revit/Dynamo 스크립트 자동 실행으로 도면 일괄 변환·일괄 출력
- 사양서·도면·DB 정보를 추출해 BIM 모델에 자동 충진
- BIM 모델에 대한 품질 검사(clash detection 결과 정리, 표준 준수 확인)
- 데이터 입력 시간 최대 90% 절감 보고

다만 이 영역은 **클래식 RPA(GUI 조작)보다는 BIM 도구의 API/스크립팅**(Revit API, Dynamo, Grasshopper, Forge/APS)이 훨씬 효율적이다. RPA는 "BIM 결과물을 외부 시스템(ERP·관청 사이트·세금계산서)에 옮기는" 다리 역할에 집중하는 것이 합리적이다.

---

## 2. 본 사무소 적용 가능 업무 매트릭스

| 업무 영역 | 세부 업무 | RPA 적합도 | 대안 | 비고 |
|---|---|---|---|---|
| **회계·경리** | 세금계산서 발행/수취 대사 | ★★★★★ | API 가능시 API | 메타넷 등 SAP+RPA 사례 존재 |
|  | 카드 매입전표·법인카드 사용내역 자동 분개 | ★★★★ | ERP API | OCR 결합 필요 |
|  | 외주 설계비·용역비 청구서 처리 | ★★★★ | IDP+ERP API | 형식 편차 큼 |
|  | 국세청 홈택스 신고·증빙 다운로드 | ★★★★★ | 대안 거의 없음 | RPA의 전형적 강점 영역 |
|  | 원가 계산·프로젝트별 손익 집계 | ★★★ | Pandas + ERP API | LLM 분석 결합 가치 ↑ |
| **인사·총무** | 근태 집계 (다우오피스 → 급여 시스템) | ★★ | **다우오피스 오픈 API 직결** | RPA 불필요할 가능성 ↑ |
|  | 출장 정산·증빙 분류 | ★★★ | OCR + LLM | IPA 영역 |
|  | 4대보험 EDI 신고(취득/상실) | ★★★★★ | 대안 거의 없음 | 정부 사이트는 API 빈약 → RPA 필수 |
|  | 채용 공고 게시·이력서 수집 | ★★★ | 채용 사이트 API | 단순 |
|  | 자격증 갱신·면허 만료 알림 | ★★ | 위키 + 자체 스크립트 | 굳이 RPA 불필요 |
| **인허가·관청** | 세움터(건축행정시스템) 진행 추적 | ★★★★ | API 부재 → RPA만 가능 | **건축사무소 RPA의 킬러 시나리오 후보** |
|  | 건축 인허가 신청 도서 일괄 업로드 | ★★★★ | 동일 | 첨부 표준화가 관건 |
|  | 환경영향평가·교통영향평가 진행 추적 | ★★★ | 관청별 사이트 상이 | 사례별 개발 |
|  | 국토부·지자체 통계 입력 | ★★★ | API 일부 가능 | 정기 반복 |
| **발주·구매** | 자재·소모품 발주 | ★★ | 구매 사이트 API | 설계사무소는 발주량 적음 |
|  | 외주 협력사(구조·기계·전기·토목) 관리 대장 갱신 | ★★★ | ERP/PMS + 위키 | 인덱싱은 [[Wiki-Backed_Chatbot]]이 더 적합 |
|  | 도서 출도(인쇄소)·CD 사본 발송 추적 | ★★ | PMS 자체 기능 활용 | 수기 영역 |
| **PMS/ERP 동기화** | 다우오피스 ↔ PMS 일정 동기화 | ★★ | **다우오피스 오픈 API + PMS API** | API 우선 |
|  | PMS ↔ ERP 프로젝트 코드 동기화 | ★★★ | 양 시스템 API 여부에 따라 | API 부재시 RPA |
|  | 견적·계약 진행 단계 알림 | ★★ | 워크플로우 자동화 | n8n / Power Automate |
| **CAD/BIM 워크플로우** | DWG → PDF 일괄 변환·일괄 출력 | ★★★ | **AutoCAD Script / Revit API** | RPA보다 도구 내장 자동화 우선 |
|  | 도면 페이지·도면번호 일괄 갱신 | ★★ | Dynamo / Python | 동일 |
|  | 도면 검수(스탬프·서명·날짜 확인) | ★★★ | Vision LLM + 스크립트 | 신기술 영역 |
|  | NAS 도면 폴더 → 클라이언트 전송 자동화 | ★★★ | Synology 자체 스크립트 | rclone·ShareSync |

**매트릭스 요약**:
- **★★★★~★★★★★ (RPA 강력 추천)**: 정부·관청 사이트 자동화(세움터, 4대보험 EDI, 홈택스), 세금계산서 대사
- **★★★ (조건부 RPA)**: 회계·인사 일부, PMS/ERP 동기화 (양쪽 API가 부재할 때만)
- **★★ 이하 (RPA 부적합)**: 다우오피스·BIM 도구처럼 풍부한 API/스크립팅이 이미 있는 영역

다시 말해, 본 사무소에서 RPA가 **유일한 합리적 해법**인 영역은 "외부 정부 시스템 자동화" + "OCR 결합 회계 자동화" 두 개 정도이다.

---

## 3. RPA vs LLM 자동화 vs API 통합 — 비교

| 차원 | RPA | 워크플로우 자동화 ([[n8n]]·[[Power_Automate]]) | API 통합 (직접 코딩) | LLM 에이전트 (Agentic AI) |
|---|---|---|---|---|
| **방식** | GUI 화면 조작 | API 노드 + 일부 GUI | HTTP/SDK 직접 호출 | LLM이 도구 선택·실행 |
| **선결 조건** | GUI 안정성 | 대상 SaaS의 API/Webhook | 대상 시스템 API | LLM + 도구 인벤토리 + 검증 레이어 |
| **개발자** | 비전공자 가능(1주~1달 교육) | 비전공자 가능(시민 개발자) | 개발자 필수 | 프롬프트 엔지니어 + 개발자 |
| **변경 취약성** | 매우 높음 (UI 바뀌면 깨짐) | 낮음 (API 안정) | 낮음 | 중간 (모델·프롬프트 회귀) |
| **유지비** | 높음 (지속적 보수) | 낮음~중간 | 낮음 | 중간 (LLM 호출비) |
| **초기 비용** | 높음 (라이센스) | 낮음 (오픈소스 가능) | 인건비만 | LLM 호출비 + 인프라 |
| **적합 업무** | 정부 사이트, 레거시 GUI | SaaS 간 데이터 흐름 | 사내 시스템 통합 | 비정형 의사결정·문서 분석 |
| **부적합 업무** | 비정형 데이터, 빈번한 UI 변경 | 복잡한 GUI 조작 | API 없는 시스템 | 결정론·감사 추적 필수 영역 |
| **2026 트렌드** | IPA·Agentic 결합 | LLM 노드 추가 | 변동 적음 | 폭발적 성장 |
| **본 사무소 적합도** | 정부 사이트만 ★★★★ | SaaS·ERP 흐름 ★★★★★ | 다우오피스 API 활용 ★★★★ | 사내 챗봇·문서 분석 ★★★★ |

### 3.1 본 사무소 환경에서의 합리적 조합

본 사무소는 ① **다우오피스 오픈 API 보유**, ② **별도 [[PMS]] + [[ERP]] 운영**, ③ **GPU 부재**, ④ **[[Gemini_File_Search]] 기반 사내 챗봇 운영 중**, ⑤ **[[Wiki-Backed_Chatbot]] 도입 검토 중**이라는 다섯 가지 조건을 갖는다.

이 조건을 종합하면 권장 분담은 다음과 같다:

```
[다우오피스 ↔ NAS ↔ PMS/ERP 사이] → 워크플로우 자동화 (n8n / Power Automate)
[정부 사이트, 4대보험 EDI, 세움터, 홈택스]  → RPA (제한적·필수 영역만)
[사내 매뉴얼·규정·도면 검색·작성 보조]   → LLM 에이전트 (Wiki-Backed Chatbot 확장)
[BIM/CAD 도서 일괄 처리]                → Revit API + Dynamo + Python
[OCR 기반 비정형 청구서·계약서 처리]     → IDP(AI-OCR) + RPA(또는 워크플로우)
```

이 분담은 **"RPA를 도입하지 않는다"가 아니라, RPA의 적용 범위를 5~10개 업무로 좁힌다**는 것을 의미한다.

### 3.2 [[자동화_시나리오|n8n / Power Automate]] vs RPA(UiPath/AutomateOne)의 분기

- **API가 있고 데이터 흐름이 핵심이면** → n8n / Power Automate
- **GUI만 있고 사람이 클릭해야 한다면** → RPA
- **둘 다 가능하면** → 워크플로우 자동화 우선 (유지비·내구성 압도적)

본 사무소 맥락에서, 만약 다우오피스 오픈 API가 충분하다면 인사·총무·메일·캘린더·결재 영역에서는 RPA가 사실상 불필요하다.

---

## 4. 국내 7개 + 해외 3대 RPA 벤더 상세 비교

### 4.1 해외 3대

| 항목 | UiPath | Automation Anywhere | Microsoft Power Automate |
|---|---|---|---|
| **본사** | 미국(루마니아 출발) | 미국 | 미국 |
| **가격(2026 기준)** | Pro Attended ~$135/봇·월, Pro Unattended ~$420/봇·월 (Orchestrator 포함) | 1 unattended bot + 1 creator + control room ~$750/월, 추가 attended bot $125/월 | $15~$40/사용자·월 (M365 라이선스 보유시 사실상 추가비 0) |
| **한국어 지원** | 우수(공식 한국지사·교육) | 양호 | 우수(MS 한국 채널) |
| **AI/Agentic** | Agentic Automation, AI Trust Layer, Maestro, Autopilot | IQ Bot, Document Automation | Copilot Studio 통합, AI Builder |
| **건축·건설 사례** | Skanska(공식 사례) 외 다수 시공사 | 회계 IDP 강점 | 본 사무소처럼 M365/다우 환경에서 빠른 PoC |
| **장점** | 시장 1위, 거버넌스, 대규모 봇 운영 | IDP/Document AI 우수, 클라우드 확장성 | M365 기반 비용 효율, 시민 개발자 친화 |
| **단점** | 봇 단가 높음 | 한국 사례 적음 | 복잡한 GUI 자동화는 약함 (Desktop flow는 보강 중) |
| **본 사무소 적합도** | 대형 표준화 후 확장에 적합 | 회계 IDP 한정 | **다우오피스 + 일부 MS365 환경이면 비용 효율 1위** |

### 4.2 국내 7대

| 벤더 (제품) | 강점 | 가격(공개 범위) | 도입 사례 | 본 사무소 적합도 |
|---|---|---|---|---|
| **AutomateOne (그리드원)** | 국내 최초, "오류 자동 검증" 특허, 한국환경공단·우정사업본부·NH농협·IBK·신한카드·교보생명 등 30+ 고객 | 비공개(영업 협의), 무료판 `ezbot` 별도 제공 | 공공·금융 강세 | ★★★★ (국내 사례·지원 가장 풍부) |
| **CheckMATE RPA (Symation)** | 비주얼 로우코드, RDA 1인 1봇 컨셉, AI Agent + AI OCR 통합 | 비공개, GoBizKorea·Amazon 월 구독 모델 노출 | 중소·중견기업 다수 | ★★★★ (비용 합리적) |
| **Brity Automation (삼성SDS)** | 자체 사용 11개월에 82만 시간 절감, 22,000+ 프로세스, 삼성전기 24만 시간 절감, 수협중앙회 도입 | Cloud Orchestrator + Designer 수 + Bot 수 기준 월 과금 | 삼성 계열사·금융·공공 | ★★★ (대기업 표준, 중견 사무소엔 과함) |
| **Worktronics (이든티앤에스)** | 중소·중견 특화 컨설팅, 가격 유연성 | 비공개 | 공공·중견 | ★★★ |
| **AUTOBE RPA (링크제니시스)** | 시스템 검증 RPA(AUTOBE-MAT) 1위 기업의 사무자동화 라인, 화면 녹화 → 자동 스크립트, 로우코드 | 비공개, 커뮤니티 공개 | 자동차·임베디드 검증 출신 | ★★★ (검증 도메인 강점, 사무 자동화는 후발) |
| **BATEM** | 국내 RPA 벤더 중 후발, 가성비 지향 | 비공개 | 한정적 | ★★ |
| **인바이즈 (invaiz.com)** | 부산 스타트업, AI 기반 셀프힐링 웹 드라이버, Selenium 한계 보완, 웹 자동화 특화 | 비교적 저렴 (스타트업 가격) | 중소기업·웹 RPA | ★★★ (웹 RPA 한정으로 PoC 가치) |

### 4.3 라이센스 모델 비교

- **User 단위**: Power Automate, n8n (사용자 수만큼 과금)
- **Bot 단위**: UiPath, Automation Anywhere, AutomateOne, Brity, CheckMATE, AUTOBE (봇 수 × Attended/Unattended)
- **Cloud vs On-prem**: UiPath·Power Automate는 클라우드 우선, AutomateOne·Brity·CheckMATE는 온프레미스 동시 지원 (한국 정부·금융 요건상 중요)

### 4.4 건축·건설 도메인 적합성 평가

공식 도입 사례 기준으로 본다면, 본 사무소 환경에서 **현실적으로 검토할 가치가 있는 후보는 3개**:
1. **Microsoft Power Automate** — 다우오피스/M365 일부 사용 환경에서 비용 압도적
2. **AutomateOne (그리드원)** — 국내 사례·지원·정부 사이트 자동화 노하우
3. **CheckMATE (Symation)** — 1인 1봇 컨셉 + AI OCR 결합으로 PoC 부담이 작음

UiPath는 향후 50봇 이상으로 확장될 때, Brity는 본 사무소 규모에 비해 과대 투자가 될 가능성이 크다.

---

## 5. 300인 사무소 도입 타당성 분석

### 5.1 ROI 추정

국내 평균 통계 (디지털투데이, GDS Online, AImultiple 등):
- 도입 기업 평균 ROI **약 200%**, 대부분 **12개월 내 회수**
- 표본 사례: 초기 6,500만원 + 연간 운영비 1,800만원 → 연간 절감 1.7억원, ROI 233.8%, 회수 4.6개월
- RPA 도입 기업 월평균 인건비 절감 **약 6,000만원**
- 평균 도입 비용 **약 1억원 내외** (라이센스 + 컨설팅 + 1차 PoC)

본 사무소(300명) 규모로 환산:
- **봇 5~10기** (정부 사이트 자동화 + 회계 OCR + 인허가 추적 + 4대보험 EDI 위주)
- 라이센스: AutomateOne/CheckMATE 기준 **연 3,000~6,000만원 추정**, Power Automate 기준 **연 500~1,500만원 추정**
- 컨설팅·1차 개발: **3,000~5,000만원** (외주 시) 또는 **사내 1명 0.5 FTE** (내재화 시)
- 운영비: 연 **1,000~2,000만원** (라이센스 갱신 + 유지보수)
- 절감 가정: 회계 1명 + 인사·총무 0.5명 + 인허가 보조 0.5명 = **연 인건비 1.5억원 상당의 시간 회수**
- 보수적 ROI: **연 절감 1억원 / 연 비용 5,000만원 = 100% (12~18개월 회수)**

### 5.2 일반적 실패 패턴 (컨설팅 보고서 인용)

- **Ernst & Young**: 초기 RPA 프로젝트 **실패율 30~50%**
- **Deloitte 글로벌 RPA Survey**: 도입 기업 53%, 도입+계획 78%이지만 **확장(scaling) 단계 진입은 절반 이하**
- **McKinsey**: RPA 확장과 변화관리(change management) 어려움이 최대 장애
- **PwC**: 거버넌스 부재, 내부통제 미비
- **BCG**: 디지털 전환 일반론으로 **70% 실패**, 850개 기업 분석에서 **목표 달성 35%**
- **Gartner (2025-2026)**: "Agent Washing" 현상 — 기존 RPA를 "Agentic AI"로 재포장하는 마케팅, 실제 자율 추론·계획 능력 부재 사례 다수

본 사무소가 피해야 할 패턴:
1. **전사 일괄 도입** → PoC 없이 확장 시 실패율 폭증
2. **벤더 주도 컨설팅** → 도메인 모르는 컨설팅이 만든 봇은 첫 UI 변경에서 깨짐
3. **봇 개수 KPI** → "봇 수"가 아니라 "절감 시간" + "오류율 감소"가 KPI여야 함
4. **거버넌스 부재** → 봇이 깨졌을 때 누가 고치는가, 권한·계정·로그를 누가 관리하는가 미정

### 5.3 단계적 도입 로드맵 (300명 기준 권장)

| 단계 | 기간 | 예산 | 핵심 과업 | 성공 지표 |
|---|---|---|---|---|
| **0. 사전 진단** | 1개월 | 0~500만원 | 자동화 후보 업무 30개 식별·우선순위, RPA vs API vs LLM 분기 | 후보 리스트 + 분기표 |
| **1. PoC** | 2~3개월 | 500~1,500만원 | 가장 명확한 1~2개 업무(예: 4대보험 EDI, 홈택스 자료 수집) 자동화 | 1개 봇 가동, 절감 시간 측정 |
| **2. MVP** | 3~6개월 | 3,000~5,000만원 | 5~7개 업무 자동화, 사내 운영자 1명 교육 | 누적 절감 시간 ≥ 1,000h/연 |
| **3. 확장** | 6~12개월 | 5,000~8,000만원 | 10~15개 업무 + 거버넌스(봇 인벤토리·계정·모니터링) 정립 | ROI ≥ 100% |
| **4. 진화** | 12개월~ | 운영비 | RPA → IPA → Agentic 전환, [[Wiki-Backed_Chatbot]]과 통합 | LLM 에이전트가 봇을 호출하는 구조 |

### 5.4 필요 인력·기간

- **PoC 단계**: 외부 컨설팅 0.5 FTE + 사내 IT 1명 일부 시간
- **MVP/확장 단계**: 사내 RPA 운영자 **1명 풀타임** (기존 IT/회계 인력 중 적성자 교육) + IT팀 백업
- **거버넌스 위원회**: 회계·인사·인허가 부서 각 1명 + IT책임자 (월 1회 봇 검토)

---

## 6. RPA → IPA → Agentic AI 진화 (2025-2026 트렌드)

### 6.1 용어 정리

- **RPA (Robotic Process Automation)**: 정해진 GUI 시퀀스 반복 — 결정론적
- **IPA (Intelligent Process Automation)**: RPA + AI-OCR + ML 분류기 (비정형 입력 처리)
- **CA (Cognitive Automation)**: NLU/지식 기반 의사결정 결합
- **APA / Agentic Process Automation**: LLM 에이전트가 목표를 받아 도구·봇을 자율적으로 선택·실행

### 6.2 2026년 시장 흐름

- **UiPath**: Maestro (오케스트레이션), Agent Builder, Autopilot, **AI Trust Layer** (데이터·정책·모델·감사 통제), Snowflake/SharePoint/Confluence/SOP 등에 RAG 임베딩
- **Microsoft**: Copilot Studio + Power Automate + Agent SDK 통합. Word/Excel/PowerPoint Copilot이 GA, **UiPath ↔ Copilot Studio 양방향 통합** 발표
- **Automation Anywhere**: AI Agent Studio, IQ Bot의 Agent화
- **SS&C Blue Prism**: Agentic AI vs RPA 비교 백서 — "교체가 아니라 공존, 책임 분담"
- **Gartner**: 2026년 "agent washing" 경고 — 자율 추론·계획·목표 지향 능력이 없는 RPA를 Agent로 재포장하는 사례 다수
- **국내 그리드원**: 2026.04 "에이전틱 자동화 플랫폼 전략" 공개 (전자신문)

### 6.3 LLM 에이전트가 RPA를 대체하는가?

**결론은 "공존하되 책임이 바뀐다"**. 2026년 기준 신뢰성 높은 에이전트 배포 패턴:
1. **Planner**: 목표 분해 (LLM)
2. **Tool Router**: 어떤 시스템을 호출할지 결정 (LLM + 규칙)
3. **Executor**: 실제 시스템 조작 — **여기에 RPA 봇이 도구로 등장**
4. **Verifier**: 출력 검증 (규칙 + LLM)
5. **Observability**: 감사 추적

즉 **RPA는 Executor 레이어로 잔존**한다. 다만 "사람이 봇을 직접 트리거"하던 시대 → "에이전트가 봇을 도구로 호출"하는 시대로 바뀐다. 이 패턴에서는 RPA 봇 개발이 줄지 않고 오히려 **재사용 가능한 도구로 표준화**되어야 한다.

### 6.4 본 사무소의 분기 판단 — "지금 RPA vs 6~12개월 기다리기"

- **지금 도입 권장 영역** (대체 불가):
  - 4대보험 EDI, 홈택스, 세움터 등 정부 사이트 자동화는 LLM 에이전트가 6개월 내 대체할 수 없다 (UI 의존 + 보안 + 캡차)
  - 회계 세금계산서 대사처럼 결정론적 규칙이 분명한 영역
- **기다려도 되는 영역** (LLM 에이전트가 더 잘 풀 가능성):
  - 외주 청구서·계약서 비정형 분류 → IDP/LLM이 빠르게 진화 중
  - 사내 문의 응대·매뉴얼 검색 → 이미 [[Wiki-Backed_Chatbot]]으로 처리 중
- **결론**: "정부 사이트 자동화 + 회계 핵심 1~2개" 만 우선 RPA로, 나머지는 LLM 에이전트와 워크플로우 자동화로 분산

---

## 7. 본 사무소 권장 — 결론과 근거

### 7.1 종합 권장: "선택적·보수적 RPA 도입"

본 사무소가 RPA를 **전면 도입할 필요는 없다**. 그러나 **선택적·국지적 도입은 강하게 권장**한다. 근거:

1. **다우오피스 오픈 API**가 풍부하다는 점에서 인사·총무·메일·결재 영역의 자동화는 [[n8n]] 또는 [[Power_Automate]]로 처리하는 편이 RPA보다 압도적으로 저렴·안정적이다.
2. **별도 PMS + ERP**의 API 노출 정도에 따라 동기화 영역의 도구가 달라진다. 양쪽 다 API가 있다면 워크플로우 자동화, 한쪽이라도 GUI만 있으면 RPA가 필요하다.
3. **GPU 부재**는 자가치유 RPA(셀프힐링)·고급 AI-OCR을 사내에서 운영하기 어렵게 한다. 따라서 AI-OCR은 클라우드 SaaS(Naver CLOVA OCR, Upstage Document AI, Google Document AI 등)와 결합해야 한다.
4. **운영 중인 [[Gemini_File_Search]] 챗봇 + 검토 중인 [[Wiki-Backed_Chatbot]]**이 이미 사내 매뉴얼·규정·도면 검색 영역을 커버하고 있다. 이 영역에 굳이 RPA를 도입할 이유는 없다.
5. **국내 건축사무소 RPA 사례 부재** = 벤더 주도 컨설팅 시 도메인 무지 위험 → 사내 주도 PoC 필수.

### 7.2 단계별 권장 액션

#### 즉시 (1~3개월)
- 자동화 후보 업무 **30개 식별** + RPA/API/LLM 분기표 작성
- **다우오피스 오픈 API 인벤토리** 작성 (RPA로 풀어야 할 업무 vs API로 풀 업무 구분)
- 정부 사이트 자동화 후보 **3개 식별** (4대보험 EDI, 홈택스, 세움터 등)
- Power Automate Desktop **무료 PoC** (M365 환경 일부 활용 가능 시)

#### 단기 (3~6개월)
- AutomateOne 또는 CheckMATE 1봇 PoC (정부 사이트 자동화 1건)
- **AI-OCR**은 SaaS 결합 (Naver CLOVA, Upstage 등)
- 사내 RPA 운영자 1명 지정 + 1주~1달 교육

#### 중기 (6~12개월)
- 봇 5~7기 확장
- [[Wiki-Backed_Chatbot]] 정식 가동 후, "봇 트리거를 챗봇이 호출"하는 패턴 시도 (Agentic 진입)
- 거버넌스 위원회 구성 (회계·인사·인허가·IT)

#### 장기 (12~24개월)
- IPA 단계: AI-OCR 안정화, 비정형 청구서 처리
- Agentic 단계: LLM 에이전트가 봇 인벤토리를 도구로 호출

### 7.3 도입 시 반드시 지킬 5가지 원칙

1. **봇 개수가 아닌 "절감 시간 + 오류율 감소"를 KPI로** (Gartner Agent-washing 경고 적용)
2. **사내 운영자 내재화** (벤더 주도 외주는 첫 UI 변경에서 깨짐)
3. **거버넌스 위원회 운영** (계정·권한·로그·롤백 책임자 명시)
4. **RPA 봇은 도구화** (LLM 에이전트가 호출할 수 있도록 표준화 — 미래 호환)
5. **민감 정보 보안** (4대보험·세금계산서 등은 봇 계정 분리 + 키 관리)

### 7.4 한 줄 요약

> "전사 RPA"는 부적합. **"5~10개 업무에 한정한 선택적 RPA + 다우오피스 API 워크플로우 자동화 + Wiki 기반 LLM 에이전트"의 3-tier 아키텍처**가 본 사무소의 합리적 답이다. 라이센스는 **Power Automate 또는 AutomateOne / CheckMATE** 중 PoC를 통해 비교 후 결정. 24개월 후 Agentic 통합을 전제로 봇은 "도구화 가능한 형태"로 설계한다.

---

## 8. 출처

수집일 모두 2026-05-06.

### 한국 RPA 일반·통계
- KT Enterprise — RPA 업무자동화 도입 사례: <https://enterprise.kt.com/bt/dxstory/1050.do>
- 디지털투데이 — RPA 도입 기업 월평균 6000만원 인건비 절감: <https://www.digitaltoday.co.kr/news/articleView.html?idxno=433251>
- GDS Online — RPA 프로젝트 ROI 측정: <https://www.gdsonline.tech/a-guide-to-measuring-rpa-roi/>
- KAIS 학술지 — 농협중앙회 RPA 포털 사례: <https://www.kais99.org/jkais/journal/Vol23No06/vol23no06p12.pdf>
- IBM RPA 디지털 업무 혁신 사례 (PDF): <https://www.ibm.com/downloads/cas/MLYVMP9Q>
- NIA — Automation RPA 도입 가이드: <https://www.nia.or.kr/common/board/Download.do?bcIdx=21820&cbIdx=39485&fileNo=1>
- 다빈치 블로그 — 다양한 RPA 사례·솔루션: <https://www.dvn.ci/blog/%EB%A1%9C%EB%B3%B4%ED%8B%B1-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EC%9E%90%EB%8F%99%ED%99%94rpa-%EC%97%85%EB%AC%B4-%EC%9E%90%EB%8F%99%ED%99%94%EC%99%80-%EB%B9%84%EC%9A%A9-%EC%A0%88%EA%B0%90%EC%9D%84-%EC%9C%84%ED%95%9C-%EC%84%A0%ED%83%9D-22310>
- SK AX 인사이트 — 한국 기업 RPA 47.5% 도입률: <https://www.skax.co.kr/insight/trend/3058>

### 국내 RPA 벤더
- 그리드원 (AutomateOne): <https://www.gridone.co.kr/solution/rpa>, <https://www.gridone.co.kr/>
- 그리드원 — 에이전틱 자동화 전략 공개 (전자신문): <https://www.etnews.com/20260427000437>
- 그리드원 RPA 무료 배포 (CIO Korea): <https://www.ciokorea.com/news/130790>
- AutomateOne 솔루션 소개 (한국밸런스 PDF): <http://www.valence.co.kr/images/rk-0068/AutomateOne.pdf>
- 그리드원 위키백과: <https://ko.wikipedia.org/wiki/%EA%B7%B8%EB%A6%AC%EB%93%9C%EC%9B%90>
- Symation CheckMATE RPA: <https://checkmate.symation.co.kr/>
- Symation Inc: <https://www.symation.co.kr/>
- 삼성SDS Brity RPA: <https://www.samsungsds.com/global/ko/solutions/off/brity/brity.html>
- 삼성SDS Brity Works 사내 도입 11개월 82만시간 절감: <https://www.samsungsds.com/kr/news/sds-genai-mediaday.html>
- 삼성SDS US — Brity Automation 하이퍼오토메이션: <https://www.samsungsds.com/us/ai-automation/brity-automation.html>
- 링크제니시스 AUTOBE RPA: <https://www.linkgenesis.co.kr/KR/Automation/Automation_RPA.aspx>
- 링크제니시스 AUTOBE 커뮤니티 (인공지능신문): <https://www.aitimes.kr/news/articleView.html?idxno=29131>
- 링크제니시스 나무위키: <https://namu.wiki/w/%EB%A7%81%ED%81%AC%EC%A0%9C%EB%8B%88%EC%8B%9C%EC%8A%A4>
- 인바이즈 (Invaiz) 공식: <https://invaiz.com/>
- 인바이즈 — RPA 어디에 써먹는가 (블로그): <https://invaiz.com/blog/rpa-%EA%B0%80-%EB%AD%90-%EC%96%B4%EB%94%94%EC%97%90-%EC%8D%A8%EB%A8%B9%EB%8A%94%EA%B2%81%EB%8B%88%EA%B9%8C>
- 인바이즈 나무위키: <https://namu.wiki/w/%EC%9D%B8%EB%B0%94%EC%9D%B4%EC%A6%88>
- 인바이즈 — 2025 자동화 툴 RPA 1편: <https://invaiz.com/blog/2025-automation-tools-summary-rpa-part-1>

### 해외 3대 벤더
- UiPath 가격: <https://www.uipath.com/pricing>
- UiPath Agentic Automation: <https://www.uipath.com/automation/agentic-automation>
- UiPath AI Trust Layer (Forum): <https://forum.uipath.com/t/agentic-automation-governance-ai-trust-layer-secure-governed-compliant-agentic-ai-automation/5718100>
- UiPath × Microsoft Copilot Studio 양방향 통합: <https://www.microsoft.com/en-us/microsoft-copilot/blog/copilot-studio/announcing-agentic-automation-with-bidirectional-integration-between-microsoft-copilot-studio-uipath/>
- UiPath × Deloitte Agentic ERP: <https://erp.today/uipath-deloitte-launch-agentic-erp-platform/>
- Accelirate — UiPath 2026 Trends: <https://www.accelirate.com/uipath-ai-agentic-automation-trends-2026/>
- Microsoft Copilot Studio: <https://www.microsoft.com/en-us/microsoft-365-copilot/microsoft-copilot-studio/>
- Microsoft Copilot 자율 에이전트: <https://www.microsoft.com/en-us/microsoft-copilot/blog/copilot-studio/unlocking-autonomous-agent-capabilities-with-microsoft-copilot-studio/>
- InfoSeeMedia — UiPath vs Power Automate vs Automation Anywhere 2026: <https://infoseemedia.com/blog/uipath-vs-microsoft-power-automate-vs-automation-anywhere/>
- EPC Group — UiPath vs Power Automate 2026: <https://www.epcgroup.net/uipath-vs-power-automate>
- AImultiple — RPA Pricing: <https://research.aimultiple.com/rpa-pricing/>
- AImultiple — 50 RPA Statistics: <https://research.aimultiple.com/rpa-stats/>
- Kanerika — UiPath vs Automation Anywhere: <https://kanerika.com/blogs/uipath-vs-automation-anywhere/>
- Automation Anywhere 재무·회계: <https://www.automationanywhere.com/kr/solutions/finance-accounting>
- Automation Anywhere — IDP: <https://www.automationanywhere.com/kr/rpa/intelligent-document-processing>

### AEC·건설업 RPA·BIM·자동화
- Skanska × UiPath 사례: <https://www.uipath.com/resources/automation-case-studies/skanska-construction-rpa>
- Skanska AI 사례: <https://www.usa.skanska.com/who-we-are/media/constructive-thinking/how-ai-is-revolutionizing-productivity-efficiency-and-knowledge-sharing/>
- Skanska AI Expert Network 사례: <https://aiexpert.network/ai-at-skanska/>
- Arcadis — AI in AEC 백서 (PDF): <https://www.arcadis.com/-/media/project/arcadiscom/com/blogs/global/susanne-knorr/2020/the-future-of-architecture-engineering-and-construction-is-digital-and-intelligent/artificial-intelligence-in-the-aec-industry-a-code-of-practice.pdf>
- ResearchGate — Adopting RPA in Construction: <https://www.researchgate.net/publication/378432839_Adopting_Robotic_Process_Automation_RPA_in_the_Construction_Industry>
- Springer — BIM integration in prefabricated additive construction (2025): <https://link.springer.com/article/10.1007/s41024-025-00678-6>
- Preprints — RPA + BIM Smart Building: <https://www.preprints.org/manuscript/202210.0045>
- LinkedIn — Transforming BIM with RPA: <https://www.linkedin.com/pulse/transforming-building-information-modeling-bim-rpa-sandya-devarajan-dttzc>
- Desapex — RPA in Construction: <https://www.desapex.com/blog-posts/the-benefits-of-robotic-process-automation-for-construction-companies>

### 회계·OCR·IDP
- 메타넷글로벌 — 세금계산서 RPA: <https://metanetglobal.com/bbs/board.php?bo_table=tech&wr_id=97>
- UiPath — 재무/회계 RPA: <https://www.uipath.com/ko/solutions/department/finance-and-accounting-automation>
- 슈피겐코리아 — IT 비전공자 회계 자동화: <https://www.spigenkorea.co.kr/culture/news.php?ptype=view&idx=1116&code=news>
- KoreaDeep — AI OCR 전사 문서 자동화: <https://www.koreadeep.com/blog/ai-ocr-rpa>
- KoreaDeep — 건설/건축 AI 도입: <https://www.koreadeep.com/blog/%EA%B1%B4%EC%84%A4%EA%B1%B4%EC%B6%95%EC%97%90-ai%EB%A5%BC-%EB%8F%84%EC%9E%85%ED%95%98%EB%A9%B4-%EB%AC%B4%EC%97%87%EC%9D%B4-%EB%8B%AC%EB%9D%BC%EC%A7%88%EA%B9%8C-166657>
- KoreaDeep — AI OCR ERP 가이드: <https://www.koreadeep.com/blog/ai-ocr-erp-automation-guide>
- 4대보험 EDI 신고 (NHIS): <https://edi.nhis.or.kr/webedi/help/html/appli/ap_05.html>
- 4대사회보험 정보연계센터: <https://www.4insure.or.kr/>

### 워크플로우 자동화 vs RPA
- Lets-Viz — n8n vs RPA Cost & Flexibility: <https://lets-viz.com/blogs/n8n-vs-rpa-uipath-power-automate-cost-flexibility>
- 인바이즈 — 2025 워크플로우 자동화 3편: <https://invaiz.com/blog/2025-automation-tools-summary-workflow-automation-part-3>
- WikiDocs — n8n 한글 가이드북: <https://wikidocs.net/290885>
- n8n 나무위키: <https://namu.wiki/w/n8n>
- 한컴테크 — n8n 서비스 자동화 전략: <https://tech.hancom.com/workflow-n8n-service/>

### Agentic AI / IPA 트렌드
- SS&C Blue Prism — Agentic AI vs RPA: <https://www.blueprism.com/resources/blog/agentic-ai-vs-rpa-vs-ai-agents-comparing/>
- Beam AI — RPA vs APA 2026: <https://beam.ai/agentic-insights/from-rpa-to-apa-why-bots-alone-cant-keep-up-in-2026>
- TechTarget — AI agents vs RPA: <https://www.techtarget.com/searchenterpriseai/tip/Compare-AI-agents-vs-RPA-Key-differences-and-overlap>
- Genpact — Agentic Process Automation Future: <https://www.genpact.com/insight/agentic-process-automation-the-future-of-intelligent-automation>
- Wadline — AI Agents vs Traditional Automation 2026: <https://wadline.com/mag/ai-agents-vs-traditional-automation-best-fit-2026>
- Kognitos — IA vs RPA vs APA: <https://www.kognitos.com/blog/intelligent-automation-vs-rpa-agentic-process-automation/>
- Klover — AI Agents in Enterprise Survey (McKinsey/PwC/Deloitte/Gartner): <https://www.klover.ai/ai-agents-in-enterprise-market-survey-mckinsey-pwc-deloitte-gartner/>

### 컨설팅 보고서·실패율
- Deloitte Global RPA Survey (PDF): <https://www2.deloitte.com/content/dam/Deloitte/us/Documents/process-and-operations/us-cons-global-rpa-survey.pdf>
- Mavim — Why 70% Digital Transformations Fail: <https://blog.mavim.com/why-70-of-digital-transformations-fail-insights-and-solutions>
- Flobotics — 50+ RPA Statistics: <https://flobotics.io/blog/rpa-statistics/>
- Integrate.io — Data Transformation Statistics 2026: <https://www.integrate.io/blog/data-transformation-challenge-statistics/>

### 본 사무소 컨텍스트 (내부 위키 참조)
- raw/2026-05-06_rpa_namuwiki_overview.md — RPA 개관
- raw/2026-05-06_wiki_backed_chatbot_research.md — 사내 LLM 위키 챗봇
- raw/2026-05-06_corporate_chatbot_development_research.md — 기업 챗봇 개발
- raw/2026-05-06_llm_wiki_operations_research.md — LLM Wiki 운영
- raw/2026-05-05_workflow_automation_tools.md — 워크플로우 자동화 도구

---

(끝)
