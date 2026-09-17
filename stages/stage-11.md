# 11. 남의 서랍 (IDOR)

- 힌트: id 는 내 프로필만 열 수 있을까? 다른 주인(peer)의 것도 물어봐.

## 관찰

화면에 다음과 같이 표시된다.

```
profile: id=me role=guest peers=me,admin
```

`peers` 목록에 `me` 말고 `admin`이라는 다른 사용자 id가 노출돼 있다.

## 확인 과정

Network 패널을 보면 페이지가 로드되며 `GET /api/profile?id=me` 요청을 보낸 걸 알 수 있다. `id` 파라미터가 그대로 클라이언트에서 결정되는 값이라, 다른 사용자 id로 바꿔서 같은 엔드포인트를 다시 호출해봤다.

```js
await (await fetch('/api/profile?id=admin')).json();
```

```json
{"ok":true,"id":"admin","role":"root","code":"IDOR-9A3F"}
```

서버가 요청자의 실제 권한과 무관하게 `id` 파라미터에 적힌 대상의 정보를 그대로 돌려준다 — 전형적인 IDOR(Insecure Direct Object Reference)이다.

## 코드

`IDOR-9A3F`
