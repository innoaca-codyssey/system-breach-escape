# 20. 뭉개진 코드

- 힌트: 읽을 수 있는 모양으로 펴서 봐.

## 관찰

화면에 "packed (5 segments, rev 7)"이라고만 표시된다.

## 확인 과정

이 스테이지는 API 호출이 전혀 없어서, 페이지 자체의 원본 HTML을 직접 받아봤다.

```
curl -b "access=unlocked; vault_key=GATE-D5B8" https://escape.bsh00.com/stage/20?token=...
```

HTML 맨 아래에 두 스크립트가 걸려 있었다.

```html
<script type="module" src="/stages/s12/script.js"></script>
<script src="/stages/s12/packed.js"></script>
```

`packed.js`를 받아보면 축약된 즉시실행함수 안에 base64 문자열 배열이 들어있다.

```js
var q=["Rk1U","OUM3RQ==","S0dCQg==","WldGclpRPT0=","c2VhbA=="];
var z=function(s){ return atob(s) };
var k=function(a,b){ return z(q[a])+"-"+z(q[b]) };
var m={seg:q.length, mode:"packed", rev:7};
var v=function(){ var c=k(0,1); ...; return c };
```

배열 5개를 각각 base64 디코딩했다.

```
Rk1U           → FMT
OUM3RQ==       → 9C7E
S0dCQg==       → KGBB
WldGclpRPT0=   → ZWFrZQ==  (한 번 더 디코딩하면 "eake")
c2VhbA==       → seal
```

화면에 보이던 "5 segments"는 이 5개 배열 원소를 뜻했고, 스크립트 안에서 실제로 코드를 조립하는 함수(`v()`)는 인덱스 0번과 1번만 이어붙이도록 짜여 있었다(`k(0,1)`). 나머지 세 조각(KGBB, eake, seal)은 사용되지 않는 미끼 값이었다. 0번과 1번을 이어 붙이면 코드 형식(`XXXX-XXXX`)과 정확히 맞아떨어졌다.

```
FMT + "-" + 9C7E = FMT-9C7E
```

## 코드

`FMT-9C7E`
