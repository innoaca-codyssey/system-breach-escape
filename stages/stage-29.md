# 29. 되돌리는 손

- 힌트: 누가 자꾸 되돌려 놓는다. Elements 에서 그 요소를 우클릭해봐.

## 관찰

화면에 `seal: LOCKED`(내부적으로 `<span id="seal" data-locked="yes">`)와 `CHECK seal` 버튼, "idle" 문구가 있다.

## 확인 과정

스크립트(`/stages/s17/script.js`)를 확인하면 다음과 같았다.

```js
let sentinelActive = true;

function watcher() {
  if (!sentinelActive) return;
  if (seal.dataset.locked !== 'yes') {
    seal.dataset.locked = 'yes';
    seal.textContent = 'LOCKED';
  }
}
setInterval(watcher, 300);   // 0.3초마다 강제로 되돌린다

document.querySelector('#check-btn').addEventListener('click', async () => {
  const state = seal.dataset.locked === 'no' ? 'released' : 'held';
  const data = await (await fetch(`/api/watch?seal=${state}`)).json();
  ...
});
```

Elements 패널에서 `data-locked` 속성을 손으로 `no`로 바꿔봐도, 0.3초짜리 감시 타이머가 곧바로 다시 `yes`로 되돌려놓기 때문에 손으로 값을 바꾸고 버튼을 누르는 타이밍을 맞추기가 매우 어렵다(사실상 경쟁 조건). 하지만 `CHECK seal` 버튼이 실제로 하는 일은 결국 DOM 상태를 읽어 `/api/watch?seal=...`를 호출하는 것뿐이었다. DOM을 건드리는 대신 이 API를 감시 로직과 무관하게 바로 호출했다.

```js
await (await fetch('/api/watch?seal=released')).json();
```

```json
{"ok":true,"code":"WTCH-7B62"}
```

화면의 감시 로직은 DOM 속성만 되돌릴 뿐, 서버로 보내는 실제 요청 자체를 막지는 못했다.

## 코드

`WTCH-7B62`
