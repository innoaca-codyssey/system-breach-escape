# 17. 수많은 문

- 힌트: 열린 문이 하나 있다. 그 문만 따로 열어봐.

## 관찰

화면에 "doors: 300"이라고 표시된다. 문이 300개 있다는 뜻으로 보인다.

## 확인 과정

Network 패널에서 `GET /api/doors` 요청을 확인하고 직접 호출해봤다. 응답은 id 0~299번 문 각각의 상태(`sealed`/`open`)를 담은 배열이었다.

```json
{"doors":[{"id":0,"state":"sealed"}, ... ,{"id":187,"state":"open"}, ... ,{"id":299,"state":"sealed"}]}
```

300개 중 딱 하나(id=187)만 `"state":"open"`이었다. 그 문 번호로 개별 엔드포인트를 호출했다.

```
curl -b "access=unlocked; vault_key=GATE-D5B8" https://escape.bsh00.com/api/doors/187
```

```json
{"ok":true,"id":187,"key":"QRY-2E19"}
```

## 코드

`QRY-2E19`
