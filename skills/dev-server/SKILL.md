---
name: dev-server
description: Use when you need to start, stop, restart, or check dev servers during development. Triggers on "서버 띄워", "dev 서버", "서버 재시작", "서버 로그", "run dev server", "start server", "restart server", or when implementation work requires a running dev server for verification.
---

# Dev Server Management via tmux

tmux 세션을 사용하여 dev 서버를 백그라운드에서 관리한다.

## Session Naming Convention

세션명은 실행하는 디렉터리의 폴더명을 사용한다.

```bash
# 세션명 결정 (현재 디렉터리의 폴더명)
SESSION_NAME="$(basename "$(pwd)")"
```

예: `/Users/me/code/my-blog` → 세션명 `my-blog`

모노레포에서 여러 서버를 띄울 때는 `{폴더명}-backend`, `{폴더명}-frontend` 형식을 사용한다.

## Commands

### Pre-check (항상 먼저 실행)

서버를 시작하기 전에 해당 세션이 이미 있는지 확인한다:

```bash
SESSION_NAME="$(basename "$(pwd)")"
tmux has-session -t "$SESSION_NAME" 2>/dev/null && echo "running: $SESSION_NAME" || echo "no session: $SESSION_NAME"
```

- 이미 세션이 떠있으면 새로 만들지 않는다.
- 로그를 확인해서 정상 동작 중인지 체크한다:

```bash
tmux capture-pane -t "$SESSION_NAME" -p | tail -10
```

- 정상이면 "이미 서버가 실행 중입니다"로 안내하고 끝낸다.
- 에러가 있으면 해당 세션만 재시작한다.

### Start

기존 세션이 없을 때만 새로 생성한다:

```bash
SESSION_NAME="$(basename "$(pwd)")"

# 단일 서버
tmux new-session -d -s "$SESSION_NAME" -c "$(pwd)" '{command}'

# 예시: /Users/me/code/my-blog 에서 실행 → 세션명 my-blog
tmux new-session -d -s "$SESSION_NAME" -c "$(pwd)" 'pnpm dev'

# 모노레포 예시: my-blog-backend, my-blog-frontend
tmux new-session -d -s "${SESSION_NAME}-backend" -c "$(pwd)" 'pnpm run start:dev'
tmux new-session -d -s "${SESSION_NAME}-frontend" -c "$(pwd)/client" 'pnpm dev'
```

시작 후 3초 대기 → 로그 확인으로 정상 기동 여부를 체크한다:

```bash
SESSION_NAME="$(basename "$(pwd)")"
tmux new-session -d -s "$SESSION_NAME" -c "$(pwd)" 'pnpm dev' && sleep 3 && tmux capture-pane -t "$SESSION_NAME" -p | tail -20
```

### Stop

```bash
SESSION_NAME="$(basename "$(pwd)")"
tmux kill-session -t "$SESSION_NAME"
```

### Restart

```bash
SESSION_NAME="$(basename "$(pwd)")"
tmux kill-session -t "$SESSION_NAME" 2>/dev/null; tmux new-session -d -s "$SESSION_NAME" -c "$(pwd)" '{command}' && sleep 3 && tmux capture-pane -t "$SESSION_NAME" -p | tail -20
```

### Logs

```bash
SESSION_NAME="$(basename "$(pwd)")"

# 최근 출력 확인
tmux capture-pane -t "$SESSION_NAME" -p -S -50

# 실시간 스냅샷 (마지막 20줄)
tmux capture-pane -t "$SESSION_NAME" -p | tail -20
```

### Status

```bash
SESSION_NAME="$(basename "$(pwd)")"

# 세션 존재 여부
tmux has-session -t "$SESSION_NAME" 2>/dev/null && echo "running" || echo "stopped"

# 현재 프로젝트 관련 세션 목록
tmux list-sessions 2>/dev/null | grep "^$SESSION_NAME" || echo "no sessions for $SESSION_NAME"
```

### Stop All (현재 프로젝트)

```bash
SESSION_NAME="$(basename "$(pwd)")"
tmux list-sessions -F '#{session_name}' 2>/dev/null | grep "^$SESSION_NAME" | xargs -I{} tmux kill-session -t {}
```

## How to Determine the Dev Command

1. CLAUDE.md에 dev 명령이 정의되어 있으면 그것을 사용
2. 없으면 `package.json`의 `scripts.dev` 또는 `scripts.start:dev` 확인
3. 모노레포면 각 workspace의 dev 스크립트를 별도 세션으로 실행

## Common Patterns

| 프로젝트 타입 | 명령 | 세션명 예시 (`my-blog/`) |
|---|---|---|
| Next.js | `pnpm dev` | `my-blog` |
| NestJS | `pnpm run start:dev` | `my-blog` |
| 풀스택 모노레포 | 백엔드 + 프론트 각각 | `my-blog-backend`, `my-blog-frontend` |
| Vite | `pnpm dev` | `my-blog` |

## Error Recovery

서버가 비정상 종료되면 (포트 충돌 등):

```bash
# 포트 사용 중인 프로세스 확인
lsof -i :3000 | grep LISTEN

# 강제 종료 후 재시작
SESSION_NAME="$(basename "$(pwd)")"
kill -9 $(lsof -t -i :3000) 2>/dev/null
tmux kill-session -t "$SESSION_NAME" 2>/dev/null
tmux new-session -d -s "$SESSION_NAME" -c "$(pwd)" '{command}'
```
