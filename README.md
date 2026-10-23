# kickoff

Claude Code로 새 프로젝트를 시작할 때 쓰는 개인 스킬입니다. `/kickoff` 뒤에 만들 것을 적으면 Claude가 아래 일을 이어서 진행합니다.

- 환경 확인
- 실제로 작동하는 최소 기능 구현
- 작업 목록(`TASKS.md`) 작성
- 짧은 지침(`CLAUDE.md`)과 인계 기록 작성
- 직접 검증

비개발자가 쓰는 것을 전제로 합니다. 그래서 모든 설명을 쉬운 한국어로 하고, 기술 결정은 Claude가 내립니다. push·배포·결제처럼 되돌리기 어려운 일은 항상 먼저 묻습니다.

## 설치 (PC마다 한 번)

필요한 것: Git, Claude Code 2.1.139 이상.

PowerShell:

```powershell
git clone https://github.com/eycollective/claude-kickoff "$HOME\.claude\skills\kickoff"
```

Git Bash:

```bash
git clone https://github.com/eycollective/claude-kickoff ~/.claude/skills/kickoff
```

설치한 뒤에는 Claude Code 세션을 새로 열어야 명령 목록에 `/kickoff`가 나타납니다.

## 사용

1. 새 프로젝트 폴더에서 Claude Code를 열고 입력합니다.

   ```
   /kickoff 동네 빵집 예약 페이지. 손님이 날짜와 빵을 고르면 사장님이 목록으로 확인한다.
   ```

   설명 없이 `/kickoff`만 입력하면 Claude가 무엇을 만들지 먼저 묻습니다.
2. 첫 보고 때 Claude가 `/goal ...`로 시작하는 한 줄을 보여 줍니다. 그 줄을 그대로 복사해 입력하면 남은 작업을 멈추지 않고 이어 갑니다.
3. 그만하고 싶을 때는 "마무리하자"라고 말하거나 `/handoff`를 입력합니다. 그러면 Claude가 정리하고 다음에 이어 갈 기록을 남깁니다.

## 업데이트

PowerShell:

```powershell
git -C "$HOME\.claude\skills\kickoff" pull
```

Git Bash:

```bash
git -C ~/.claude/skills/kickoff pull
```

## 들어 있는 파일

- `SKILL.md`: 본문
- `references/plugins.md`: 플러그인을 고를 때 읽는 표와 조건
- `references/hooks.md`: 훅을 만들 때의 규칙과 `/goal`을 쓸 수 없을 때의 지속 훅 설계

## 함께 쓰면 좋은 것 (선택)

- [ego lite](https://lite.ego.app/): 화면을 직접 눌러 보며 확인할 때 씁니다. 이 앱이 없으면 Claude가 화면 확인을 "미확인"으로 남깁니다.
- `superpowers`, `frontend-design` 플러그인: 필요할 때 Claude가 설치 여부를 확인합니다.
