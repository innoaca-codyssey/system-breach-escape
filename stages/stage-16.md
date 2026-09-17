# 16. 공개된 지도

- 힌트: 사람에겐 숨긴 길을, 크롤러에겐 적어 두었다.

## 관찰

화면에 "archive: locked"만 표시된다.

## 확인 과정

"크롤러에겐 적어 두었다"는 문구를 보고 `robots.txt`부터 확인했다.

```
curl https://escape.bsh00.com/robots.txt
```

```
User-agent: *
Disallow: /api/archive-7c1
```

검색엔진 크롤러에게 "가지 말라"고 알려주는 `Disallow` 항목이 오히려 숨겨진 경로를 그대로 알려주는 셈이었다. 그 경로를 직접 요청했다.

```
curl -b "access=unlocked; vault_key=GATE-D5B8" https://escape.bsh00.com/api/archive-7c1
```

```json
{"ok":true,"code":"ROBO-4AA4"}
```

## 코드

`ROBO-4AA4`
