# 07. 잠긴 실행

- 힌트: Elements 패널에서 버튼 태그에 붙은 속성을 봐.

## 관찰

화면에 `RUN recover.sh` 버튼 하나만 있고, 클릭해도 아무 반응이 없다.

## 확인 과정

Elements 패널에서 해당 버튼 태그를 확인하면 다음과 같다.

```html
<button id="exec-btn" disabled="">RUN recover.sh</button>
```

`disabled` 속성 때문에 클릭 이벤트 자체가 브라우저 단에서 막혀 있다. Elements 패널에서 이 속성을 지우면(또는 스크립트로 `removeAttribute('disabled')` + `disabled = false` 처리) 버튼이 정상적으로 눌리는 상태가 된다.

속성을 지운 뒤 버튼을 클릭하면 화면 텍스트가 다음과 같이 바뀐다.

```
recover.sh → EXEC-3D91
```

## 코드

`EXEC-3D91`
