# Claude Code

Anthropic 의 LLM 코딩 에이전트. 본 kouwiki 의 운영 환경 그 자체이며, [[LLM_Wiki_개념]] + [[통합형_vs_연동형|연동형]] 패턴의 살아있는 사례. [[바이브_코딩_도구]] 6강 중 하나.

## 본 위키 환경
- **VSCode 확장** 으로 실행 (Windows 11)
- 모델: Claude Opus 4.7 (1M context)
- 운영 규칙: [[CLAUDE]]
- 보관함: `c:\Users\user\Desktop\kouwiki\kouwiki\` (Obsidian)
- 원격 저장소: <https://github.com/kouros7376/LLMwiki>

## 핵심 기능

### 1. 큰 컨텍스트 윈도우
- Opus 4.7: **1M 토큰** (책 한 권 분량)
- 본 위키 전체(현재 ~30 페이지) + raw 자료 + 운영 규칙을 동시에 메모리 안에 둘 수 있음
- → [[지식의_복리]] 의 "한 번 컴파일된 지식 위에서 다음 작업" 을 1M 까지 지원

### 2. MCP (Model Context Protocol) 생태계
본 위키에 연결된 6개 MCP 서버:

| 서버 | 역할 |
|------|------|
| **fetch** | 외부 URL 본문 가져오기 (raw 자료 수집 자동화) |
| **filesystem** | 허용 디렉토리 안 파일 작업 (Read/Write 와 별도) |
| **puppeteer** | 헤드리스 브라우저 제어 (스크린샷, 동적 페이지) |
| **postgres** | DB 직접 쿼리 (현재 자리표시자, 실 DB 정보로 교체 시 활성) |
| **context7** | 라이브러리/프레임워크 최신 공식 문서 |
| **memory** | 지식 그래프 메모리 (대화 간 영속) |

→ 이 구조 자체가 [[통합형_vs_연동형]] 의 **연동형** 표상. ChatGPT Workspace 가 통합형이라면, Claude Code + MCP 는 사내 도구·외부 도구를 표준 프로토콜(MCP)로 조립하는 연동형.

### 3. 자연어 워크플로
- "raw에 자료 추가해줘" → 검색·요약·저장 자동
- "위키에 정리해줘" → ingest + 교차참조 + index 갱신
- "건강검진 해줘" → [[건강검진]] 절차 실행
- 별도 명령어 학습 불필요 — [[CLAUDE|운영규칙]] 만 정의되면 됨

### 4. 슬래시 스킬·플러그인
- `/init`, `/review`, `/security-review` 등 내장
- 사용자 정의 스킬 추가 가능
- `update-config` 스킬로 권한·환경변수 등 조정

## 가격
- Pro: $17/월
- Max: $100/월~
- 1M context Opus 4.7 은 Max 등급에서 본격 활용

## 강점·약점

| 강점 | 약점 |
|------|------|
| 1M 컨텍스트 — 본 위키 전체 동시 분석 | GUI IDE 자체 부재 (확장으로 대체) |
| MCP 생태계 — 사내 시스템 연동 자유 | 실시간 인라인 편집은 Cursor 대비 약함 |
| 자연어 워크플로 — 운영 규칙만 있으면 즉시 동작 | 학습 곡선 (운영 규칙 설계 필요) |
| 슬래시 스킬·플러그인 확장 | |

## 다른 도구와의 비교
→ 자세한 6강 비교는 [[바이브_코딩_도구]]

| 비교 | Claude Code 우위 | 대안 우위 |
|------|-----------------|-----------|
| vs Cursor | 긴 컨텍스트 + MCP | 인라인 편집 + GUI IDE 완성도 |
| vs Copilot | 자연어 워크플로 + MCP | 엔터프라이즈 보안 검증 + 보편성 |
| vs Windsurf | MCP 생태계 + 가격 | 협업 기능 |

## 본 위키와의 핵심 관계

본 kouwiki 는 Claude Code 의 능력을 검증하는 **라이브 사례**:
- [[LLM_Wiki_개념]] 의 Schema/Ingest/Query/Lint 모드 구현 = Claude Code + MCP 조합으로 자연어 한 줄로 실행
- [[정보_일원화_패턴]] 의 "단일 운영 규칙 하의 위키" = [[CLAUDE]] 1개 파일에 운영 규칙 일원화
- [[통합형_vs_연동형|연동형]] 의 가치 = MCP 6개 + Obsidian + Git 의 조립으로 락인 회피

## 의외의 연결

### Claude Code + MCP ↔ [[더존_아마란스10|아마란스10]] 의 ONE AI
같은 가치(자료 정리, 데이터 분석, 자동 검증)를 두 가지 다른 형태로 구현:
- **ONE AI**: 벤더 통합형 (한 플랫폼에 다 내장, 락인 + 일관 UX)
- **Claude Code + MCP**: 사용자 연동형 (도구 조립, 자유도 + 진화 속도)

→ 어느 쪽이 옳다 아니라 [[통합형_vs_연동형]] 의 분기 1차 결정 사항.

### Claude Code 의 자연어 워크플로 ↔ 정림 BIM 규모검토 Package 의 단일 정보원
다른 도메인이지만 **"수동 반복 → 자동 일원화"** 라는 같은 패턴 ([[정보_일원화_패턴]]). 정림이 Revit + Dynamo 로 구현했다면, 본 위키는 Claude Code + MCP 로 구현. 사용 도구만 다름.

→ 자세한 정림 사례는 [[정림건축_혁신본부]]

## 출처
- raw/2026-05-05_vibe_coding_tools.md
- 본 위키 자체의 운영 경험
- Anthropic 공식: <https://www.anthropic.com/claude-code>
- 수집일: 2026-05-05

## 관련
- [[CLAUDE]] — 본 위키의 Schema (Claude Code 가 따르는 규칙)
- [[바이브_코딩_도구]] — 6강 비교 컨텍스트
- [[LLM_Wiki_개념]] — Claude Code 가 구현하는 패턴
- [[지식의_복리]] — Claude Code + 위키의 가치 명제
- [[통합형_vs_연동형]] — Claude Code + MCP 의 위치
- [[더존_아마란스10]] — 같은 가치의 통합형 대안 (ONE AI)
- [[정림건축_혁신본부]] — 같은 패턴의 BIM 도메인 구현
