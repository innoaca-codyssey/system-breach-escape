# SYSTEM BREACH — escape.bsh00.com 워게임 풀이

## 스테이지

| # | 제목 | 방법 요약 | 코드 | 상세 |
|---|---|---|---|---|
| 01 | 멈춘 부팅 | 로딩 스피너가 화면을 덮고 있지만, 그 아래 실제 DOM에는 코드 입력 폼과 코드(`BOOT-7F2A`)가 이미 렌더링되어 있다. 로딩 오버레이는 시각적 가림막일 뿐이라 Elements 패널로 하단 요소를 확인하면 바로 코드를 읽을 수 있다. | BOOT-7F2A | [stage-01](stages/stage-01.md) |
| 02 | 지워진 로그 | 오버레이 밑 로그 목록에 `recovery key = LOGS-B4C6` 줄이 그대로 남아있다. | LOGS-B4C6 | [stage-02](stages/stage-02.md) |
| 03 | 시스템 경고 | Console에 `[SYS] override code = WARN-58EE` 경고 로그가 출력된다. | WARN-58EE | [stage-03](stages/stage-03.md) |
| 04 | 은닉 신호 | 화면에 보이는 값이 아니라, 폴링 중인 `/api/signal` 응답의 `x-unlock` 응답 헤더에 코드가 실려 있다. | SGNL-2C4D | [stage-04](stages/stage-04.md) |
| 05 | 숨은 채널 | `/api/channel` 응답 JSON 바디의 `fragment` 필드에 코드가 담겨 있다. | CHAN-9E63 | [stage-05](stages/stage-05.md) |
| 06 | 흩어진 조각 | 화면 텍스트는 `EXIT-` 접두어만 보여주고, 나머지 네 자리는 sessionStorage의 `sys_fragment` 값(`FFFF`)에 들어있다. 둘을 합치면 완성된다. | EXIT-FFFF | [stage-06](stages/stage-06.md) |
| 07 | 잠긴 실행 | `RUN recover.sh` 버튼에 `disabled` 속성이 걸려 있어 클릭이 씹힌다. Elements에서 그 속성을 지우면 버튼이 눌리고, 실행 결과로 코드가 출력된다. | EXEC-3D91 | [stage-07](stages/stage-07.md) |
| 08 | 봉인 함수 | 화면에는 `unlockSeal`이라는 이름만 보이는데, 실제로는 전역(`window`)에 같은 이름의 함수가 노출되어 있다. Console에서 직접 호출하면 반환값이 코드다. | FUNC-A017 | [stage-08](stages/stage-08.md) |
| 09 | 접근 차단 | `document.cookie`에 `access=locked` 쿠키가 있다. 값을 `unlocked`로 바꾸고 새로고침하면 금고가 열리며 쿠키에 `vault_key=GATE-D5B8`가 추가로 실린다. | GATE-D5B8 | [stage-09](stages/stage-09.md) |
| 10 | 권한 위조 | localStorage의 `role` 값이 `user`로 저장되어 있고 서버는 이 값을 그대로 신뢰한다. `admin`으로 바꾸고 새로고침하면 관리자 콘솔이 열린다. | ROLE-1A7F | [stage-10](stages/stage-10.md) |
| 11 | 남의 서랍 (IDOR) | 화면에 `peers=me,admin`이라는 힌트가 있다. `/api/profile?id=me`를 그대로 `id=admin`으로 바꿔 요청하면 접근 제어 없이 다른 사람의 프로필이 그대로 반환된다. | IDOR-9A3F | [stage-11](stages/stage-11.md) |
| 12 | 되돌아온 요청 | `/api/relay?node=relay-03`은 거부되며 응답에 `known:["relay-03","core-01"]` 목록을 알려준다. 알려준 다른 노드(`core-01`)로 같은 요청을 다시 보내면 성공한다. | RSND-5C83 | [stage-12](stages/stage-12.md) |
| 13 | 멋대로 이정표 (오픈 리다이렉트) | 화면의 `FOLLOW` 버튼은 내부적으로 `/api/go?next=/home`을 호출한다. `next` 값에 목적지 검증이 없어서, 사이트 밖의 임의 URL로 바꿔 요청해도 그대로 통과된다. | OPRD-4E82 | [stage-13](stages/stage-13.md) |
| 14 | 거쳐 간 정거장 | 요청이 `hop/1 → hop/2 → hop/3 → end` 순으로 302 리다이렉트를 거치는데, 브라우저의 fetch는 리다이렉트 응답 자체의 헤더를 스크립트에 숨긴다. curl로 각 hop을 개별적으로(`--max-redirs 0`) 요청해 302 응답 헤더를 직접 읽으면, `x-hop-part` 헤더에 코드 조각이 1/3, 2/3, 3/3로 나뉘어 있다. 이를 순서대로 이어 붙인다. | HOP-6D44 | [stage-14](stages/stage-14.md) |
| 15 | 오래된 사본 | 화면은 조건부 캐시 요청(If-None-Match 방식) 때문에 매번 "304 not modified"만 본다. 캐시 조건 없이 `/api/snapshot`을 직접 요청하면 신선한 200 응답과 함께 코드가 온다. | ETAG-4F78 | [stage-15](stages/stage-15.md) |
| 16 | 공개된 지도 | 사람에게는 안 보이지만 크롤러용으로 `robots.txt`의 Disallow 항목에 숨겨진 경로가 적혀 있다. 그 경로를 그대로 요청하면 코드가 나온다. | ROBO-4AA4 | [stage-16](stages/stage-16.md) |
| 17 | 수많은 문 | 문 300개 중 하나만 열려 있다는 설정. `/api/doors` 목록을 통째로 받아보면 각 문의 상태(`sealed`/`open`)가 그대로 나오고, 열린 문(187번) 하나만 개별 요청하면 열쇠가 나온다. | QRY-2E19 | [stage-17](stages/stage-17.md) |
| 18 | 건너편 금고 (CORS) | 다른 출처(`vault.bsh00.com`)로의 요청이 브라우저 CORS 정책 때문에 실패한다. 브라우저를 거치지 않고 그 주소를 직접 요청하면 코드가 그대로 반환된다. | CORS-FE6C | [stage-18](stages/stage-18.md) |
| 19 | 잘린 이름 | 채널 이름에 `&`, `#` 같은 특수문자가 섞여 있는데 인코딩하지 않고 보내면 서버가 실제로 받는 값이 중간에서 잘린다. 값 전체를 제대로 URL 인코딩해서 다시 보내면 서버가 원래 의도한 이름을 받아 코드를 내준다. | FIND-4B21 | [stage-19](stages/stage-19.md) |
| 20 | 뭉개진 코드 | 화면에 "packed (5 segments)"라고만 뜬다. 페이지가 불러오는 `packed.js` 파일 안에 base64로 인코딩된 문자열 배열이 들어있고, 이를 디코드해서 조합하면 코드가 된다. | FMT-9C7E | [stage-20](stages/stage-20.md) |
| 21 | 스쳐가는 값 | `derive()` 함수는 실행되는 그 순간에만 값이 존재하고 화면에는 글자 수만 보여준다. 같이 로드되는 스크립트 원본에서 계산식을 확인하고, 그 식이 참조하는 `/api/seed` 값(7512)을 16진수로 변환해 넣으면 같은 코드를 얻는다. | BRK-1D58 | [stage-21](stages/stage-21.md) |
| 22 | 거짓 검증 | 입력창은 "4자만" 받도록 프론트에서 막아놨지만, 이는 클라이언트 쪽 제한일 뿐이다. 서버에 직접 값을 보내 실패 메시지를 확인하면 실제로는 9자 이상, `-` 포함 포맷을 요구한다는 게 드러난다. 그 포맷대로 다시 보내면 통과한다. | BYPS-6A30 | [stage-22](stages/stage-22.md) |
| 23 | 지울 수 있는 이름 | 내 블랙리스트 항목을 조회(GET)만 하는 화면이지만, 같은 리소스에 삭제(DELETE) 메서드도 열려 있다. 직접 DELETE 요청을 보내 블랙리스트에서 빠지면 그다음 관문이 열린다. | METH-E3EC | [stage-23](stages/stage-23.md) |
| 24 | 위장한 사진 | 업로드 전에 클라이언트가 PNG 매직 바이트를 검사해서 가짜 파일은 요청 자체가 나가지 않게 막아둔다. 하지만 서버는 파일 내용이 아니라 `Content-Type` 헤더만 보고 판단한다. 아무 내용에 `image/png` 헤더만 붙여서 직접 업로드하면 통과한다. | MIME-4A49 | [stage-24](stages/stage-24.md) |
| 25 | 자칭 신분 | 서버는 특정 `User-Agent` 값만 신뢰하는데, 브라우저는 그 헤더를 스크립트에서 바꾸지 못하게 막는다. 페이지 소스(HTML 주석)에 신뢰 대상 이름(`escape-core`)이 그대로 남아 있어서, 그 값을 User-Agent로 위장해 직접 요청하면 통과한다. | HDR-6C1D | [stage-25](stages/stage-25.md) |
| 26 | 깨진 지문 | 32자리 16진수 쿠키(`is_admin`)가 있는데, 이는 `md5("false")` 값과 정확히 일치한다. 즉 서버가 실제 값이 아니라 문자열의 해시만 비교하고 있다는 뜻이라, `md5("true")` 값을 계산해 쿠키를 바꿔치면 봉인이 풀린다. | HASH-2B7C | [stage-26](stages/stage-26.md) |
| 27 | 먼저 묻는 편지 | 커스텀 헤더(`X-Seal`)를 실어 다른 출처로 보내는 요청이 CORS 사전 확인(프리플라이트)에서 거부되어 본 요청 자체가 나가지 않는다. 프리플라이트 응답(`Access-Control-Allow-Headers`)을 직접 확인하면 실제로 허용된 헤더 이름(`X-Vault-Key`)이 다르다는 걸 알 수 있고, 그 헤더와 이미 알고 있던 값으로 다시 요청하면 통과한다. | PREF-5927 | [stage-27](stages/stage-27.md) |
| 28 | 잠기지 않는 자물쇠 | 3자리 숫자 자물쇠. 한 번에 하나씩 눌러서는 끝이 없지만, 서버는 시도 횟수만 셀 뿐 잠금 없이 계속 받아준다. 000부터 999까지 순서대로 자동으로 대입해 맞는 값(142)을 찾았다. | IDB-3A97 | [stage-28](stages/stage-28.md) |
| 29 | 되돌리는 손 | 화면 값을 0.3초마다 감시해서 바꾸는 즉시 원상복구시키는 로직이 붙어있다. 화면을 직접 건드리는 대신, 그 로직이 최종적으로 호출하는 서버 API를 화면 조작 없이 바로 호출하면 감시망을 우회할 수 있다. | WTCH-7B62 | [stage-29](stages/stage-29.md) |
| 30 | 최종 보스 | 21, 22, 12번 스테이지에서 얻은 코드 세 개를 열쇠로 요구한다. 열쇠가 맞아도 곧바로 통과시키지 않고 "실시간 증명(liveness)"이 필요하다며 challenge 값을 함께 돌려준다. 거부 응답이 요구한 형식(`challenge:열쇠1:열쇠2:열쇠3`를 base64로 인코딩한 값)을 그대로 만들어 같은 요청에 실어 다시 보내면 최종 코드가 나온다. | BOSS-0F15 | [stage-30](stages/stage-30.md) |
