# 08. 봉인 함수

- 힌트: Console 패널에서 window 에 뭐가 붙어 있는지 봐.

## 관찰

화면에는 "unlockSeal"이라는 이름 하나만 떠 있고, 버튼도 입력창도 없다.

## 확인 과정

이름만 보고 전역 스코프에 같은 이름의 함수가 있는지 콘솔에서 확인했다.

```js
typeof window.unlockSeal   // "function"
```

페이지 스크립트가 `unlockSeal`이라는 함수를 전역(`window`) 객체에 그대로 노출해 두고 있었다. 콘솔에서 직접 호출하면 반환값이 코드다.

```js
window.unlockSeal()   // "FUNC-A017"
```

## 코드

`FUNC-A017`
