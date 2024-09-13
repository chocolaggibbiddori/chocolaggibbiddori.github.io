---
layout: single
title: "Redis 명령어 모음"
tag: redis
---

# Redis 자료구조 명령어 모음

## 개요

&nbsp;&nbsp; 이 글은 `Redis`를 공부하다가 `Redis`에서 사용하는 기본 명령어들을 한 페이지에 보기 위해 정리한 글입니다.

- Redis version: 7.4.0
- Last modified: 24.09.13

&nbsp;&nbsp; `Redis`에서 사용하는 예약어들은 대문자로, 사용자가 입력해야 하는 임의 문자를 소문자로 표기하였습니다.
- `[ ]`: 생략 가능하다는 뜻입니다. 즉, 필요할 때만 사용하면 됩니다.
- `...`: 여러 개 입력 가능하다는 뜻입니다. 0개부터 가능합니다.
- `|`: 여러 개 중 하나만 사용 가능하다는 뜻입니다. 보통 대괄호(`[]`) 내에서 쓰입니다.

## Utility Command

### KEYS

- Content: 키 목록 확인
- Use: `KEYS [pattern]`
- Args
  - pattern: 찾고자 하는 키의 이름 패턴
    - `*`: 와일드카드. 이곳에는 모든 문자열이 올 수 있다.
    - `?`: 단일 문자 와일드카드. 하나의 문자를 대체한다.
    - `[]`: 제시된 여러 문자 중 하나를 대체한다.
    - `^`: 제시된 문자열을 제외한다.
    - 특수문자는 `\`로 표현 가능하다.
    - ex)
      - `h?llo` matches hello, hallo and hxllo
      - `h*llo` matches hllo and heeeello
      - `h[ae]llo` matches hello and hallo, but not hillo
      - `h[^e]llo` matches hallo, hbllo, ... but not hello
      - `h[a-b]llo` matches hallo and hbllo
      - `KEYS *` matches all keys
- Return: 키 목록
- Time complexity: O(n)
- Available since: 1.0.0

### EXISTS

- Content: 키 존재 여부 확인
- Use: `EXISTS key [keys...]`
- Args
  - key: 키 이름
  - keys: 키 이름(복수)
- Return
  - `1`: 키가 존재할 경우
  - `0`: 키가 존재하지 않을 경우
- Time complexity: O(N)
- Available since: 1.0.0

### TYPE

- Content: 해당 키의 타입 체크
- Use: `TYPE key`
- Args
  - key: 키 이름
- Return: 해당 키의 타입
- Time complexity: O(1)
- Available since: 1.0.0

### DEL

- Content: 키 삭제
- Use: `DEL key [keys...]`
- Args
  - key: 키 이름
  - keys: 키 이름(복수)
- Return: 삭제된 키의 개수
- Time complexity: O(N)
- Available since: 1.0.0

## String

### SET

- Content: 값 저장
- Use: `SET key value [NX | XX] [GET] [EX seconds | PX milliseconds | EXAT unix-time-seconds | PXAT unix-time-milliseconds | KEEPTTL]`
- Args
  - key: 키 이름
  - value: 값
  - NX(v2.6.12): 해당 키가 존재하지 않는 경우에만 값을 저장함
  - XX(v2.6.12): 해당 키가 이미 존재할 경우에만 값을 저장함
  - GET(v6.2.0): 값을 저장하면서 이전에 존재했던 값을 반환함. 값이 존재하지 않았으면 `(nil)`을 반환
  - EX(v2.6.12): `seconds`초의 유효시간을 설정함. 해당 시간이 지나면 삭제됨
  - PX(v2.6.12): `milliseconds`밀리초의 유효시간을 설정함. 해당 시간이 지나면 삭제됨
  - EXAT(v6.2.0): 유닉스 시간으로 `unix-time-seconds`초의 유효시간을 설정함. 해당 시간이 지나면 삭제됨
  - PXAT(v6.2.0): 유닉스 시간으로 `unix-time-milliseconds`초의 유효시간을 설정함. 해당 시간이 지나면 삭제됨
  - KEEPTTL(v6.0.0): 해당 키에 존재하는 기존 TTL을 유지합니다. SET 명령으로 값을 수정할 경우, 기존에 있던 TTL도 사라지지만 KEEPTTL 옵션으로 남은 시간을 유지할 수 있습니다.
- Return:
  - `OK`: 저장에 성공한 경우
  - `(nil)`: 저장되지 않은 경우
- Time complexity: O(1)
- Available since: 1.0.0
- Special
  - `[GET]` 옵션과 `[NX]` 옵션을 함께 사용할 수 있는건 v7.0.0 부터 가능함





___
**[참고]**  
[Redis 공식 문서](https://redis.io/docs/latest/commands/)\
[실전 레디스](https://www.hanbit.co.kr/store/books/look.php?p_code=B6215862232)