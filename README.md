# claude-plugin-dev-server

Claude Code plugin for managing dev servers via tmux.

## Installation

```bash
claude plugin install dev-server --scope user
```

Or install from this repository:

```bash
# Clone and test locally
git clone https://github.com/arahansa/claude-plugin-dev-server.git
claude --plugin-dir ./claude-plugin-dev-server
```

## Features

- tmux 세션으로 dev 서버를 백그라운드 관리
- 자동 세션 네이밍 (디렉터리 폴더명 기반)
- 서버 시작/정지/재시작/로그 확인
- 모노레포 멀티 서버 지원
- 포트 충돌 자동 감지 및 복구

## Triggers

`"서버 띄워"`, `"dev 서버"`, `"서버 재시작"`, `"서버 로그"`, `"run dev server"`, `"start server"`, `"restart server"`

## License

MIT
