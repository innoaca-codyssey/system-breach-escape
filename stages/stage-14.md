# 14. 거쳐 간 정거장

- 힌트: 도착지엔 아무것도 없다. 거쳐 간 곳의 헤더를 봐.

## 관찰

화면에 "hop: ARRIVED"만 표시된다. Network 패널을 보면 `hop/1 → hop/2 → hop/3 → hop/end` 순서로 네 개의 요청이 찍혀 있다.

## 확인 과정

먼저 브라우저 스크립트(`fetch`)로 각 hop을 직접 호출해봤지만, 어떤 hop을 호출하든 결과는 똑같이 다음 본문만 돌아왔다.

```json
{"ok":true,"message":"ARRIVED"}
```

`fetch`의 `response.redirected`, `response.url`을 확인해 보면 `hop/1`, `hop/2`, `hop/3` 모두 최종적으로 `hop/end`로 리다이렉트(302)된 뒤의 결과라는 걸 알 수 있었다. 문제는 브라우저의 fetch/XHR API가 보안상 리다이렉트 응답 자체(302 응답과 그 헤더)는 스크립트에 절대 넘겨주지 않는다는 점이다(`redirect:'manual'`로 시도해도 `opaqueredirect`라는 내용이 비어있는 응답만 받는다).

그래서 브라우저를 거치지 않고 터미널에서 curl로 각 hop을 개별적으로, 리다이렉트를 따라가지 않는 옵션(`--max-redirs 0`)으로 요청해 302 응답 자체를 직접 읽었다. 이때 필요한 세션 쿠키(`access=unlocked; vault_key=GATE-D5B8`, 9번 스테이지에서 확보)를 같이 실어 보냈다.

```
curl -D - -o /dev/null -b "access=unlocked; vault_key=GATE-D5B8" --max-redirs 0 https://escape.bsh00.com/api/hop/1
→ location: /api/hop/2
→ x-hop-part: 1/3 HOP-

curl ... /api/hop/2
→ location: /api/hop/3
→ x-hop-part: 2/3 6D

curl ... /api/hop/3
→ location: /api/hop/end
→ x-hop-part: 3/3 44
```

세 응답의 `x-hop-part` 헤더에 코드가 1/3, 2/3, 3/3로 나뉘어 실려 있었다. 순서대로 이어 붙이면 완성된다: `HOP-` + `6D` + `44`.

## 코드

`HOP-6D44`
