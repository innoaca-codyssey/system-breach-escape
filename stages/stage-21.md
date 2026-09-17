# 21. 스쳐가는 값

- 힌트: 값은 함수 안에서만 잠깐 존재한다.

## 관찰

화면에 "idle"과 `RUN derive()` 버튼이 있다. 버튼을 누르면 "derived 8 chars"로 바뀔 뿐, 실제 문자열은 보여주지 않고 글자 수만 알려준다.

## 확인 과정

이 스테이지의 스크립트(`/stages/s13/script.js`)를 직접 받아 로직을 확인했다.

```js
const { seed } = await (await fetch('/api/seed')).json();

function derive() {
  const suffix = seed.toString(16).toUpperCase();
  const code = `BRK-${suffix}`;
  return code.length;   // 버튼은 이 length만 화면에 보여준다
}
```

`code` 변수는 함수 안에서 만들어졌다가 함수가 끝나면 사라지는 지역 변수라, 화면에는 절대 값 자체가 노출되지 않고 길이(8)만 나온다. 하지만 이 값을 만드는 계산식 자체는 스크립트 파일에 그대로 공개돼 있으므로, 같은 계산을 직접 해보면 된다.

```
curl -b "access=unlocked; vault_key=GATE-D5B8" https://escape.bsh00.com/api/seed
```

```json
{"seed":7512}
```

`7512`를 16진수로 변환하면 `1D58`이다 (7512 = 1×4096 + 13×256 + 5×16 + 8, 13은 16진수로 D). 스크립트의 계산식대로 `BRK-` 접두어를 붙이면 코드가 나온다.

## 코드

`BRK-1D58`
