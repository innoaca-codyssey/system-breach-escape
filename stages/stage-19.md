# 19. 잘린 이름

- 힌트: 네가 보낸 글자와 서버가 받은 글자가 같은지부터 확인해.

## 관찰

화면에 다음과 같이 표시된다.

```
channel: core 01&alt#7
```

`OPEN` 버튼이 하나 있다.

## 확인 과정

`OPEN` 버튼을 누르고 Network 패널을 확인하면 실제로 전송된 요청은 다음과 같았다.

```
GET /api/channel-name?name=core%2001&alt
```

의도한 채널 이름은 `core 01&alt#7`인데, 이 문자열을 URL 인코딩 없이 그대로 쿼리 파라미터에 넣어 보내는 바람에 `&`가 새 파라미터 구분자로 해석되어 `name` 값이 `core 01`에서 잘리고, 뒤에 별개의 빈 파라미터 `alt`가 붙어버렸다. `#` 이후의 `7`은 URL 프래그먼트로 취급돼 아예 서버로 전송되지도 않았다. 즉 "내가 의도한 글자"와 "서버가 실제로 받은 글자"가 달랐던 것이 힌트의 정확한 의미였다.

의도한 문자열 전체(`core 01&alt#7`)를 값 그대로 퍼센트 인코딩해서(`&`, `#`까지 전부 인코딩되도록) 다시 요청했다.

```
curl -b "access=unlocked; vault_key=GATE-D5B8" -G --data-urlencode "name=core 01&alt#7" https://escape.bsh00.com/api/channel-name
```

```json
{"ok":true,"code":"FIND-4B21"}
```

## 코드

`FIND-4B21`
