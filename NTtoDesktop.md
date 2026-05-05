# 내일 아침 회사 데스크탑 셋업 체크리스트

작성일: 2026-05-05 (개인 노트북에서 작성·푸시)
목표: 회사 데스크탑(Windows 11)에 노트북과 동일한 kouwiki 작업 환경을 구축한다.

---

## 0. 출근 직전 노트북 확인 (집에서)

```bash
cd c:/Users/user/Desktop/kouwiki/kouwiki
git status
```

→ "On branch main, nothing to commit" 이면 OK. 변경 있으면 push 후 출근.

---

## 1. 사전 요구 — 회사 PC에 설치돼 있는지 확인

PowerShell 열어서 한 줄씩:

```powershell
python --version       # 3.10 이상
git --version
```

| 도구 | 없으면 |
|------|--------|
| Python 3.10+ | <https://python.org> → 3.12 다운로드, **"Add to PATH" 체크 필수** |
| Git | <https://git-scm.com> |
| Claude Code | <https://www.anthropic.com/claude-code> (VS Code 확장 또는 데스크탑) |
| Obsidian (선택) | <https://obsidian.md> |

---

## 2. vault 복제

```powershell
cd C:\Users\<회사PC사용자명>\Desktop
mkdir kouwiki
cd kouwiki
git clone https://github.com/kouros7376/LLMwiki.git kouwiki
cd kouwiki
git status
```

→ "On branch main, nothing to commit" 확인.

**private 저장소면** 첫 clone에 GitHub 인증 요구:

```powershell
# 옵션 A: GitHub CLI (가장 깔끔)
winget install GitHub.cli
gh auth login

# 옵션 B: PAT(Personal Access Token) 만들어서 password 자리에 붙여넣기
# https://github.com/settings/tokens → "repo" 권한
```

---

## 3. Graphify 설치 (2명령)

```powershell
python -m pip install --user graphifyy
python -m graphify install --platform windows
```

검증:

```powershell
python -m graphify --help        # 도움말 나오면 OK
```

설치되는 위치 (참고만):
- 패키지: `C:\Users\<사용자명>\AppData\Roaming\Python\Python312\Lib\site-packages\graphify\`
- CLI: `C:\Users\<사용자명>\AppData\Roaming\Python\Python312\Scripts\graphify.exe`
- 스킬: `C:\Users\<사용자명>\.claude\skills\graphify\SKILL.md`
- 사용자 CLAUDE.md 트리거: `C:\Users\<사용자명>\.claude\CLAUDE.md`

---

## 4. Claude Code 설정

1. VS Code 확장 또는 데스크탑 앱 로그인 (Anthropic 계정)
2. **새 창 시작** 후 `File > Open Folder` → `C:\Users\<사용자명>\Desktop\kouwiki\kouwiki`
3. `/graphify` 인식 확인 — 슬래시 입력해서 자동완성 목록에 graphify 보이면 OK

---

## 5. (선택) Obsidian 보관함 등록

Obsidian → "Open folder as vault" → `kouwiki/kouwiki` 선택.
`.obsidian/` 설정이 이미 push되어 있으므로 플러그인·테마 자동 적용.

---

## 6. 셋업 검증 — 첫 작업

### 6-1. 그래프 그대로 열기 (재빌드 불필요)

```powershell
start graphify-out\graph.html
```

→ 노트북에서 봤던 34 노드 그래프가 그대로 뜨면 vault 동기화 OK.

### 6-2. 위키 ingest 테스트

raw/에 아무 마크다운 1개 추가하고 Claude에게:
```
raw에 자료 넣었어 — <파일명>
```

→ Claude가 wiki/에 정리하면 운영 흐름 OK.

### 6-3. (선택) 그래프 재빌드

회사 PC에서 처음 `/graphify ./wiki` 실행하면 캐시가 비어 있어 ~12만 토큰 재추출.
노트북과 같은 결과 (34 노드 / 179 엣지 / 6 커뮤니티) 가 나오면 환경 동등성 검증 완료.

---

## 7. 매일 흐름 — 양쪽 PC 사이 동기화 원칙

| 상황 | 명령 |
|------|------|
| 작업 시작 (어느 PC든) | `git pull origin main` |
| 작업 마감 (어느 PC든) | `git add <파일> && git commit -m "..." && git push origin main` |

**원칙: 시작 = pull, 마감 = push.** pull을 잊으면 conflict.

---

## 8. 비상 복구 명령

| 사고 | 복구 |
|------|------|
| pull 빠뜨려 conflict 발생 | `git status` → 충돌 파일 직접 정리 → `git add . && git commit` |
| 잘못 commit (push 전) | `git reset --soft HEAD~1` (push 후엔 사용 금지) |
| `--user` 설치 후 PATH 미적용 | `python -m graphify` 형태로 호출 (검증 OK) |
| `python` 명령 인식 안 됨 | Python 재설치 시 "Add to PATH" 체크했는지 확인 |
| Claude Code에서 `/graphify` 인식 안 됨 | Claude Code 완전 재시작 (스킬 설치 후 새 세션 필요) |

---

## 9. 미해결 사항 — 회사 PC 셋업 후 결정

- **`.obsidian/workspace.json` 추적 여부**
  - 현재 추적 중 → PC별 패널 레이아웃이 매번 commit 노이즈 됨
  - 분리하려면 `.gitignore`에 `.obsidian/workspace.json` 추가 + `git rm --cached .obsidian/workspace.json` 후 push
  - 회사 PC에서 한 번 작업해보고 결정

- **API 키 사용 여부**
  - 헤드리스 `graphify extract` 가 필요하면 `ANTHROPIC_API_KEY` 환경변수 설정
  - 본 위키는 `/graphify` 슬래시 스킬만 쓰면 키 불필요 (현 셋업)

---

## 10. 한 줄 요약

```
clone → pip install --user graphifyy → graphify install --platform windows → 시작.
매일 git pull (시작) / git push (마감).
```

이 파일이 더 이상 필요 없어지면 삭제 OK (작업 마감 후 또는 본 셋업이 안정되면).
