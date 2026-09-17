# 18. 건너편 금고 (CORS)

- 힌트: 막힌 건 브라우저 안의 JS 다. 서버는 이미 대답했다.

## 관찰

화면에 "vault: blocked"라고 표시된다.

## 확인 과정

Network 패널을 보면 두 요청이 있다.

```
GET https://escape.bsh00.com/api/vault-origin   → 200
GET https://vault.bsh00.com/api/vault-note       → (브라우저에서 실패로 표시)
```

두 번째 요청은 `escape.bsh00.com` 페이지가 다른 출처인 `vault.bsh00.com`으로 보내는 교차 출처(cross-origin) 요청인데, 응답에 이 페이지의 출처를 허용하는 CORS 헤더가 없어서 브라우저가 응답을 스크립트에 넘겨주지 않고 막아버린 상태였다. 즉 서버는 이미 정상적으로 응답을 보냈는데(힌트 그대로), 브라우저만 그 응답을 화면에 전달하지 않은 것이다.

브라우저의 CORS 정책은 브라우저 자체의 규칙이라, 브라우저를 거치지 않고 터미널에서 같은 주소를 직접 요청하면 아무 제약 없이 응답을 받을 수 있다.

```
curl -b "access=unlocked; vault_key=GATE-D5B8" https://vault.bsh00.com/api/vault-note
```

```json
{"ok":true,"code":"CORS-FE6C"}
```

## 코드

`CORS-FE6C`
