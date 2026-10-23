# 플러그인 고르기와 설치

kickoff 스킬 6번에서 플러그인을 설치하거나 고르기 전에 읽는다.

## 마켓플레이스와 설치 범위

- 공식 마켓플레이스 `claude-plugins-official`은 보통 처음 실행할 때 자동 등록된다. `claude plugin marketplace list --json`에 그 이름이 없을 때만 `claude plugin marketplace add anthropics/claude-plugins-official`로 등록한다. 명령의 현재 문법은 `claude plugin --help`로 확인한다.
- 이미 켜진 플러그인은 다시 설치하지 않는다. 새로 설치할 때, 이 제품을 만드는 동료에게도 꼭 필요한 것은 `--scope project`(커밋되는 `.claude/settings.json`에 기록)로, 내 작업 습관용은 `--scope local`로 한다. 같은 플러그인을 다른 마켓플레이스에서 또 설치하지 않는다.

## 후보

| 필요한 경우 | 후보(제작자) | 확인할 점 |
|---|---|---|
| 복잡한 설계·계획·검증 워크플로 | `superpowers` (Jesse Vincent) | 공식 마켓 버전이 제작자 최신보다 늦을 수 있다. Windows에서는 Git Bash가 있어야 시작 안내가 주입된다 |
| 사용자 화면의 설계·구현 | `frontend-design` (Anthropic) | 스킬 1개, 훅·MCP 없음 |
| 라이브러리의 최신 공식 문서 조회가 반복됨 | `context7` (Upstash) | 질의 내용이 외부로 전송되므로 비밀·사내 코드를 질의에 넣지 않는다. 데스크톱 앱에서는 새 세션이 필요할 수 있다 |
| 해당 언어의 오류 탐지·탐색 | `typescript-lsp`, `pyright-lsp` 등 | 언어 서버 실행 파일이 따로 있어야 한다 |
| 민감 기능의 추가 보안 검토 | `security-guidance` (Anthropic) | 기본 미설치. 아래 조건을 먼저 확인한다 |

설치 형식: `claude plugin install <후보>@claude-plugins-official --scope <project 또는 local>`

설치하지 않는 것: 지속 반복 플러그인(`ralph-loop` 등, 지속 제어기는 `/goal` 하나), 다른 브라우저 조작 플러그인(Playwright MCP 등, 브라우저는 ego-browser 하나).

## security-guidance 조건

Stop과 `git commit`·`git push` 때 diff와 관련 파일을 `api.anthropic.com`으로 보내 모델 검토를 한다(사용량·비용 발생, Stop에서 Claude를 최대 3회 다시 깨움). 세션 시작 때 Python 패키지를 내려받으며 Git Bash·Python·git 저장소가 필요하다. 인증·결제·개인정보 같은 민감 기능이 있고 비용과 전송을 사용자가 허용한 경우에만 설치하고, 설치하면 `/goal` 같은 지속 실행 제어와 함께 어떻게 동작하는지 확인한다. 기본 보안 검토는 의미 있는 변경의 독립 리뷰와 프로젝트의 기존 검사로 한다.

## 설치 뒤 반영

- 셸에서 `claude plugin install`로 설치하면 현재 세션에는 `/reload-plugins`를 입력하거나 새 세션을 열어야 반영된다.
- 세션 시작 뒤 `.claude/skills/` 폴더를 새로 만들었다면 `/reload-skills`가 필요할 수 있다.
- 이 slash 명령은 사람만 입력할 수 있다. 실제로 필요한 것만 이유와 함께 사람의 일로 한 번에 묶어 요청하고, 그 답과 무관한 일은 계속한다.
- 설치 후 `claude plugin list`, `claude plugin details <이름>`과 실제 노출된 기능을 확인한다.
