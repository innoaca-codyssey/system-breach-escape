# 12. 되돌아온 요청

- 힌트: 같은 요청을 조금 바꿔서 다시 보내봐.

## 관찰

화면에 "relay: NODE REFUSED"만 표시된다.

## 확인 과정

Network 패널에서 `GET /api/relay?node=relay-03` 요청을 확인했다. 같은 요청을 직접 호출해 본문을 확인하면 다음과 같다.

```json
{"ok":false,"message":"NODE REFUSED","known":["relay-03","core-01"]}
```

거부 응답이 친절하게도 `known` 목록으로 사용 가능한 다른 노드 이름(`core-01`)을 알려준다. 파라미터만 바꿔서 같은 요청을 다시 보냈다.

```js
await (await fetch('/api/relay?node=core-01')).json();
```

```json
{"ok":true,"code":"RSND-5C83"}
```

이 코드(`RSND-5C83`)는 30번 최종 보스 단계에서 열쇠 중 하나로 다시 필요하다.

## 코드

`RSND-5C83`
