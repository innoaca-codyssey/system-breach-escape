# 25. 자칭 신분

- 힌트: 서버는 네가 보내는 User-Agent 를 믿어. 브라우저 JS 로는 못 바꾸는 헤더는 curl 로 위조해봐.

## 관찰

화면에 "inner: UNKNOWN AGENT"라고 표시된다.

## 확인 과정

스크립트는 단순히 `fetch('/api/inner')`만 호출한다. `User-Agent`는 브라우저가 강제로 관리하는 "금지된 헤더"라서 페이지 스크립트가 값을 바꿔 보낼 수 없고, 서버는 이 헤더 값으로 요청자를 판별하고 있었다.

문제는 정확히 어떤 값을 기대하는지였다. curl로 다양한 값을 `-A` 옵션으로 바꿔가며 시도해봤지만(내부 서비스명, 잘 알려진 봇 이름, 자체 개발 도구 UA 등) 전부 다음과 같이 거부됐다.

```json
{"ok":false,"message":"UNKNOWN AGENT","need":"User-Agent"}
```

브라우저에서 실제 로그인 세션과 진짜 Chrome User-Agent로 그대로 요청해도 똑같이 거부되는 걸 확인하고서, 무작위로 값을 추측하는 대신 이 스테이지의 원본 HTML을 처음부터 끝까지 다시 읽어봤다. `script` 태그만 보고 넘어갔던 부분에 HTML 주석이 남아 있었다.

```html
<!-- inner ring trusts only the agent name: escape-core -->
```

이 값을 그대로 `User-Agent`로 위장해 curl로 요청했다.

```
curl -A "escape-core" https://escape.bsh00.com/api/inner
```

```json
{"ok":true,"code":"HDR-6C1D"}
```

값을 추측하지 않고 페이지 원본에 남은 힌트를 끝까지 읽었어야 하는 단계였다.

## 코드

`HDR-6C1D`
