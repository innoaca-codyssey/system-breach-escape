# 24. 위장한 사진

- 힌트: 서버는 봉투에 적힌 종류만 본다. 내용은 열어보지 않는다.

## 관찰

화면에 파일 선택 버튼과 `업로드` 버튼이 있고, "upload: idle"이라고 표시된다.

## 확인 과정

스크립트(`/stages/s30/script.js`)를 확인하면 업로드 전에 클라이언트가 파일 내용을 직접 검사하고 있었다.

```js
const PNG_SIGNATURE = [0x89,0x50,0x4e,0x47,0x0d,0x0a,0x1a,0x0a]; // PNG 매직 바이트

async function looksLikePng(file) {
  const head = new Uint8Array(await file.slice(0, 8).arrayBuffer());
  return PNG_SIGNATURE.every((b, i) => head[i] === b);
}

// 업로드 버튼 클릭 시:
if (!(await looksLikePng(file))) {
  out.textContent = 'upload: not a png';
  return;   // 여기서 막히면 /api/upload 요청 자체가 나가지 않는다
}
await fetch('/api/upload', {
  method: 'POST',
  headers: { 'Content-Type': file.type || 'application/octet-stream' },
  body: await file.arrayBuffer(),
});
```

즉 "PNG인지 아닌지"는 파일을 고르는 그 순간 브라우저 스크립트가 바이트를 직접 읽어 검사하고, 아니면 요청을 아예 보내지도 않는다. 파일 선택 UI를 거치지 않고 콘솔에서 곧바로 `/api/upload`를 호출해, 실제 PNG가 아닌 임의의 텍스트에 `Content-Type: image/png` 헤더만 붙여 보냈다.

```js
await (await fetch('/api/upload', {
  method: 'POST',
  headers: { 'Content-Type': 'image/png' },
  body: new Blob(['not a real png']),
})).json();
```

```json
{"ok":true,"code":"MIME-4A49"}
```

서버는 실제 바이트(매직 넘버)를 검사하지 않고, 클라이언트가 스스로 신고한 `Content-Type` 헤더값(봉투에 적힌 종류)만 보고 통과시켰다.

## 코드

`MIME-4A49`
