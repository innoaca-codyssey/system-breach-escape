# 02. 지워진 로그

- 힌트: Elements 패널에서 로그 목록을 하나씩 훑어봐.

## 관찰

화면에 시간순 로그 4줄이 나열되어 있다.

```
[08:12] session opened
[08:13] privilege escalated
[08:14] recovery key = LOGS-B4C6
[08:15] logs purged
```

## 확인 과정

제목은 "지워진 로그"이지만 실제로 로그가 지워진 건 화면 문구뿐이고, DOM에는 삭제되기 전 로그 4줄이 그대로 남아있다. 화면을 눈으로 훑기만 해도 세 번째 줄에 `recovery key = LOGS-B4C6`가 그대로 적혀 있다. 별도의 콘솔, 네트워크 조작 없이 Elements(또는 렌더링된 페이지 텍스트) 확인만으로 끝나는 단계였다.

## 코드

`LOGS-B4C6`
