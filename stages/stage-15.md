# 15. 오래된 사본

- 힌트: 서버는 "바뀐 게 없다"고만 했다. 조건 없이 물어보면?

## 관찰

화면에 "snapshot: 304 not modified"라고 표시된다.

## 확인 과정

페이지가 부르는 `/api/snapshot`은 조건부 GET(캐시 유효성 검사, `If-None-Match`/`ETag` 방식)으로 설계돼 있어서, 브라우저가 이전에 받은 `ETag`를 그대로 다시 실어 보내면 서버가 "달라진 게 없다(304)"는 뜻으로 빈 응답을 준다. 그래서 화면에는 항상 "304 not modified"만 보였다.

캐시 조건(If-None-Match 등) 없이 처음 요청하듯 순수하게 다시 GET을 날려봤다.

```
curl -b "access=unlocked; vault_key=GATE-D5B8" https://escape.bsh00.com/api/snapshot
```

```json
{"ok":true,"code":"ETAG-4F78"}
```

응답 헤더에는 `etag: "snap-v1"`, `cache-control: no-cache`가 붙어 있었지만, 조건부 헤더를 실어 보내지 않았기 때문에 304가 아니라 신선한 200 응답과 함께 코드가 왔다.

## 코드

`ETAG-4F78`
