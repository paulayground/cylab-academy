# Category

General Skills

# Overview

Sometimes you need to handle process data outside of a file. Can you find a way to keep the output from this program and search for the flag?

# Analysis

- 제공된 chatelaine.cylabacademy.net 35501에 접속 시 다음과 같은 텍스트 응답을 받고 연결이 종료된다

```txt
Not a flag either
Not a flag either
I don't think this is a flag either

...

Not a flag either
Again, I really don't think this is a flag
Not a flag either

```

# Exploitation

- 문제의 요구사항와 같이 출력결과를 저장하고 해당 내용에서 flag 구성요소인 `academy{}`를 검색할 경우 flag를 획득할 수 있다.

```bash
nc chatelaine.cylabacademy.net 35501 > res.log

cat res.log | grep academy{
```

# Flag

`academy{di...8E}`
