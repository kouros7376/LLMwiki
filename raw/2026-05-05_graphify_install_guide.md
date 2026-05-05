# Graphify 설치·사용 가이드 자료 정리 (2026-05-05)

수집일: 2026-05-05
출처:
- 공식 GitHub: <https://github.com/safishamsi/graphify>
- 공식 사이트: <https://graphify.net>
- PyPI: <https://pypi.org/project/graphifyy/> (패키지명 `graphifyy`, 더블-y 의도)
- Claude Code + Obsidian + Graphify 통합 사례: <https://github.com/lucasrosati/claude-code-memory-setup>
- 커뮤니티 글: <https://www.skool.com/cc-strategic-ai/graphify-claude-code-obsidian-new-meta>

---

## 1. Graphify 정체성

- 형식: **Python CLI + AI 코딩 어시스턴트용 스킬**. Obsidian 플러그인 아님.
- 한 줄 정의: 임의 폴더(코드 / 문서 / 논문 / 이미지 / 영상)를 → 의미·구조 추출 → 커뮤니티 클러스터링 → 3종 산출물(graph.html · graph.json · GRAPH_REPORT.md) 생성.
- 추출 엔진: Tree-sitter(코드 26개 언어 AST) + LLM(문서/마크다운/PDF) + faster-whisper(영상/오디오 로컬 전사) + NetworkX/Leiden(클러스터링).
- 명시적 계보: README가 "Andrej Karpathy의 /raw 폴더 워크플로 기반"이라고 직접 선언. LLM Wiki 개념과 동일 계보.

## 2. PyPI 명명 주의

- 패키지명: `graphifyy` (y 두 개). CLI 명령은 `graphify` (y 한 개).
- 이유: PyPI에 동명의 무관 패키지가 이미 존재해서 더블-y로 등록.
- 공식 문서 명시: "The PyPI package is `graphifyy` (double-y). Other `graphify*` packages on PyPI are not affiliated."
- 보안 검증: PyPI 메타데이터 homepage 링크가 정확히 `github.com/safishamsi/graphify`로 매핑됨 (2026-05-05 0.7.5 릴리스, MIT, 메인테이너 captainturbo).

## 3. 설치 (Windows 11)

### 전제

- Python 3.10+ (kouwiki 환경: 3.12.10 확인됨)
- `pip` 또는 `uv` 또는 `pipx` 중 하나

### 설치 명령

```powershell
# pip 사용 (kouwiki에서 사용한 방법)
python -m pip install --user graphifyy

# 또는 uv (가장 깔끔)
uv tool install graphifyy

# 또는 pipx
pipx install graphifyy
```

설치 후 `graphify.exe`는 `C:\Users\<User>\AppData\Roaming\Python\Python312\Scripts\` 에 들어감. 이 경로가 PATH에 없으면 `python -m graphify` 로 호출.

### Claude Code 스킬 설치

```powershell
# Windows + Claude Code 글로벌 스킬 설치
python -m graphify install --platform windows
```

설치 결과:
- `C:\Users\<User>\.claude\skills\graphify\SKILL.md` 생성 — 스킬 본체
- `C:\Users\<User>\.claude\CLAUDE.md` 생성/갱신 — 사용자 레벨 CLAUDE.md에 `/graphify` 트리거 등록 (kouwiki vault의 `CLAUDE.md`와 분리)

이후 새로 시작한 Claude Code 세션에서 `/graphify` 슬래시 커맨드 사용 가능.

### 다른 플랫폼 통합 (참고)

| 플랫폼 | 설치 명령 |
|--------|-----------|
| Cursor | `graphify cursor install` (.cursor/rules/graphify.mdc) |
| Codex | `graphify codex install` (AGENTS.md) |
| GitHub Copilot CLI | `graphify copilot install` (~/.copilot/skills) |
| VS Code Copilot Chat | `graphify vscode install` |
| Gemini CLI | `graphify gemini install` (GEMINI.md + BeforeTool hook) |
| OpenCode | `graphify opencode install` (AGENTS.md + plugin) |
| Aider, Trae, Antigravity, Hermes, Kiro, Pi 등 | 각자 `<platform> install` 서브커맨드 |

git 후크로 자동 그래프 갱신: `graphify hook install` (post-commit/post-checkout).

## 4. 헤드리스 CLI (Claude Code 외부)

```powershell
# 빌드
graphify update <path>          # 코드 파일만, LLM 키 불필요
graphify extract <path>         # 전체 (AST + LLM 시맨틱), LLM 키 필요
  --backend kimi|claude         # ANTHROPIC_API_KEY 또는 MOONSHOT_API_KEY
  --no-cluster                  # 클러스터링 생략

# 외부 자료 추가
graphify add <url> --author "Name" --contributor "Name"
graphify clone <github-url>     # GitHub 저장소 복제 후 그래프

# 질의
graphify query "질문"            # BFS 순회, 기본 2000 토큰
  --dfs --budget 1500            # DFS, 토큰 한도
graphify path "노드A" "노드B"     # 두 개념 사이 최단 경로
graphify explain "노드명"         # 노드 + 이웃 평이한 설명

# 그래프 관리
graphify watch <path>            # 폴더 감시, 자동 재빌드 (LLM 불필요)
graphify cluster-only <path>     # 기존 graph.json 재클러스터링만
graphify merge-graphs g1.json g2.json  # 다중 저장소 그래프 병합
graphify check-update <path>     # 의미 재추출 필요 여부 확인 (cron 안전)
graphify benchmark               # naive 풀-코퍼스 대비 토큰 절감 측정
graphify tree                    # D3 v7 collapsible-tree HTML 생성
```

**중요**: 마크다운 위주 보관함은 `update`가 코드 파일만 다루므로 무용지물. `extract`로 LLM 키 + 시맨틱 추출이 필요. Claude Code 스킬을 쓰면 현재 Claude 세션이 LLM 역할을 하므로 별도 키 불필요.

## 5. 슬래시 커맨드 (Claude Code 안)

스킬 설치 후 Claude Code에서:

```
/graphify                          # 현재 디렉토리 풀파이프라인 → Obsidian vault
/graphify <path>
/graphify <path> --mode deep       # 더 적극적인 INFERRED 엣지 추출
/graphify <path> --update          # 변경 파일만 증분 빌드
/graphify <path> --directed        # 방향성 그래프
/graphify <path> --cluster-only    # 재클러스터링만
/graphify <path> --no-viz          # 시각화 생략, 보고서·JSON만
/graphify <path> --svg             # SVG 추가 출력
/graphify <path> --graphml         # Gephi/yEd용
/graphify <path> --neo4j           # cypher.txt
/graphify <path> --neo4j-push bolt://localhost:7687
/graphify <path> --wiki            # 에이전트가 크롤링할 마크다운 위키 (커뮤니티별 article)
/graphify <path> --obsidian --obsidian-dir <vault path>  # 기존 vault에 출력 합류
/graphify <path> --mcp             # MCP stdio 서버 시작 (에이전트 직접 접근)
/graphify <path> --watch           # 자동 재빌드

/graphify add <url>                 # URL 가져와 ./raw에 저장 + 그래프 갱신
/graphify query "<질문>"             # BFS 답변
/graphify query "<질문>" --dfs --budget 1500
/graphify path "AuthModule" "Database"
/graphify explain "노드명"
```

## 6. 산출물

빌드 후 `graphify-out/` 디렉토리:
- `graph.html` — 인터랙티브 시각화 (클릭, 필터, 검색)
- `GRAPH_REPORT.md` — 핵심 개념·연결·추천 질문 자동 생성 보고서
- `graph.json` — 전체 그래프 (재질의용)
- `manifest.json` / `cost.json` — 메타·비용 (gitignore 권장)

`--obsidian` 플래그를 쓰면 코드 엔티티별 자동 노트가 vault에 생성됨. `--wiki` 플래그는 에이전트가 크롤링하기 좋은 마크다운 위키(인덱스 + 커뮤니티별 1 article) 출력.

## 7. 지원 파일 형식

- 코드 (26개 언어 AST): `.py .ts .js .jsx .tsx .go .rs .java .c .cpp .rb .cs .kt .scala .php .swift .lua .zig .ps1 .ex .exs .m .jl .vue .svelte .sql .f .f90 .f95 .f03 .f08`
- 문서: `.md .mdx .html .txt .rst .yaml .yml`
- 오피스: `.docx .xlsx` (`pip install graphifyy[office]` 추가 필요)
- 미디어: `.pdf .png .jpg .webp .gif .mp4 .mov .mp3 .wav` (영상/오디오는 `graphifyy[video]` 추가 필요)

## 8. `.graphifyignore`

`.gitignore` 문법 + `!` 부정 매칭 지원. 예:

```
node_modules/
dist/
*.generated.py
*
!src/
!src/**
```

## 9. 엣지 감사(audit) 규칙

graphify가 만든 모든 엣지(연결)는 출처 태그가 붙음:
- `EXTRACTED` — 실제 자료에서 발견됨 (직접 인용·import·링크 등)
- `INFERRED` — 의미 유사도로 추론됨
- `AMBIGUOUS` — 후보지만 불확실

→ "어디까지가 사실이고 어디부터가 모델 추측인지" 명시. kouwiki 의 [[건강검진]] 패턴과 같은 메커니즘.

## 10. 프라이버시

- 코드 파일: 로컬 tree-sitter 파싱, 외부 전송 없음
- 영상/오디오: 로컬 faster-whisper 전사, 외부 전송 없음
- 문서/PDF/이미지: AI 어시스턴트 API로 전송 (Claude Code 사용 시 Anthropic)
- 텔레메트리·사용량 추적 없음

## 11. claude-code-memory-setup (Lucas Rosati) 사례 요약

핵심 주장: Obsidian + Graphify 조합으로 Claude Code 세션당 **71.5x 적은 토큰 사용**.
- 한 vault 안에 `permanent/` (Zettelkasten), `inbox/`, `fleeting/`, `templates/`, `logs/`, `chats/`, `graphify/<project>/` 폴더 구조
- `/resume`, `/save` 슬래시 커맨드로 세션 영속
- `graphify . --obsidian --obsidian-dir ~/vault/graphify/<proj>` 로 vault 안에 그래프 출력
- 측정 사례: 126개 TS 파일 프로젝트 → 332개 노드, 그래프 파일 172KB, 쿼리당 토큰 499x 감소

→ kouwiki 운영 모델(raw → wiki + index → 마스터 인덱스)과 매우 유사한 철학. 폴더 분류 방식만 약간 차이.

## 12. kouwiki 도입 시 고려사항 (정리 시 적용)

1. **API 키 vs 슬래시 스킬**: 헤드리스 마크다운 빌드는 ANTHROPIC_API_KEY 필요. 현재 미보유. → Claude Code 스킬 경로 권장.
2. **vault CLAUDE.md 보호**: `graphify install --platform windows` 는 사용자 레벨 (`~/.claude/`)에만 쓰므로 `kouwiki/CLAUDE.md` 운영 규칙과 충돌 없음.
3. **결과 위치**: `graphify-out/` 을 vault 안에 둘지(통합) 밖에 둘지(격리) 선택. vault 안에 두면 Obsidian 그래프뷰가 자동 graph.html 옆에 노트들도 보임.
4. **운영 모드 매핑**: kouwiki 의 Ingest/Query/Lint ↔ graphify 의 add/query/cluster-only. 본 위키 운영을 자동화하는 보완재.
5. **Git 후크**: 본 vault가 GitHub로 푸시되므로 `graphify hook install` 로 커밋 시 자동 재빌드 가능 (실험적).
