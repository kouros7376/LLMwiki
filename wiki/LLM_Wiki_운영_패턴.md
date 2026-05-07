# LLM Wiki 운영 패턴 — 1인 → 5~10명 admin 큐레이션 → 전사 확장

[[LLM_Wiki_개념]] 의 3 레이어·3 모드 위에서 **운영 규모를 키울 때 발생하는 문제와 패턴**을 정리한 메타 페이지. 본 kouwiki 가 1인 운영을 검증했고, 다음 단계는 사내 5~10명 admin 큐레이션 모델로 확장 — 그 과정에서 검증된 8가지 운영 패턴 + 10건 실증 사례 + 1인/소규모/전사 비교표. [[CLAUDE|운영규칙]] 진화의 근거 자료.

## 운영 모델 비교 — 규모별 핵심 차이

LLM Wiki 패턴은 **1인 운영을 전제**로 설계됐다(Karpathy 본인 모델: 한쪽 Claude Code/Cursor + 한쪽 Obsidian, LLM 이 위키를 컴파일하고 본인은 읽기·질문만). 단일 의사결정자 → 권한 충돌·동시 편집·합의 비용 0.

5~10명 admin 모델로 확장하면 단순함이 깨진다. 한국 GeekNews 토론에서 반복 지적된 3가지 위험:
- **컨텍스트 한계로 기억 손실**: 200~300k 토큰 부근에서 LLM 이 위키를 일관되게 유지하지 못함
- **새로운 형태의 기술 부채**: admin 마다 ingest 스타일이 다르면 위키가 "여러 사람의 다른 사고 흐름"으로 갈라짐
- **사고 정리 가치 손실**: 문서 작성의 진가는 결과물이 아니라 사고 정리 과정인데, LLM 위임 시 깊이 사라짐

전사 단위(300명) 운영은 본 리서치에서 본격 사례 미발견. **본 사무소가 5~10 admin 모델 정착 시 국내 건축설계업계 첫 레퍼런스**가 될 가능성.

| 항목 | 1인 (현 kouwiki) | 5~10 admin (목표) | 전사 300명 |
|------|------------------|-------------------|------------|
| 운영 부담 | 낮음 | 중간 (admin 합의·편집 충돌 조율) | 높음 (부서별 ACL·온보딩) |
| 권한 모델 | 단일 owner | DokuWiki 식 reader/editor/admin 3-tier | + 부서별 ACL + 다우오피스 SSO |
| 드리프트 위험 | 낮음 | 중간 (스타일 표준화 필요) | 높음 (부서 간 용어 충돌) |
| 의사결정 비용 | 0 | 주1회 동기 회의 | governance board 필요 |
| LLM 컨텍스트 | 본인 vault만 | admin 분산 큐레이션으로 줄어듦 | 위키 분할 불가피 |
| 스키마 변경 | 본인 결정 | git PR + admin 2명 승인 | governance board 안건 |

## 8가지 운영 패턴 (실무 적용형)

### 1. Ingest 패턴 — Human-in-the-Loop "AI proposes, human approves"

완전 자동(/raw-inbox 자동 적재 → LLM 위키 컴파일)은 1인에서 잘 동작하지만 모델 신뢰도 문제로 잘못된 분류·중복·할루시네이션 누적. 완전 수동은 [[지식의_복리|복리]]의 LLM 위임 가치 포기. **하이브리드(AI proposes, admin approves)** 가 권장.

dsebastien.net Agentic KM 모델: AI 1분 heartbeat → vault 변경 감지 → 노트에 수정안 직접 제안 → 사용자 승인/거부 → 실행. 5~10 admin 모델에서 admin 인지 부담을 줄이는 거의 필수 패턴. 다우오피스 게시물·이메일로 propose 라우팅 가능.

### 2. 분류·태깅 — 신뢰도 + 민감도 필수

[[Graphify]] 의 EXTRACTED/INFERRED/AMBIGUOUS 3-tier 신뢰도 라벨 + LLM Wiki v2 의 자동 신뢰도 점수 + **민감도 라벨**(public/internal/project/confidential) 4-tier. 건축설계사무소는 발주처 정보·계약 자료가 위키에 흘러들면 즉시 보안 사고 → confidential 페이지는 frontmatter 에 `llm_access: deny` 같은 플래그 부여해 LLM 컨텍스트 차단.

### 3. 중복·통합 — 명시적 폐기(Supersession)

새 정보가 옛 정보를 대체하면 **삭제 금지, "superseded_by" 메타로 마크**. LLM Wiki v2 는 git 기반 감사 추적과 결합해 자동 망각 곡선보다 안정적이라 평가. 본 kouwiki 는 이미 [[건강검진]] 절차에서 "사용자 승인 후 정리" 원칙으로 일부 구현 — supersession 메타 추가가 다음 단계.

### 4. Query 패턴 — `[[위키링크]]` 인용 강제

답변에 항상 `[[위키링크]]` 로 근거 페이지 인용 — 단순 매너가 아니라 **드리프트 방지의 1차 방어선**. 인용이 깨지면 자동으로 lint 가 잡아냄. 본 [[CLAUDE|운영규칙]] 이 이미 명시. 추가로 claude-obsidian 의 **hot.md (hot cache) 패턴** — 세션 종료 시 컨텍스트 요약을 hot.md 에 갱신, 다음 세션 자동 로드 → 반복 질문 비용 절감. admin 모델에서는 단일 공유 hot.md 를 admin 회의 직후 갱신 (단독 갱신 금지).

### 5. Lint 주기 — 일/주/월 3계층

탐지 항목: 깨진 [[위키링크]]·고립 노드·중복/유사·스텁·모순·누락 교차참조·데이터 갭. 자동화 도구는 wiki-brain-skill, hellohejinyu/llm-wiki, [[Graphify]], claude-obsidian 의 `lint the wiki` 등.

5~10 admin 모델 권장 주기:
- **일별**: ingest 직후 자동 lint (해당 페이지·교차참조만)
- **주별**: 전체 lint, admin 회의에서 결과 리뷰, 다우오피스 그룹 게시물로 통보
- **월별**: 카테고리 재편·schema 갱신 검토

### 6. 드리프트 방지 — 3가지 컨텍스트 분리

DataHub "Continuous Context" 분석: 문서 품질 저하의 본질은 "문서가 묘사하는 세계가 변하기 때문". **사람은 패턴 매칭으로 보정하지만 AI 에이전트는 "doc 그대로" 동작 → stale 위키가 obvious failure 가 아니라 confidently wrong output 을 낳는다.** 가장 무서운 실패 모드.

| 컨텍스트 | 출처 | 갱신 패턴 |
|----------|------|-----------|
| **Declared** (사규·매뉴얼) | 사람 작성 | 정기 검증 (last_validated_at 메타) |
| **Derived** (ERP 스키마·릴리즈 노트) | 시스템 자동 계산 | 변경 시 자동 갱신 |
| **Observed** (어떤 페이지가 자주 참조되나) | 사용 패턴 추론 | 자동 수집 |

추가 규칙: **삭제 금지, 아카이브 이동** (`wiki/_archive/`), `superseded_by` 메타로 연결, `last_validated_at` 90일 초과 + 시점 의존 문장(가격·버전·법령) 있는 페이지 우선 검토.

### 7. 권한·ACL — DokuWiki 모델

본 리서치에서 발견한 가장 단순·검증된 ACL 패턴(DokuWiki GroupManager): reader / editor / group-admin 3-tier, namespace 분리. "ACL 셋업 후 group-admin 자율 운영, super-admin 개입 최소" — 5~10 admin 모델의 좋은 청사진.

본 사무소 적용:
- 본부별 NAS 분산 운영(70TB) 구조를 그대로 매핑: **본부 = namespace**
- 다우오피스 SSO 게이트 + git 백엔드(자동 감사 추적 — 모든 admin 액션이 commit log 에)

### 8. 메타 인사이트 — 결정화(Crystallization)

**LLM Wiki 의 진짜 가치는 "자료 안 읽고 답하기"가 아니라 "자료를 읽었기 때문에 보이는 cross-cutting 통찰을 별도 페이지로 결정화" 하는 것**. 본 리서치에서 가장 일관되게 강조된 메시지.

메타 페이지 설계 패턴:
- **Hub 페이지** — 한 도메인의 시작점 (본 위키의 [[index]])
- **Comparison 페이지** — 동일 문제의 여러 도구 비교 ([[ERP_벤더_비교]], [[통합형_vs_연동형]])
- **Lessons 페이지** — 회고. 시간 지나며 가치 압축
- **Map of Content (MOC)** — Obsidian 표준. 주제별 묶음 메타 페이지

LLM Wiki v2 의 결정화 워크플로: 완료된 작업을 "질문·발견·관련 엔티티·교훈"으로 추출 → 새 위키 페이지 생성. 본 kouwiki 의 [[정보_일원화_패턴]] / [[지식의_복리]] / [[통합형_vs_연동형]] 메타 페이지가 이 패턴의 1차 사례. **3건 이상 페이지에서 같은 개념이 등장하면 자동으로 메타 페이지 후보로 등재** 가 5~10 admin 모델의 추천 트리거.

[[Graphify]] 의 신(God) 노드·뜻밖의 연결이 결정화 후보를 발견하는 알고리즘 도구. 분기 1회 graphify 정기 실행 → admin 회의 어젠다 고정.

## 실증 사례 10건 (요지)

### Karpathy 본인 — 100 페이지 / 40만 단어
2026년 4월 gist 공개 후 16M views. 한쪽 Claude Code/Cursor + 한쪽 Obsidian. 본인이 직접 쓴 부분 없음. "Obsidian = IDE, LLM = programmer, wiki = codebase" 메타포. 한계: 1인 단위라 권한·동시편집·드리프트 governance 미검증.

### Abraham Song — wiki.ai-biz.app
[[LLM_Wiki_웹구현_사례]] 별도 페이지 참조. Hetzner CX23 + FastAPI + Vanilla JS + Caddy + bcrypt + IP 화이트리스트. 멀티세션 디버깅으로 안정화. 5~10 admin 모델에도 "위키(프론트) + 그래프(백엔드)" 분리 구조 유효성 시사.

### Balu Kosuri — 1주일 15-20 페이지
Cursor + Obsidian + 3개 프롬프트로 부트스트랩. 핵심 인사이트: **"지식베이스의 어려운 부분은 읽기·사고가 아니라 항상 bookkeeping(교차참조·용어 일관성·인덱스 갱신)이었다. LLM 이 그걸 가져가준다."**

### Benjamin (한국 개발자 블로그)
본인 블로그 글 수백 개를 Obsidian vault 로 이전. **vault 구조: Sources / Concepts / Projects / Maps / Comparisons 5개 디렉토리**. "schema 문서 품질이 모델 성능만큼 결과에 영향" — 본 [[CLAUDE|운영규칙]] 이 잘 갖춘 영역.

### GeekNews 한국 토론
- Codex / Gemini 파서 추가, 한글 검색 BM25 가드레일, GitHub 백업 자동화
- 우려: 컨텍스트 한계, 기술부채 누적, 사고 정리 가치 손실
- 본 위키 함의: 한글 BM25 가드레일·토크나이저(Kiwi) 별도 신경 필요 → [[한국어_LLM_스택]]

### claude-obsidian (AgriciDaniel)
명령 체계: `/wiki` 부트스트랩, `ingest [source]`, `what do you know about X?`, `/autoresearch [topic]`, `lint the wiki`. **hot.md hot cache 패턴** + autoresearch 루프 (search → fetch → synthesize → file).

### nvk/llm-wiki — 멀티 에이전트
`~/wiki/topics/<topic>` 단위 격리 vault. 명령: `/wiki:research --new-topic --deep --min-time 2h`, `/wiki:thesis "claim"`, `/wiki:audit`. 5/8/10 병렬 에이전트, "Retardmaxxing(act first, refine later)" 철학. 5~10 admin = 5~10 병렬 에이전트로 보면 메커니즘 유사.

### dsebastien.net — Agentic KM, AI heartbeat
AI 1분 heartbeat → vault 변경 감지 → 노트에 수정안 제안 → 승인/거부 → 실행. **"AI proposes before it executes. You maintain control."** 5~10 admin 모델 admin 인지 부담 줄이는 거의 필수.

### LLM Wiki v2 (rohitg00) — agentmemory 교훈
명시적 폐기(Supersession), 신뢰도 점수 + 모순 자동 감지, 인간 검증 게이트, 메시 동기화(병렬 세션의 관찰값 공유 위키로 병합), **결정화(Crystallization)** — 5~10 admin 모델 메타 페이지 생산 표준.

### DokuWiki GroupManager — 검증된 ACL
reader / editor / group-admin 3-tier, namespace 분리. ACL 셋업 후 자율 운영. 본 사무소 5~10 admin 청사진.

### 실패 사례·교훈
- **컨텍스트 폭발** (200~300k 토큰): 위키 분할 (nvk topics 격리, admin 별 namespace)
- **자동 ingest 신뢰도 문제**: HITL 필수
- **stale 위키 = confidently wrong**: lint 와 last_validated_at 안전망
- **schema 변경 비공식화**: git PR + admin 회의 통과 필수
- **그래프 백엔드 디버깅**: [[Graphify]] 통합 시 큐 hang·메모리 누수·타임아웃 ([[LLM_Wiki_웹구현_사례|Abraham Song 실측치]] 그대로)

## kouwiki 5~10 admin 확장 단계 로드맵

| Phase | 시점 | 작업 |
|-------|------|------|
| **0 (현재)** | M1~M2 | 1인 운영 안정화, schema 확정 — **현 위치** |
| **1** | M3~M5 | admin 2~3명 추가 (전산실 + 1개 본부 코디네이터). git PR 흐름·권한 모델 검증 |
| **2** | M6~M9 | admin 5~7명. 본부별 namespace, 다우오피스 SSO, 자동 lint 알림 |
| **3** | M10~M12 | admin 8~10명. 부서별 reader 권한 개방, 멀티모달 ingest, [[Graphify]] 분기 운영 |
| **4** | 12M+ | 전사 reader 개방 검토. governance board 설치 전제 |

## 본 사무소 적용 시 필수 운영 규칙 (체크리스트)

- [ ] **민감도 frontmatter** — `sensitivity: public/internal/project/confidential` 모든 페이지 의무
- [ ] **AI proposes, admin approves** — 자동 ingest 결과는 다우오피스 게시물로 propose, admin 1명 이상 승인 후 commit
- [ ] **신뢰도 게이트** — 자동 ingest 0.7 미만은 별도 큐 (사람 검토 강제)
- [ ] **민감 정보 자동 필터** — API 키·발주처명·개인정보 정규식 ingest 단계 차단
- [ ] **schema 변경 governance** — CLAUDE.md 변경은 git PR + admin 2명 승인 필수
- [ ] **삭제 금지, 아카이브 이동** — `wiki/_archive/` + `superseded_by` 메타
- [ ] **stale 우선순위** — `last_validated_at` 90일 초과 + 시점 의존 문장 우선
- [ ] **graphify 분기 정기 실행** — 신 노드·뜻밖의 연결 리포트 → admin 회의 어젠다 고정
- [ ] **3건 이상 동일 개념 → 메타 페이지 후보** — admin 1명 결정화 담당
- [ ] **공유 hot.md** — admin 회의 직후 갱신, 비동기 단독 갱신 금지

## 출처
- raw/2026-05-06_llm_wiki_operations_research.md (본 페이지 1차 소스)
- Karpathy LLM Wiki gist: <https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f>
- LLM Wiki v2 (rohitg00): <https://gist.github.com/rohitg00/2067ab416f7bbe447c1977edaaa681e2>
- Balu Kosuri: <https://medium.com/@k.balu124/i-used-karpathys-llm-wiki-to-build-a-knowledge-base-that-maintains-itself-with-ai-df968e4f5ea0>
- Benjamin: <https://benjamin0326.github.io/ai/2026/04/19/LLM_Wiki.html>
- GeekNews: <https://news.hada.io/topic?id=28208>
- claude-obsidian: <https://github.com/AgriciDaniel/claude-obsidian>
- nvk/llm-wiki: <https://github.com/nvk/llm-wiki>
- dsebastien.net Agentic KM: <https://www.dsebastien.net/agentic-knowledge-management-the-next-evolution-of-pkm/>
- DataHub Continuous Context: <https://datahub.com/blog/continuous-context/>
- DokuWiki GroupManager: <https://www.dokuwiki.org/plugin:groupmanager>
- 수집일: 2026-05-06

## 관련
- [[LLM_Wiki_개념]] — 본 페이지의 개념 모태 (3 레이어·3 모드)
- [[LLM_Wiki_웹구현_사례]] — Abraham Song 의 5~10 admin 모델 청사진
- [[지식의_복리]] — 본 패턴이 추구하는 메타 가치
- [[CLAUDE]] — 본 위키의 schema. 5~10 admin 확장 시 진화 대상
- [[건강검진]] — Lint 패턴의 본 위키 구현
- [[Graphify]] — 결정화·메타 인사이트 도구
- [[정보_일원화_패턴]] — 메타 페이지 결정화의 본 위키 사례
- [[통합형_vs_연동형]] — Comparison 페이지 패턴의 사례
- [[Wiki_Backed_Chatbot]] — 운영 패턴의 챗봇 통합 진화
- [[사내_챗봇_시스템]] — 5~10 admin 모델이 사용자 인터페이스를 가질 때의 시스템
- [[한국어_LLM_스택]] — 한국어 검색·임베딩 운영 인프라
- [[건축IT_사내개발_전략]] — 본 패턴의 사무소 적용 단계 로드맵
