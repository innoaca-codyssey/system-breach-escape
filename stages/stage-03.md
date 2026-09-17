# 03. 시스템 경고

- 힌트: Console 패널을 열어봐.

## 관찰

화면에는 "STATUS: nominal"만 표시된다. 화면 텍스트 자체에는 코드가 없다.

## 확인 과정

Console 패널을 열어보면 페이지 로드 시점에 다음 경고 로그가 찍혀 있다.

```
[WARNING] (stages/s04/script.js:2:8)
[SYS] integrity check failed

[WARNING] (stages/s04/script.js:5:10)
[SYS] override code = WARN-58EE
```

두 번째 경고가 3초 간격으로 반복 출력되는데, 매번 같은 값(`WARN-58EE`)이 찍힌다. 화면에는 의도적으로 노출하지 않고 콘솔에만 남기는 패턴이다.

## 코드

`WARN-58EE`
