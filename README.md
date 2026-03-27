# claude-plugin-arahansa

arahansa's Claude Code plugin collection.

## Installation

```bash
claude plugin install arahansa --scope user
```

Or install from marketplace:

```bash
# 마켓플레이스 등록
claude plugin marketplace add https://raw.githubusercontent.com/arahansa/claude-plugin-arahansa/master/marketplace.json

# 설치
claude plugin install arahansa
```

Or test locally:

```bash
git clone https://github.com/arahansa/claude-plugin-arahansa.git
claude --plugin-dir ./claude-plugin-arahansa
```

## Skills

### dev-server (`/arahansa:dev-server`)

tmux 세션으로 dev 서버를 백그라운드 관리.

- 자동 세션 네이밍 (디렉터리 폴더명 기반)
- 서버 시작/정지/재시작/로그 확인
- 모노레포 멀티 서버 지원
- 포트 충돌 자동 감지 및 복구

**Triggers:** `"서버 띄워"`, `"dev 서버"`, `"서버 재시작"`, `"서버 로그"`, `"run dev server"`, `"start server"`, `"restart server"`

### cmux-browser (`/arahansa:cmux-browser`)

cmux 내장 WebKit 브라우저를 CLI에서 제어.

- 스크린샷 & 접근성 스냅샷
- 브라우저 상태 저장/복원 (쿠키, 스토리지)
- 브라우저 초기화 (쿠키/스토리지 클리어)
- 네비게이션, 폼 자동화, 클릭/타이핑
- 페이지 데이터 조회 (텍스트, HTML, 스타일)
- Playwright-style 요소 탐색 & 대기
- JavaScript 실행 & CSS 주입
- 뷰포트, 네트워크 라우팅, 트레이싱

**Triggers:** `"cmux"`, `"브라우저 스크린샷"`, `"브라우저 상태 저장"`, `"브라우저 초기화"`, `"browser state"`, `"screenshot"`, `"스냅샷"`

## License

MIT
