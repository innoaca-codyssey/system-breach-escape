# 05. 숨은 채널

- 힌트: Network 패널에서 요청의 응답 본문을 봐.

## 관찰

화면에는 "channel: relay-03 (ok)"만 표시된다.

## 확인 과정

Network 패널에서 `GET /api/channel` 요청을 확인하고, 같은 요청을 `fetch('/api/channel')`로 다시 호출해 응답 바디를 그대로 읽었다.

```json
{"status":"ok","node":"relay-03","fragment":"CHAN-9E63"}
```

화면에는 `status`와 `node`만 그대로 옮겨 보여주고, `fragment` 필드는 화면에 그리지 않은 채 응답 안에만 남겨둔 케이스였다.

## 코드

`CHAN-9E63`
