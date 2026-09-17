# 27. 먼저 묻는 편지 (CORS 프리플라이트)

- 힌트: 본 요청은 떠나지도 않았다. 먼저 보낸 질문의 답장을 읽어라.

## 관찰

화면에 "seal: rejected"라고 표시된다.

## 확인 과정

스크립트(`/stages/s25/script.js`)를 확인하면 다음과 같이 되어 있었다.

```js
const { vault } = await (await fetch('/api/vault-origin')).json();
const data = await (await fetch(`${vault}/api/vault-seal`, {
  headers: { 'X-Seal': 'guest' },
})).json();
```

다른 출처(`vault.bsh00.com`)로 커스텀 헤더(`X-Seal`)를 실어 보내는 요청은, 실제 요청을 보내기 전에 브라우저가 먼저 "이 헤더를 보내도 되는지" 서버에 사전 질의(OPTIONS 프리플라이트)를 보낸다. 힌트의 "본 요청은 떠나지도 않았다"는 바로 이 뜻이다 — 프리플라이트가 거절당하면 실제 GET 요청은 브라우저 밖으로 나가지도 못하고 곧바로 실패 처리된다.

그 프리플라이트에 대한 답장을 직접 확인해봤다.

```
curl -D - -X OPTIONS \
  -H "Origin: https://escape.bsh00.com" \
  -H "Access-Control-Request-Method: GET" \
  -H "Access-Control-Request-Headers: x-seal" \
  https://vault.bsh00.com/api/vault-seal
```

```
access-control-allow-headers: X-Vault-Key
access-control-allow-methods: GET
access-control-allow-origin: https://escape.bsh00.com
```

서버가 실제로 허용하는 헤더 이름은 `X-Seal`이 아니라 `X-Vault-Key`였다. 즉 스크립트가 보내려는 헤더 이름 자체가 애초에 잘못돼 있어서 프리플라이트 단계에서부터 거절당하고 있었던 것이다. 허용된 헤더 이름과, 9번 스테이지에서 이미 확보해둔 `vault_key` 값을 그대로 사용해 직접 요청했다.

```
curl -H "X-Vault-Key: GATE-D5B8" https://vault.bsh00.com/api/vault-seal
```

```json
{"ok":true,"code":"PREF-5927"}
```

## 코드

`PREF-5927`
