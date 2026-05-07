# LLM Wiki 웹 구현 사례 — Abraham Song (wiki.ai-biz.app)

[[LLM_Wiki_개념]] + [[Graphify]] 를 **셀프호스트 웹 앱**으로 직접 구현한 실사례. 원작자 Abraham Song (前 Visa 엔지니어링 그룹 지식관리자, 現 Seattle Basecamp Inc. 대표) 이 2026년경 Hetzner CX23 리눅스 서버 위에 FastAPI + Vanilla JS SPA + D3.js + Graphify 를 조립해 [wiki.ai-biz.app](https://wiki.ai-biz.app) 으로 운영. 본 kouwiki 가 옵시디언 vault + [[Claude_Code]] 로컬 구조라면, 이쪽은 같은 패턴의 **웹 SaaS 대체 구현**. [[지식의_복리]] / [[정보_일원화_패턴]] / [[통합형_vs_연동형]] 의 실증 데이터.

## 원작자 동기 — Obsidian Sync 회피

저자가 본 글에서 명시한 동기:

1. Visa 시절 회사·개인 양쪽으로 지식관리(KM) 도구를 다양하게 써봤지만, 여전히 지식이 Google Drive · Gmail · OneNote · Apple Notes · Samsung Notes · Slack · Medium · Brunch 등에 **분산**되어 있었음 → 같은 정보 중복, 노후화, 소음 매몰 (= 본 위키의 [[정보_일원화_패턴]] 도입 동기와 동일)
2. Karpathy LLM Wiki 발견 → 옵시디언으로 로컬 구현 → 작동 좋았으나 **노트북 안에만 갇힘**
3. 가장 쉬운 해법은 Obsidian Sync 월 5달러. 그러나 고정수입 불안정 상태에서 매월 구독료가 부담 → **셀프호스트로 회피**
4. 1차 구현(텍스트 DB 수준)은 작동했지만 시각적 연결 없음 → Graphify 통합 결심

→ 즉 [[LLM_Wiki_개념]] 의 옵시디언 단점(로컬 락인) + 시각화 부재(텍스트만) 두 가지를 **조합형 웹앱**으로 동시 해결한 사례. 본 kouwiki 와 다른 길.

## 시스템 아키텍처 (저자 자술)

| 레이어 | 구성 |
|--------|------|
| **호스팅** | Hetzner CX23 리눅스 VPS (월 약 5달러 — Obsidian Sync 와 같은 가격대) |
| **백엔드** | FastAPI (Python) — Extractor → Graphify 파이프라인 → LLM 종합 |
| **프론트엔드** | Vanilla JS SPA (D3.js 포스 시뮬레이션 그래프 뷰) |
| **데이터** | `raw/` (원본) · `wiki/` (마크다운 vault) · `graphify-out/graph.json` (지식 그래프) |
| **에이전트 인터페이스** | 텔레그램 봇 + Hermes 에이전트 (Anthropic API) |
| **인증/보안** | Caddy TLS 1.3 + bcrypt HTTP Basic + 관리자 IP 제한(포트 8080) |
| **DNS** | wiki.ai-biz.app |

→ 본 kouwiki 의 [[Claude_Code]] + Obsidian + MCP 6개 구조와 같은 [[통합형_vs_연동형|연동형]] 패턴, 다만 인터페이스 레이어가 **클라이언트 IDE → 웹 SPA + Telegram** 으로 치환됨.

## 텔레그램 → AI 에이전트 → 웹 위키 흐름

저자의 운영 패턴:

1. 텔레그램으로 자료(URL · GitHub repo · UI 스크린샷 · 유튜브 영상) 를 Hermes 에이전트에 전송
2. Hermes 에이전트가 백엔드 호출 → Extractor 가 자료 종류별 파싱 (코드: Tree-sitter AST · 영상: faster-whisper 로컬 전사 · 문서/이미지: LLM 추출)
3. Graphify 파이프라인이 NetworkX 그래프 갱신 + Leiden 클러스터링
4. LLM 이 마크다운 위키 페이지 종합 → `wiki/` 에 저장 + 자동 [[교차참조]] 주입
5. 사용자는 wiki.ai-biz.app 웹 SPA 에서 페이지 또는 그래프 탭으로 탐색

**페이지 하단 자동 주입 형식 (원문 인용)**:
```
## 관련 항목 (Graphify 추출)
- [[UserManager Class]] — `EXTRACTED` — imports_from
- [[Auth Best Practices]] — `INFERRED` (0.87) — semantically_similar_to
- 속한 커뮤니티: 인증 클러스터 (Cluster #3)
```

→ 본 kouwiki 의 ## 관련 섹션이 인간(Claude) 큐레이션이라면, 이쪽은 **Graphify 가 자동 주입**하고 신뢰도 점수(EXTRACTED/INFERRED/AMBIGUOUS) 로 출처를 명시. [[Graphify]] 의 엣지 감사 시스템이 위키 페이지 표면까지 노출된 형태.

## 통합 작업의 현실 — "저항하는 통합"

저자가 가장 중요하게 다룬 부분. graphifyy 패키지 설치 + 서비스 래퍼 + 재빌드 트리거는 **개념상 단순**하나 실제로는:

- 첫 몇 회 실행이 멈춤 (작업 큐 running 상태로 무한 대기)
- 그래프 JSON 미생성 / 워커 메모리 점유만
- 망가진 마크다운 입력에 Graphify 가 체함
- 타임아웃 설정 부조화
- 게이트웨이 재시작 필요

저자의 해법은 **터미널 직접 진입** — 큐 파일 tail · 워커 상태 확인 · 에러 트레이스 grep · 텔레그램으로 로그 조각 보내며 에이전트와 함께 디버깅.

**최종 안착한 타임아웃 (시사점 — 실측치)**:

| 작업 | 타임아웃 |
|------|---------|
| 그래프 쿼리 (`/graphify query`) | 10초 |
| 경로 탐색 (`/graphify path`) | 30초 |
| 전체 재빌드 | 300초 |

→ 본 위키에서 [[Graphify]] 를 자동화/CI 워크플로에 거는 경우(예: `graphify hook install` 후 git post-commit 재빌드) 동일 수준의 타임아웃 분리가 필요하다는 실증 데이터. [[자동화_시나리오]] 에 적용 가능.

## "두 번째 의견" — 사서 비유

저자가 발견한 핵심 가치:

> "OAuth2 모듈과 PaymentGateway 모듈이 공유 유틸리티 함수 하나를 통해 긴밀하게 엮여 있다는 걸 Graphify 가 발견했다. 아무도 눈치채지 못했던 연결이었다."

이 발견은 **저자가 수동으로 위키링크를 걸어놓은 적 없음**. 코드 안에 구조적으로 존재하는 연결을 텍스트로는 볼 수 없었을 뿐. Graphify 가 그 다리를 찾아냄.

저자의 비유:
> "대부분의 AI 기반 지식 도구는 LLM 을 검색 엔진처럼 다룬다. 이 시스템은 LLM 을 **사서(librarian)** 처럼 다룬다. 사서는 당신이 물어볼 때까지 기다리지 않는다 — 스스로 정리하고, 연결하고, 당신이 놓친 패턴을 알아챈다."

→ [[지식의_복리]] 의 "컴파일된 지식" 비유와 짝을 이루는 메타. 컴파일된 지식이 **사용자 관점**(매번 재해석 회피) 의 비유라면, 사서는 **시스템 관점**(능동적 정리·발견) 의 비유.

## 본 kouwiki 와의 비교

| 항목 | 본 kouwiki | Abraham Song wiki.ai-biz.app |
|------|-----------|------------------------------|
| **위치** | 로컬 (Windows + Obsidian vault) | 셀프호스트 웹앱 (Hetzner VPS) |
| **AI 인터페이스** | [[Claude_Code]] (VSCode 확장) | 텔레그램 봇 + Hermes 에이전트 (Anthropic API 직접) |
| **그래프 시각화** | Obsidian 그래프뷰 + [[Graphify]] graph.html | D3.js 포스 시뮬레이션 (자체 구현) |
| **자동 [[교차참조]] 주입** | 인간(Claude) 수동 | Graphify 가 페이지 하단에 자동 주입 (신뢰도 라벨 포함) |
| **접근** | 본인 노트북에서만 | 어느 기기든 브라우저 |
| **인증** | OS 로그인 | bcrypt HTTP Basic + IP 제한 |
| **운영 비용** | 0원 (Claude Pro 구독은 별도 가치) | 약 월 5달러 (VPS) |
| **공유 가능성** | 본인만 (sync 없으면 수동 복사) | URL 공유로 가능 (인증 우회 시) |
| **유지보수 부담** | Obsidian/Claude Code 자동 업데이트 | FastAPI · D3 · Graphify 통합 디버깅 본인 책임 |

→ **같은 [[정보_일원화_패턴]] 의 두 구현**. 본 위키가 [[통합형_vs_연동형|연동형]] + 로컬 우위, 저자 구현이 연동형 + 웹 우위. 의사결정 분기는 "공유 필요성 vs 유지보수 부담".

## 시사점 (전산실 총괄 관점)

[[건축IT_사내개발_전략]] 과 직결되는 데이터:

1. **사내 위키 웹 구축은 약 월 5달러 + 1인 한 달 작업**으로 가능 — 외부 SaaS 의존 회피 가능. 단 통합 작업의 너저분함(타임아웃·큐 디버깅)을 받아들일 인력 필요
2. **Telegram + 백엔드 분리** 패턴은 사내 메신저(다우오피스 메신저 / Slack) + 사내 위키 백엔드로 그대로 치환 가능 — 직원이 메신저로 자료 던지면 자동 ingest 흐름. [[자동화_시나리오]] 의 새 시나리오 후보
3. **300초 전체 재빌드 / 10초 쿼리 / 30초 경로** 타임아웃 분리는 사내 워크로드 사이징의 출발점 (300명 규모 시 vault 크기 고려해 비례 산정 필요)
4. **저자도 단순 통합이 아니라 "저항하는 통합"이라 명시** — [[자동화_시나리오]] 의 "단계적 도입" 주장과 동일 교훈. 한 번에 풀시스템 가지 말고 가치 단위로 분할
5. **bcrypt + IP 제한 + Caddy** 의 "적정 보안" 수준은 사내 망 한정 PoC 에 충분. 외부 노출 시 다층 추가 필요 ([[ERP_RFP_체크리스트]] 의 보안 항목 참고)

## 한계 / 본 위키와의 의도된 차이

저자 구현의 트레이드오프:
- **유지보수 부담**: graphifyy 버전 업그레이드마다 통합 깨질 위험. 본 위키는 [[Claude_Code]] 가 알아서 처리
- **세션 관리 미구현**: HTTP Basic + bcrypt 만. 다중 사용자/조직 단위 운영 어려움
- **Obsidian 호환 vault 라고 명시했지만 실제 동기화 미증명**: 옵시디언 vault 도 같은 디렉토리를 가리키게 설정하면 가능하나 동시 편집 시 충돌
- **Telegram 의존**: 메신저 정책/저장 정책에 영향. 사내 컴플라이언스 고려 필요 (사내 환경에선 다우오피스 메신저로 치환)

본 kouwiki 는 "1인 사용 + 본인 노트북 + 옵시디언 + Claude Code" 의 가장 단순한 구성을 의도적으로 채택. 저자 구현은 **공유 가능성**을 위해 복잡도를 받아들인 다음 단계. 향후 본 위키가 사내 공유 단계로 진화한다면 저자 구현이 직접적 참고자료.

## 용어 주의 — Hermes 동명이인

저자가 사용하는 "Hermes 에이전트"는 텔레그램 연동용 일반 명칭의 Anthropic API 에이전트로 보임. **[[해외_AEC_사내개발|Foster + Partners 의 Hermes (BIM 단일 데이터원)]] 와는 무관**. 이름 우연 일치 — 본 위키 내 검색 시 혼동 주의.

다만 "Hermes" 라는 이름이 양쪽에서 같이 쓰인 것은 그리스 신화 전령신의 이미지가 **자료 전달자/중개자** 역할에 자연스럽게 매핑되기 때문으로 보임. [[정보_일원화_패턴]] 의 또 다른 약한 동형성 단서.

## 출처
- raw/LLM Wiki + Graphify를 직접 만들다.md (Brunch 매거진 "큰 변화가 오고 있다", Abraham Song)
- 원문: <https://brunch.co.kr/@abrahamsong/164> (추정 — raw 본문 출처 표기)
- 저자: Abraham Song (Visa 엔지니어링 그룹 지식관리자 출신, Seattle Basecamp Inc. 대표)
- 운영 사이트: <https://wiki.ai-biz.app>
- 인접 글: "LLM Wiki + Hermes Agents" (이전글), "Anthropic or OpenRouter API" (다음글)
- 수집일: 2026-05-06

## 관련
- [[LLM_Wiki_개념]] — 본 사례가 구현하는 원전 패턴. 저자가 Karpathy gist 를 텔레그램으로 에이전트에게 던진 것이 출발점
- [[Graphify]] — 본 사례가 채택한 그래프 엔진. 통합 디버깅 경험이 본 위키 도입 시 참고 데이터
- [[지식의_복리]] — "사서" 비유가 같은 명제의 시스템 관점 표현
- [[정보_일원화_패턴]] — 본 사례 저자가 명시한 도입 동기 (분산된 KM 도구 통합)
- [[통합형_vs_연동형]] — 본 사례는 연동형의 또 다른 구현 (FastAPI + Graphify + 텔레그램 + LLM API)
- [[자동화_시나리오]] — 메신저 연동 ingest 흐름 + 타임아웃 분리는 사내 자동화 시 직접 참고
- [[Claude_Code]] — 본 kouwiki 의 IDE 인터페이스. 저자 구현의 Telegram + Hermes 와 대비되는 선택지
- [[건축IT_사내개발_전략]] — 사내 위키 웹 구축의 인력·비용 데이터 (월 5달러 VPS + 1인 통합 작업)
- [[해외_AEC_사내개발]] — Foster Hermes 와 이름 동일 (서로 무관, 혼동 주의)
