---
name: cmux-browser
description: Use when you need to control the cmux embedded browser — take screenshots, save/load browser state, clear cookies/storage, navigate pages, automate interactions, or inspect page content. Triggers on "cmux", "브라우저 스크린샷", "브라우저 상태 저장", "브라우저 초기화", "browser state", "screenshot", "스냅샷", or when browser automation is needed during development.
---

# cmux Browser Control

cmux 내장 WebKit 브라우저를 CLI에서 제어한다. 모든 명령은 `cmux browser` 접두사를 사용한다.

## Environment

cmux 터미널 내에서는 `CMUX_WORKSPACE_ID`와 `CMUX_SURFACE_ID` 환경변수가 자동 설정된다. 별도 `--surface` 지정 없이 현재 surface를 대상으로 동작한다. 특정 surface를 지정하려면 `--surface <id|ref|index>` 플래그를 사용한다.

---

## 1. Surface / Lifecycle

```bash
# 새 브라우저 열기
cmux browser open [url]

# surface 정보 확인
cmux browser identify [--surface <id>]

# 탭 관리
cmux browser tab list
cmux browser tab new
cmux browser tab switch <index>
cmux browser tab close

# surface 닫기 (top-level 명령)
cmux close-surface [--surface <id>]

# 전체 workspace 트리 확인
cmux tree
```

## 2. Navigation

```bash
cmux browser goto <url> [--snapshot-after]
cmux browser back [--snapshot-after]
cmux browser forward [--snapshot-after]
cmux browser reload [--snapshot-after]
cmux browser url                          # 현재 URL 조회
```

`--snapshot-after`: 네비게이션 후 자동으로 접근성 스냅샷을 찍는다.

## 3. Screenshots & Snapshots

```bash
# PNG 스크린샷
cmux browser screenshot [--out <path>] [--json]
# --out: 파일로 저장, --json: base64 인코딩 반환

# 접근성 트리 스냅샷 (텍스트 기반 DOM)
cmux browser snapshot [--interactive|-i] [--compact] [--max-depth <n>] [--selector <css>] [--cursor]
# --interactive: 인터랙티브 요소만, --compact: 압축 뷰
```

### 스크린샷 저장 패턴

```bash
# 프로젝트 내 저장
cmux browser screenshot --out ./screenshots/$(date +%Y%m%d_%H%M%S).png

# 임시 파일로 저장
cmux browser screenshot --out /tmp/cmux-screenshot.png
```

## 4. State Save / Load

```bash
# 전체 브라우저 상태 저장 (쿠키, 스토리지 등)
cmux browser state save <path>

# 저장된 상태 복원
cmux browser state load <path>
```

### 상태 저장 패턴

```bash
# 프로젝트 내 저장
cmux browser state save ./browser-state.json

# 인증 상태 보존
cmux browser state save ./auth-state.json
# 나중에 복원
cmux browser state load ./auth-state.json
```

## 5. Cookie Management

```bash
# 쿠키 조회
cmux browser cookies get [--name <name>] [--url <url>] [--domain <domain>]

# 쿠키 설정
cmux browser cookies set --name <name> --value <value> [--url <url>] [--domain <domain>] [--path <path>] [--expires <unix>] [--secure]

# 쿠키 삭제
cmux browser cookies clear [--name <name>] [--url <url>] [--domain <domain>] [--all]
```

## 6. Storage Management

```bash
# localStorage
cmux browser storage local get [key]
cmux browser storage local set <key> <value>
cmux browser storage local clear

# sessionStorage
cmux browser storage session get [key]
cmux browser storage session set <key> <value>
cmux browser storage session clear
```

## 7. Browser Reset (전체 초기화)

전체 브라우저 상태를 초기화하려면 쿠키와 스토리지를 모두 클리어한다:

```bash
cmux browser cookies clear --all && cmux browser storage local clear && cmux browser storage session clear
```

특정 도메인만 초기화:

```bash
cmux browser cookies clear --domain <domain> && cmux browser storage local clear && cmux browser storage session clear
```

## 8. Interaction / Automation

```bash
# 클릭
cmux browser click <selector> [--snapshot-after]
cmux browser dblclick <selector> [--snapshot-after]

# 호버 / 포커스
cmux browser hover <selector> [--snapshot-after]
cmux browser focus <selector> [--snapshot-after]

# 텍스트 입력
cmux browser type <selector> <text> [--snapshot-after]    # append
cmux browser fill <selector> [text] [--snapshot-after]    # clear + type

# 키 입력
cmux browser press <key> [--snapshot-after]               # Enter, Tab 등
cmux browser keydown <key> [--snapshot-after]
cmux browser keyup <key> [--snapshot-after]

# 체크박스
cmux browser check <selector> [--snapshot-after]
cmux browser uncheck <selector> [--snapshot-after]

# 셀렉트 박스
cmux browser select <selector> <value> [--snapshot-after]

# 스크롤
cmux browser scroll [--selector <css>] [--dx <n>] [--dy <n>] [--snapshot-after]
cmux browser scroll-into-view <selector> [--snapshot-after]

# 하이라이트
cmux browser highlight <selector>

# 다이얼로그 (alert/confirm/prompt)
cmux browser dialog <accept|dismiss> [text]

# 파일 다운로드
cmux browser download [wait] [--path <path>] [--timeout-ms <ms>]
```

## 9. Page Data Querying

```bash
cmux browser get url
cmux browser get title
cmux browser get text [--selector <css>]
cmux browser get html [--selector <css>]
cmux browser get value [--selector <css>]
cmux browser get attr <css> <name>
cmux browser get count [--selector <css>]
cmux browser get box [--selector <css>]
cmux browser get styles [--selector <css>] [--property <name>]

# 상태 확인
cmux browser is visible <selector>
cmux browser is enabled <selector>
cmux browser is checked <selector>
```

## 10. Element Finding (Playwright-style Locators)

```bash
cmux browser find role <role> [--name <text>] [--exact]
cmux browser find text <text> [--exact]
cmux browser find label <text> [--exact]
cmux browser find placeholder <text> [--exact]
cmux browser find alt <text> [--exact]
cmux browser find title <text> [--exact]
cmux browser find testid <text> [--exact]
cmux browser find first [--selector <css>]
cmux browser find last [--selector <css>]
cmux browser find nth --index <n> [--selector <css>]
```

## 11. Waiting

```bash
cmux browser wait --selector <css>
cmux browser wait --text <text>
cmux browser wait --url-contains <text>
cmux browser wait --load-state <interactive|complete>
cmux browser wait --function <js>
cmux browser wait --timeout-ms <ms>            # 다른 옵션과 조합 가능
```

## 12. JavaScript Execution

```bash
cmux browser eval <script>                      # JS 실행
cmux browser addinitscript <script>             # 매 페이지 로드 시 실행
cmux browser addscript <script>                 # 현재 페이지에 스크립트 추가
cmux browser addstyle <css>                     # CSS 주입
```

## 13. Frames

```bash
cmux browser frame <selector>                   # iframe으로 컨텍스트 전환
cmux browser frame main                         # 메인 프레임으로 복귀
```

## 14. Console & Errors

```bash
cmux browser console list
cmux browser console clear
cmux browser errors list
cmux browser errors clear
```

## 15. Viewport, Geolocation & Network

```bash
# 뷰포트
cmux browser viewport <width> <height>

# 지오로케이션
cmux browser geolocation <lat> <lon>

# 오프라인 모드
cmux browser offline <true|false>

# 네트워크 라우팅
cmux browser network route <pattern> [--abort] [--body <text>]
cmux browser network unroute <pattern>
cmux browser network requests
```

## 16. Tracing, Screencast & Low-level Input

```bash
# 트레이싱
cmux browser trace start [path]
cmux browser trace stop [path]

# 화면 녹화
cmux browser screencast start
cmux browser screencast stop

# 저수준 입력
cmux browser input mouse [args]
cmux browser input keyboard [args]
cmux browser input touch [args]
```

## 17. Webview Focus

```bash
cmux browser focus-webview
cmux browser is-webview-focused
```

---

## Common Workflows

### 로그인 상태 보존

```bash
# 1. 로그인 페이지로 이동 후 수동 로그인
cmux browser goto https://example.com/login
# 2. 상태 저장
cmux browser state save ./auth-state.json
# 3. 나중에 복원
cmux browser state load ./auth-state.json
cmux browser reload
```

### 페이지 상태 캡처 및 디버깅

```bash
# 스크린샷 + 스냅샷 + 콘솔/에러 동시 확인
cmux browser screenshot --out /tmp/debug.png
cmux browser snapshot --compact
cmux browser console list
cmux browser errors list
```

### 폼 자동 채우기

```bash
cmux browser fill "input[name=email]" "test@example.com"
cmux browser fill "input[name=password]" "password123"
cmux browser click "button[type=submit]" --snapshot-after
```

### 클린 세션으로 테스트

```bash
# 전체 초기화
cmux browser cookies clear --all && cmux browser storage local clear && cmux browser storage session clear
# 새로 시작
cmux browser goto https://example.com
```
