# 10. 권한 위조

- 힌트: Application 패널의 Local Storage 를 봐.

## 관찰

화면에 "role=user — DENIED"라고 표시된다.

## 확인 과정

Application 패널의 Local Storage를 확인하면 `role` 키에 `user` 값이 저장돼 있다.

```js
localStorage.getItem('role')   // "user"
```

서버가 이 값을 그대로 신뢰하고 있을 가능성이 있어 `admin`으로 바꿔봤다.

```js
localStorage.setItem('role', 'admin');
```

값을 바꾼 뒤 페이지를 새로고침하면 화면이 다음과 같이 바뀐다.

```
ADMIN CONSOLE — ROLE-1A7F
```

## 코드

`ROLE-1A7F`
