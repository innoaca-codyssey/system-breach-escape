# 22. 거짓 검증

- 힌트: 막는 쪽은 브라우저다. 서버는 뭘 받아줄까?

## 관찰

화면에 `SERIAL (4자)`라고 표시된 입력창과 `전송` 버튼이 있고, "awaiting serial" 문구가 보인다.

## 확인 과정

이 단계의 스크립트(`/stages/s14/script.js`)를 받아보면 다음과 같은 검증 로직이 있었다.

```js
if (!/^[A-Z0-9]{4}$/.test(serial)) {
  out.textContent = 'serial must be 4 chars';
  return;   // 여기서 막히면 fetch 자체가 나가지 않는다
}
await fetch('/api/submit-form', { method:'POST', body: JSON.stringify({ serial }) });
```

즉 "4자만 입력 가능"이라는 제약은 순전히 이 페이지의 자바스크립트가 요청을 보내기 전에 걸어둔 검사일 뿐이었다. 서버가 실제로 뭘 받아주는지 알아보려고, 이 검사를 거치지 않고 `/api/submit-form`에 직접 다양한 값을 POST 해봤다.

```
curl -X POST -H "Content-Type: application/json" -d '{"serial":"TEST"}' https://escape.bsh00.com/api/submit-form
→ {"ok":false,"message":"SERIAL REJECTED","expected":"9자 이상, '-' 포함"}

curl ... -d '{"serial":"AAAAAAAAAA"}'
→ 역시 같은 메시지로 거부
```

서버의 거부 메시지 자체가 실제 요구 조건(9자 이상, `-` 포함)을 그대로 알려줬다. 그 조건에 맞는 형식으로 다시 보냈다.

```
curl -X POST -H "Content-Type: application/json" -d '{"serial":"ABCD-1234"}' https://escape.bsh00.com/api/submit-form
```

```json
{"ok":true,"code":"BYPS-6A30"}
```

## 코드

`BYPS-6A30`
