# Graphify

임의의 폴더(코드·문서·논문·이미지·영상)를 → 의미·구조 추출 → 커뮤니티 클러스터링 → 인터랙티브 그래프 + 에이전트용 JSON + 자동 보고서로 만드는 **Python CLI + AI 코딩 어시스턴트 스킬**. README가 명시적으로 "Karpathy의 /raw 폴더 워크플로 기반"이라고 선언 — 본 kouwiki 와 정확히 같은 계보의 도구. [[LLM_Wiki_개념]] 의 자동화·시각화 보완재로 들어옴. Obsidian 플러그인 아님(자주 오해). 2026-05-05 현재 버전 0.7.5.

## kouwiki 와의 위치 — 보완재

본 위키는 사용자가 자료를 raw/ 에 넣으면 Claude 가 wiki/ 에 정리하고 [[교차참조]]를 **수동**으로 거는 구조. Graphify 는 같은 raw/wiki 구조 위에서 **자동으로** 의미 그래프를 만들어 의외의 연결을 후보로 제시한다.

| kouwiki (수동) | Graphify (자동) | 보완 관계 |
|----------------|----------------|-----------|
| Claude 가 [[교차참조]]를 본문 안에 직접 작성 | 모든 엣지 자동 추출, EXTRACTED/INFERRED/AMBIGUOUS 태깅 | 자동 후보 → 인간이 검토 → 위키 본문 [[링크]] 확정 |
| Obsidian 그래프뷰 (단일 vault 내 명시 링크만) | community detection (Leiden) — 클러스터 발견 | 같은 데이터의 두 시각, 클러스터를 [[index]] 카테고리로 흡수 가능 |
| [[건강검진]] 으로 깨진 링크·고립 노드 점검 | graph.json + audit trail로 정량 점검 | [[건강검진]] 의 자동화 도구 |
| [[지식의_복리]] — 한 번 정리한 위에 다음이 누적 | graph.json 영속 — 세션 간 그래프 재사용 | 같은 명제의 그래프 도메인 구현 |

→ **수동 위키 + 자동 그래프** 의 이중 구조. 인간 큐레이터(Claude)의 의도된 [[교차참조]]가 1차 진실이고, Graphify의 INFERRED 엣지는 후보 제안.

## 작동 모드 ↔ kouwiki 운영 모드 매핑

[[LLM_Wiki_개념]] 의 3 동작 모드(Ingest / Query / Lint)와 거의 1:1 대응:

| kouwiki 모드 | Graphify 명령 | 비고 |
|--------------|---------------|------|
| Ingest (자료 넣기) | `/graphify add <url>` 또는 `/graphify <path> --update` | 신규 자료 → 그래프 갱신 |
| Query (질문하기) | `/graphify query "<질문>"`, `/graphify path "A" "B"`, `/graphify explain "X"` | BFS/DFS 순회로 토큰 절감 답변 |
| Lint (건강검진) | `/graphify <path> --cluster-only`, `graphify benchmark` | 그래프 무결성·구조 점검 |

## 설치 (Windows + Claude Code, 본 위키 환경)

### 1단계 — 패키지 설치

```powershell
python -m pip install --user graphifyy
```

**주의**: PyPI 패키지명은 `graphifyy` (y 두 개). CLI 명령은 `graphify` (y 한 개). PyPI에 동명 패키지가 이미 있어 더블-y로 등록된 것이며 공식 README가 명시. 다른 `graphify*` 패키지는 무관.

설치되는 의존성: tree-sitter (26개 언어 파서), networkx, datasketch, numpy, scipy, rapidfuzz. 30개+ 패키지지만 모두 표준.

`graphify.exe` 위치: `C:\Users\<User>\AppData\Roaming\Python\Python312\Scripts\`. PATH에 없으면 `python -m graphify` 로 호출 (본 위키 환경의 표준 호출법).

### 2단계 — Claude Code 스킬 설치

```powershell
python -m graphify install --platform windows
```

산출:
- `C:\Users\<User>\.claude\skills\graphify\SKILL.md` — 스킬 본체
- `C:\Users\<User>\.claude\CLAUDE.md` — 사용자 레벨 트리거 등록 (kouwiki vault `CLAUDE.md`와 분리되어 충돌 없음)

설치 후 **Claude Code 새 세션 시작** → `/graphify` 슬래시 커맨드 사용 가능.

### 3단계 (선택) — 헤드리스 사용을 원하면

`graphify extract` 는 LLM 백엔드(claude/kimi)를 요구. `ANTHROPIC_API_KEY` 또는 `MOONSHOT_API_KEY` 환경변수 설정 필요. 본 위키는 Claude Code 안에서 작동하므로 키 없이 슬래시 커맨드만으로 충분.

## 사용법 — 본 위키에 적용

### 핵심 슬래시 커맨드 (Claude Code 안)

```
/graphify ./wiki                                    # wiki/ 폴더 전체 그래프
/graphify ./wiki --obsidian --obsidian-dir .        # 결과를 본 vault에 합류
/graphify ./wiki --update                           # 변경 페이지만 증분
/graphify ./wiki --cluster-only                     # 재클러스터링만 (값싸다)
/graphify ./wiki --no-viz                           # 보고서·JSON만, HTML 생략

/graphify query "ERP 통합형과 BIM 단일정보원의 공통 패턴"
/graphify path "더존_아마란스10" "정림건축_혁신본부"
/graphify explain "정보_일원화_패턴"
```

### 권장 도입 순서

1. **첫 빌드** — `/graphify ./wiki` 한 번 돌려 `graphify-out/` 생성, `graph.html` 시각화 확인.
2. **인사이트 검증** — INFERRED 엣지 중 본 위키의 [[정보_일원화_패턴]] / [[통합형_vs_연동형]] / [[지식의_복리]] 와 일치하는지 본다. 일치하면 위키 통찰이 외부 도구로도 재현되는 강한 신호.
3. **새 후보 검토** — INFERRED 엣지 중 위키에 없는 연결을 발견하면 후보로 채택, [[건강검진]] 절차로 본문 [[교차참조]] 추가 여부 사용자 승인.
4. **벤치마크** — `graphify benchmark` 로 naive 풀-코퍼스 대비 토큰 절감 측정.
5. **선택적 git 후크** — 본 vault는 GitHub 원격 보유([[Claude_Code]] 환경 메모 참조). `graphify hook install` 로 커밋 시 자동 재빌드 가능.

### 산출 파일

`graphify-out/` 디렉토리에:
- `graph.html` — 인터랙티브 시각화 (클릭·필터·검색)
- `GRAPH_REPORT.md` — 핵심 개념·연결·추천 질문 자동 생성 (이걸 [[index]] 보강에 활용 가능)
- `graph.json` — 그래프 영속 자료 (재질의용)
- `manifest.json` / `cost.json` — gitignore 권장

`.gitignore` 추가 권장:

```
graphify-out/manifest.json
graphify-out/cost.json
```

(`graph.json` / `graph.html` / `GRAPH_REPORT.md` 는 vault 자산으로 커밋해도 됨.)

### 외부 자료 자동 ingest

```
/graphify add https://blog.naver.com/oh_seeing/224260358976 --author "오시잉" --contributor "kouwiki"
```

URL 본문을 가져와 `./raw` 에 저장하고 그래프를 갱신. 본 위키의 "raw에 자료 넣기 → wiki 정리" 패턴을 한 줄 명령으로 압축. 단 Claude 의 위키 정리 단계는 별도(저자 [[CLAUDE|운영규칙]]에 따른 사용자 승인이 분리된 것이 의도된 설계).

## 엣지 감사(audit) — 위키와의 정직성

Graphify가 만든 모든 엣지에 출처 태그:

| 태그 | 의미 | 위키 매핑 |
|------|------|-----------|
| `EXTRACTED` | 자료에서 직접 발견 (인용·링크·import) | 본문 안 명시 [[교차참조]] 후보 |
| `INFERRED` | 의미 유사도로 추론 | 검토 대상. 채택 시 [[관련]] 섹션에 |
| `AMBIGUOUS` | 후보지만 불확실 | 무시 또는 추가 자료 ingest 후 재평가 |

→ "어디까지 사실, 어디부터 모델 추측"을 분리. [[CLAUDE|운영규칙]] 의 "추측 금지" 원칙과 같은 구조. [[건강검진]] 의 정량 도구로 활용.

## 의외의 연결 — 같은 계보·같은 패턴

### Karpathy 직계 — [[LLM_Wiki_개념]] 의 자동화 도구
README가 직접 "Andrej Karpathy의 /raw 폴더 워크플로 기반"임을 선언. 본 위키와 같은 원전을 공유하면서 시각화·자동 발견 부분을 채우는 보완재. 본 위키가 인간(Claude) 큐레이션 위주라면 Graphify는 알고리즘 클러스터링 위주.

### MCP·플러그인 생태계 — [[통합형_vs_연동형|연동형]] 패턴의 또 다른 표상
Graphify는 18개+ AI 어시스턴트(Claude / Cursor / Codex / Gemini / Copilot / Antigravity / OpenCode / Aider / Trae / Hermes / Kiro / Pi 등) 각각에 별도 install 서브커맨드 제공 + `--mcp` 플래그로 MCP stdio 서버 모드. **하나의 코어에 다수 인터페이스를 조립**하는 구조 = [[Claude_Code]] + MCP 6개와 같은 연동형 아키텍처. [[더존_아마란스10|아마란스10 ONE AI]] 같은 통합형 대안의 반대편.

### 토큰 복리 — [[지식의_복리]] 의 정량적 구현
[Lucas Rosati 사례](https://github.com/lucasrosati/claude-code-memory-setup)에 따르면 126개 TS 파일 프로젝트에서 graph.json 영속화로 **세션당 71.5x, 쿼리당 499x 토큰 절감**. [[지식의_복리]] 의 "한 번 컴파일한 지식 위에 다음 작업"이라는 명제가 토큰 단위로 측정 가능해진 것. 본 위키의 [[CLAUDE|운영규칙]] 이 같은 가치를 인간이 읽을 마크다운으로, Graphify 가 같은 가치를 LLM 이 읽을 그래프로 구현.

### Foster Hermes ↔ Graphify graph.json
[[해외_AEC_사내개발|Foster + Partners 의 Hermes]] 가 클라우드 단일 데이터원으로 BIM 데이터를 일원화한 것과, Graphify graph.json 이 코드·문서를 단일 그래프로 일원화한 것은 **같은 [[정보_일원화_패턴]]** 의 다른 도메인 구현.

### 자동 [[건강검진]] 도구
본 위키의 [[건강검진]] 절차(깨진 링크·중복·고립 노드·스텁·미인덱스) 중 그래프 구조 부분(고립 노드·중복 후보·미연결 카테고리)은 `graphify benchmark` 와 `graph.json` 분석으로 자동화 가능. Lint 모드의 알고리즘 보강.

## 한계 / 주의

- **Claude Code 외부 헤드리스는 LLM 키 필요**: `graphify extract` 는 ANTHROPIC_API_KEY 또는 MOONSHOT_API_KEY 요구. `graphify update` 는 코드 파일만 다루므로 마크다운 위주 vault 에 무용.
- **INFERRED 엣지 과신 금물**: AMBIGUOUS 태깅된 엣지는 단지 후보. [[CLAUDE|운영규칙]] 의 "추측 금지" 원칙대로 사용자 승인 후 [[교차참조]] 추가.
- **그래프뷰 중복**: Obsidian 그래프뷰와 graph.html 둘 다 시각화 도구. 의미가 약간 다름 — Obsidian 은 명시 [[링크]]만, Graphify 는 의미 유사도까지 포함. 둘 다 두는 것이 권장 (서로 다른 진실).
- **PyPI 명명**: `graphifyy` 더블-y. `graphify` (싱글-y)는 무관 패키지이므로 설치 시 주의.
- **vault CLAUDE.md 충돌 위험 회피**: 본 위키 사용 시 `graphify claude install` (프로젝트 레벨 — vault `CLAUDE.md` 수정) 대신 `graphify install --platform windows` (사용자 레벨) 사용. 본 vault `CLAUDE.md` 의 운영 규칙과 분리.

## 출처
- raw/2026-05-05_graphify_install_guide.md — 본 페이지의 1차 소스 정리
- 공식 GitHub: <https://github.com/safishamsi/graphify>
- 공식 사이트: <https://graphify.net>
- PyPI: <https://pypi.org/project/graphifyy/>
- Claude Code + Obsidian + Graphify 통합 사례: <https://github.com/lucasrosati/claude-code-memory-setup>
- 본 위키 환경 설치·검증 (2026-05-05, kouwiki vault, Python 3.12.10, graphifyy 0.7.5)
- 수집일: 2026-05-05

## 관련
- [[LLM_Wiki_개념]] — 같은 Karpathy 계보. Graphify 는 그 자동화·시각화 보완재
- [[지식의_복리]] — Graphify graph.json 의 토큰 절감 (499x) 이 같은 명제의 정량 측정
- [[Claude_Code]] — Graphify 스킬이 작동하는 본 위키의 운영 환경
- [[CLAUDE]] — Graphify INFERRED 엣지 채택 시 따를 본 위키 운영 규칙
- [[건강검진]] — Graphify 가 자동화 보강할 위키 무결성 점검 절차
- [[정보_일원화_패턴]] — graph.json 단일 그래프 = 같은 패턴의 알고리즘 도메인
- [[통합형_vs_연동형]] — Graphify 의 18개+ 플랫폼 어댑터 = 연동형 아키텍처
- [[바이브_코딩_도구]] — Graphify 가 통합되는 코딩 도구 6강 모두에 어댑터 제공
- [[해외_AEC_사내개발]] — Foster Hermes 단일 데이터원 = graph.json 단일 그래프 동형
