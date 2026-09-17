# 30. 최종 보스

- 힌트: 열쇠가 맞아도 한 번 더 묻는다. 거부 응답이 무엇을 요구하는지 그대로 읽고, 같은 요청에 얹어 다시 보내라.

## 관찰

화면에 다음과 같이 표시된다.

```
three keys required: 21 · 22 · 12
boss: KEYS REJECTED
```

앞서 21번, 22번, 12번 스테이지에서 얻은 코드 세 개가 열쇠로 필요하다는 뜻이다.

- 21번(스쳐가는 값): `BRK-1D58`
- 22번(거짓 검증): `BYPS-6A30`
- 12번(되돌아온 요청): `RSND-5C83`

## 확인 과정

스크립트(`/stages/s20/script.js`)는 `GET /api/boss?a=&b=&c=` 형태로 3초마다 상태를 폴링하고 있었다. `a`, `b`, `c`에 위 세 코드를 순서대로 채워 직접 요청했다.

```
curl -G \
  --data-urlencode "a=BRK-1D58" \
  --data-urlencode "b=BYPS-6A30" \
  --data-urlencode "c=RSND-5C83" \
  https://escape.bsh00.com/api/boss
```

```json
{"ok":false,"message":"PROVE LIVENESS","challenge":"39d198","proof":"base64(challenge:a:b:c)"}
```

열쇠 세 개는 이미 정답으로 인정됐지만("KEYS REJECTED"가 아니라 한 단계 더 나아간 요구가 왔다), 서버는 곧바로 통과시키지 않고 "살아있는 요청인지 증명하라"며 `challenge` 값과 함께 요구하는 `proof`의 정확한 형식(`challenge:a:b:c`를 base64로 인코딩한 값)을 그대로 응답에 적어줬다. 거부 메시지가 곧 다음에 뭘 보내야 하는지 알려주는 사양이었던 셈이다.

지시받은 형식 그대로 `proof`를 만들었다.

```bash
echo -n "39d198:BRK-1D58:BYPS-6A30:RSND-5C83" | base64
# → MzlkMTk4OkJSSy0xRDU4OkJZUFMtNkEzMDpSU05ELTVDODM=
```

받은 `challenge` 값과 방금 만든 `proof`를 같은 요청 파라미터에 얹어 다시 보냈다.

```
curl -G \
  --data-urlencode "a=BRK-1D58" \
  --data-urlencode "b=BYPS-6A30" \
  --data-urlencode "c=RSND-5C83" \
  --data-urlencode "challenge=39d198" \
  --data-urlencode "proof=MzlkMTk4OkJSSy0xRDU4OkJZUFMtNkEzMDpSU05ELTVDODM=" \
  https://escape.bsh00.com/api/boss
```

```json
{"ok":true,"code":"BOSS-0F15"}
```

이 코드를 제출하면서 30단계 전체가 클리어됐고, `/clear` 완료 화면(ESCAPED)으로 이동했다.

## 코드

`BOSS-0F15`
