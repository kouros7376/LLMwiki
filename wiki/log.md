# 작업 이력

위키에 가해진 변경 이력을 시간순으로 기록한다. (최신이 위)

| 날짜 | 작업 | raw 자료 | 갱신/생성된 페이지 |
|------|------|----------|--------------------|
| 2026-05-05 | **건강검진** — 깨진 링크 1건(CLAUDE.md 헤더 `[[위키링크]]`) 백틱 처리. 6개 점검 항목 모두 클린. 메타 페이지(정보_일원화_패턴 56, 통합형_vs_연동형 43, 지식의_복리 33)가 그래프 중심에 위치 — 본 위키 통찰 구조 정상 | — | [[CLAUDE]] |
| 2026-05-05 | AI/자동화/AEC 자료 4건 ingest. 카테고리 2개(AI 바이브 코딩 / 전산업무 자동화) 채움 + 신규 카테고리 1개(건축 IT/AEC) 신설. Foster Hermes ↔ ERP 손익 일원화, Hydra ↔ 지식의 복리 등 의외의 동형성 추가 | `2026-05-05_vibe_coding_tools.md`, `2026-05-05_workflow_automation_tools.md`, `2026-05-05_aec_global_inhouse_dev.md`, `2026-05-05_aec_korea_inhouse_dev.md` | [[Claude_Code]], [[바이브_코딩_도구]], [[워크플로우_자동화_도구]], [[자동화_시나리오]], [[해외_AEC_사내개발]], [[정림건축_혁신본부]], [[AEC_자동화_플랫폼]], [[건축IT_사내개발_전략]], [[index]] |
| 2026-05-05 | ERP/PMS + LLM Wiki 자료 6건 ingest. 의외의 도메인 동형성 발견(LLM Wiki ↔ ERP 손익 일원화) → 메타 페이지 3개 신설 | `2026-05-05_amaranth10_overview.md`, `2026-05-05_younglimwon_ksystem_ace.md`, `2026-05-05_amaranth10_vs_ksystem_comparison.md`, `2026-05-05_construction_pms_reference.md`, `LLM Wiki AI에게 기억력을 심는 방.. 네이버블로그.md`, `LLM Wiki 활용해보기 네이버 블로그.md` | [[더존_아마란스10]], [[영림원_K-System_Ace]], [[ERP_벤더_비교]], [[건설_PMS]], [[ERP_RFP_체크리스트]], [[LLM_Wiki_개념]], [[지식의_복리]], [[정보_일원화_패턴]], [[통합형_vs_연동형]], [[건강검진]], [[index]] |
| 2026-05-05 | 위키 초기화 — 폴더 구조, CLAUDE.md, index, log 생성 | — | [[index]], [[log]] |

## 메모 — 의외의 연결점 누적

### 2차 ingest (2026-05-05, AI/자동화/AEC)
- **Foster Hermes (클라우드 단일 데이터원)** = ERP 손익 일원화 = 위키 단일 정보원 → [[정보_일원화_패턴]] 의 AEC 도메인
- **Foster Hydra (130만 시뮬레이션 자산화)** = [[지식의_복리]] 의 시뮬레이션·컴퓨테이셔널 디자인 도메인
- **Speckle (오픈 데이터 허브) vs Autodesk Construction Cloud (통합)** = [[통합형_vs_연동형]] AEC 도메인 양극
- **NXT BLD 2026 "Applications on Demand" 트렌드** = [[Claude_Code]] + MCP 구조와 정확히 같은 방향
- **Foster "AI 실용성, 이미지 생성 회피" 원칙** = [[ERP_RFP_체크리스트]] 의 데모 검증 원칙 (마케팅 ≠ 실무 가치)
- **정림 J-edu 모델** = [[지식의_복리]] 의 조직·인적자원 차원 구현
- **정림 BIM 규모검토 Package "단일 정보원"** = ERP 손익 일원화의 BIM 도메인 구현
- **n8n 셀프호스트 + Claude Code + MCP** = 모두 "사내 인프라 + 외부 LLM = 데이터 정책 보존" 동형 패턴
- **ERP 통합형/연동형 분기 = AEC 통합형/연동형 분기** → 두 결정을 일관되게 해야 사무소 IT 전략 모순 없음 (→ [[건축IT_사내개발_전략]])

### 1차 ingest (2026-05-05, ERP/PMS + LLM Wiki)
- **LLM Wiki "지식 복리 누적"** ↔ **대림산업 PMS "손익 일원화"** 본질 동일 → [[정보_일원화_패턴]]
- **더존 아마란스 vs 영림원 K-System** ↔ **ChatGPT Workspace vs Claude+MCP** 같은 양극 형태 → [[통합형_vs_연동형]]
- **위키 Lint / ERP 정합성 / PMS 마감 점검** 모두 같은 메커니즘 → [[건강검진]]
- 블로그 작가의 **"Jira issue 다 ingest"** 아이디어 ↔ ERP 마이그레이션의 레거시 데이터 이관 동일 구조

이 통찰들이 [[ERP_RFP_체크리스트]], [[건축IT_사내개발_전략]] 의 "1차 결정은 통합형/연동형 분기" 의 근거가 됨.
