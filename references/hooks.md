# 훅 규칙과 최소 지속 훅

kickoff 스킬 8번에서 훅을 하나라도 만들기 전에 읽는다.

## 훅 공통 규칙

- `type: module` 설정에 영향받지 않는 `.cjs` 파일을 쓰고, exec 형식 `"command": "node", "args": ["${CLAUDE_PROJECT_DIR}/.claude/hooks/<파일>.cjs", "<인자>"]`로 등록한다.
- `timeout`을 반드시 적는다(약 10초. 적지 않으면 기본 600초다). 내부 오류·파싱 실패 때는 exit 0으로 통과시켜 사용자를 가두지 않는다.
- 입출력은 UTF-8로 처리하고, 주입하는 글은 8,000자 이내로 자른다(한도 1만 자를 넘으면 앞부분만 보인다). 원본 로그나 비밀파일은 읽지 않는다.
- 실제 세션 이벤트에서 `claude --debug` 로그로 실행을 확인한다. Windows에서 exec 형식이 실패하면 셸 형식 `node "${CLAUDE_PROJECT_DIR}/.claude/hooks/<파일>.cjs" <인자>`로 바꾼다. 수동 입력 테스트만으로 현 세션 활성화를 단정하지 않는다.
- 포맷 훅은 편집한 파일 하나만 처리하고 검증 도구와 책임을 중복하지 않는다.

## `/goal`을 쓸 수 없을 때만 만드는 최소 지속 훅

기존 플러그인의 Stop 훅과 중복되는지 먼저 확인한다. 출처 불명의 훅이나 개인 kit 경로를 쓰지 않는다. 파일은 `.claude/hooks/continuity.cjs` 하나로, 이벤트 이름이나 서브명령을 인자로 받는다.

- SessionStart(matcher `startup|resume|clear|compact`): `.context.md` 요약, 열린 TASKS 수, 현재 session_id와 상태 파일 위치를 주입한다(`/clear`와 fork는 새 session_id를 만든다). 이 훅을 쓰면 CLAUDE.md의 `@.context.md` 줄을 뺀다.
- 상태: `.claude/state/continuity/<session_id>.json`(Git 제외). 최소 필드는 `mode: running|winding_down|stopped`, `handoffComplete`, `blocksThisMessage`, `noProgressCount`, `lastEvidence`다. 모델은 상태 파일을 직접 편집하지 않고 `node .claude/hooks/continuity.cjs state ...` 서브명령으로만 원자적으로 바꾼다(세션 ID는 환경변수 `CLAUDE_CODE_SESSION_ID`). 종료 의도를 판단하면 메인이 `winding_down`을, 마무리 절차 후 `stopped`와 `handoffComplete: true`를 기록하고, 명시적 재개 때 초기화한다. 서브에이전트는 상태를 바꾸지 않는다. `.context.md`의 수정 시각만으로 인계 완료를 판정하지 않는다.
- UserPromptSubmit: 메시지당 반복 횟수만 0으로 되돌린다. 종료·재개 의도는 메시지 문자열(인용·코드블록 포함)이 아니라 모델이 판단한다.
- Stop: mode가 `running`이고 보류가 아닌 `[ ]`·`[~]`가 남아 있으면 `hookSpecificOutput.additionalContext`로 다음 실행 가능한 항목을 이어 가라고 짧게 알린다. 실행 중인 결과를 기다리는 `[~]`(입력의 `background_tasks`로 확인)는 중복 구현시키지 않는다. 사람 대기뿐, 인계 완료 또는 미인계 안내 1회 후, `stopped`, 상태 손상, 메시지당 8회, 진척 없음 3회면 종료를 허용하고 이유를 기록한다. 진척은 항목 상태 변화·관련 코드 변경·새 검증 증거로 보고 문서 수정 시각만으로 보지 않는다. 호스트의 연속 차단 한도(기본 8회)는 도구를 호출할 때마다 초기화되므로 이 자체 상한이 꼭 필요하며, `stop_hook_active` 값과 관계없이 적용한다. Stop마다 테스트를 실행하지 않는다.
- 합성 입력으로 시작·컴팩션·clear·열린 일·진행 중 일·전부 사람 대기·실제 종료·인계 완료·재개·반복 상한·무진척·손상 상태를 확인해 기대값과 대조하고, 실제 세션 이벤트에서도 한 번 확인해 적용 여부를 보고한다.
