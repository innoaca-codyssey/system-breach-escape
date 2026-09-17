# 13. 멋대로 이정표 (오픈 리다이렉트)

- 힌트: 이 이정표(next)는 목적지를 검사하지 않아. 사이트 밖으로도 보낼 수 있다면?

## 관찰

화면에 "signpost: ready"와 `FOLLOW` 버튼이 있다.

## 확인 과정

버튼을 눌러보면 화면이 "signpost: INTERNAL (/home)"으로 바뀌고, Network 패널에 다음 요청이 찍힌다.

```
GET /api/go?next=%2Fhome
```

`next` 파라미터가 이동할 목적지를 그대로 받는 구조였다. 서버가 이 값을 검증하지 않는다면 사이트 밖 임의의 주소도 넣을 수 있을 거라 보고 직접 호출해봤다.

```js
await (await fetch('/api/go?next=' + encodeURIComponent('https://example.com'))).json();
```

```json
{"ok":true,"code":"OPRD-4E82","to":"https://example.com"}
```

목적지 검증(허용 목록, 같은 출처 확인 등) 없이 아무 URL이나 `next`에 넣으면 그대로 통과되는 오픈 리다이렉트였다.

## 코드

`OPRD-4E82`
