# kickoff

Claude Code로 새 프로젝트를 시작할 때 쓰는 개인 스킬입니다. `/kickoff` 뒤에 만들 것을 적으면 Claude가 아래 일을 이어서 진행합니다.

- 환경 확인
- 실제로 작동하는 최소 기능 구현
- 작업 목록(`TASKS.md`) 작성
- 짧은 지침(`CLAUDE.md`)과 인계 기록 작성
- 직접 검증

비개발자가 쓰는 것을 전제로 합니다. 그래서 모든 설명을 쉬운 한국어로 하고, 기술 결정은 Claude가 내립니다. push·배포·결제처럼 되돌리기 어려운 일은 항상 먼저 묻습니다.

## 설치와 업데이트

필요한 것: Git, Claude Code 2.1.139 이상.

아래 명령 하나로 설치와 업데이트를 모두 합니다. 스킬 폴더가 없으면 새로 설치하고, 있으면 최신 내용을 받습니다. 몇 번을 실행해도 안전합니다.

PowerShell:

```powershell
if (Test-Path "$HOME\.claude\skills\kickoff") { git -C "$HOME\.claude\skills\kickoff" pull } else { git clone https://github.com/eycollective/claude-kickoff "$HOME\.claude\skills\kickoff" }
```

Git Bash:

```bash
if [ -d ~/.claude/skills/kickoff ]; then git -C ~/.claude/skills/kickoff pull; else git clone https://github.com/eycollective/claude-kickoff ~/.claude/skills/kickoff; fi
```

### Claude Code에게 한 번에 말하기 (설치·업데이트 + 시작)

새 프로젝트 폴더에서 Claude Code를 열고, 아래 문장의 `만들 것:` 뒤만 바꿔서 그대로 보내면 됩니다. 그러면 Claude가 다음 일을 차례로 합니다.

1. 스킬을 설치합니다(이미 있으면 업데이트).
2. 스킬 파일을 직접 읽습니다.
3. 바로 새 프로젝트를 시작합니다.

```
https://github.com/eycollective/claude-kickoff 를 ~/.claude/skills/kickoff 에 설치해 줘(이미 있으면 git pull로 업데이트). 그다음 그 폴더의 SKILL.md를 읽고 그대로 따라서 이 폴더에서 새 프로젝트를 시작해 줘. 만들 것: 동네 빵집 예약 페이지. 손님이 날짜와 빵을 고르면 사장님이 목록으로 확인한다.
```

이미 설치된 PC에서 업데이트가 필요 없으면 `/kickoff 만들 것 설명`이 더 간단합니다.

설치한 뒤에는 Claude Code 세션을 새로 열어야 명령 목록에 `/kickoff`가 나타납니다. 업데이트한 내용은 다음에 `/kickoff`를 실행할 때부터 적용됩니다.

## 사용

1. 새 프로젝트 폴더에서 Claude Code를 열고 입력합니다.

   ```
   /kickoff 동네 빵집 예약 페이지. 손님이 날짜와 빵을 고르면 사장님이 목록으로 확인한다.
   ```

   설명 없이 `/kickoff`만 입력하면 Claude가 무엇을 만들지 먼저 묻습니다.
2. 첫 보고 때 Claude가 `/goal ...`로 시작하는 한 줄을 보여 줍니다. 그 줄을 그대로 복사해 입력하면 남은 작업을 멈추지 않고 이어 갑니다.
3. 이미 진행 중인 프로젝트에도 똑같이 씁니다. 그 프로젝트 폴더에서 `/kickoff 다음 목표 설명`을 입력하면 됩니다. Claude가 기존 프로젝트인지 알아서 판단합니다. 기존 프로젝트라면 다음처럼 진행합니다.
   - 기존 파일과 아직 저장하지 않은 변경은 건드리지 않습니다.
   - 바꾸기 전에 지금 상태(어떤 검사가 원래 통과하고 실패하는지)부터 기록합니다.
   - 빠진 문서만 더하고, 별도 작업 브랜치에서 진행합니다.
4. 그만하고 싶을 때는 "마무리하자"라고 말하거나 `/handoff`를 입력합니다. 그러면 Claude가 정리하고 다음에 이어 갈 기록을 남깁니다.

## 들어 있는 파일

- `SKILL.md`: 본문
- `references/plugins.md`: 플러그인을 고를 때 읽는 표와 조건
- `references/ego-browser.md`: 브라우저 설치 확인, 작업 공간, 로그인이 필요한 사이트 처리
- `references/hooks.md`: 훅을 만들 때의 규칙과 `/goal`을 쓸 수 없을 때의 지속 훅 설계

## 함께 쓰면 좋은 것 (선택)

- [ego lite](https://lite.ego.app/): 화면을 직접 눌러 보며 확인할 때 씁니다. 이 앱이 없으면 Claude가 화면 확인을 "미확인"으로 남깁니다.
  - 외부 사이트 작업도 맡길 수 있습니다. 해당 사이트에 ego lite로 **한 번만 직접 로그인**해 두면, 그다음부터는 Claude가 로그인된 상태로 작업합니다. 로그인이 필요해지면 Claude가 "○○ 사이트에 로그인해 주세요"라고 요청합니다.
  - Claude가 직접 로그인하게 하려면 프로젝트의 `.env`에 `<사이트>_LOGIN_URL`, `<사이트>_ID`, `<사이트>_PASSWORD`를 적어 둡니다(예: `SHOP_LOGIN_URL=https://...`). 그러면 Claude는 다음처럼 로그인합니다.
    - 로그인 스크립트가 이 값을 직접 읽어 입력하므로, 값이 대화나 기록에 나오지 않습니다.
    - 주소가 `_LOGIN_URL`과 다른 페이지에는 입력하지 않습니다.
    - 2단계 인증 코드는 사람에게 요청합니다.
    - 로그인이 2번 실패하면 멈춥니다.
  - `.env`는 저장소에 올리지 않습니다. 이 방식에는 위험이 따릅니다. 에이전트가 실제 계정 정보에 접근하고, 가짜 페이지에 속아 입력할 수도 있습니다. 중요한 계정은 ego lite에서 직접 로그인하는 방식을 권합니다.
  - 결제·발송·게시·삭제처럼 되돌리기 어려운 일은 먼저 무엇을 할지 보여 주고 승인을 받습니다.
- `superpowers`, `frontend-design` 플러그인: 필요할 때 Claude가 설치 여부를 확인합니다.
