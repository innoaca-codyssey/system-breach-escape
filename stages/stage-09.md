# 09. 접근 차단

- 힌트: Application 패널의 Cookies 를 봐.

## 관찰

화면에 "vault: ACCESS DENIED"만 표시된다.

## 확인 과정

Application 패널의 Cookies(또는 `document.cookie`)를 확인하면 다음과 같다.

```
access=locked
```

이 쿠키 값을 브라우저에서 직접 바꿀 수 있는지 시험해봤다.

```js
document.cookie = 'access=unlocked; path=/';
```

쿠키를 바꾼 뒤 같은 페이지를 새로고침하면 화면이 "vault: UNLOCKED"로 바뀌고, 쿠키에 새 항목이 하나 추가된다.

```
access=unlocked; vault_key=GATE-D5B8
```

(이번에도 `document.cookie`를 그대로 출력하면 콘솔 출력이 걸러지는 현상이 있어서, 문자열을 한 글자씩 문자 코드로 변환해 출력한 뒤 직접 디코딩했다.)

이 `vault_key=GATE-D5B8` 값은 이후 14번(hop 인증), 27번(프리플라이트 우회) 스테이지에서 다시 쓰인다.

## 코드

`GATE-D5B8`
