# 배운 점

각종 파일들의 메타데이터를 추출해서 쉽게 볼 수 있는 `exiftool`을 알게됬다.

앞으로 분석할 때 메타데이터를 통해 얻을 수 있는 정보가 있을 수 있으니 잘 활용해야될 것 같다.

검색을 하다 가독성이 안좋은 hex로 나열된 부분들을 `pdf-parser`를 통해 필요한 부분들만 pdf 구조 나눠 편하게 볼 수 있는 것을 알게되었고 `1 0 obj ~ endobj` 같이 오브젝트 단위로 나눠진다는 사실과 압축을 위해 `FlateDecode`라는 필터를 통해 `zlib/deflate`로 압축을 한 다는 사실. `pdf-parser.py`를 사용하면 압축된 내용을 풀 수 있는 부분을 알게됐다.

base64가 그냥 보고 패딩을 통해서 base64라고 유추했는데, `A-Z, a-z,0-9,+, /` 기호로 64진수로 표현하고 `=`를 통해 패딩을 채운다

url경로에서는 패딩을 생략하고 `+ /`를 `- _`로 바꿔서 url에 사용하기 안전하게 변형하기도 한다

또 변환전 데이터 8bit(1byte) 단위의 데이터를 6bit 단위로 쪼개서 사용하기 때문에 8과 6의 최소공배수 24값인 4의 배수의 길이를 가진다(8bit \* 3bytes = 24bits = 6bit \* 4bytes)

예를들어 `a`는 ASCII 코드 `97`이고 이진수로 `01100001`(8비트로 표현할 수 있으며) 6비트씩 쪼개기 때문에 `011000 010000(6비트를 만들기 위해 0으로 채움)`같이 나오게 되며 이걸 ASCII를 통해 변환하면 `24 16 -> YQ`가 되는 것을 알 수 있다. 그리고 4bytes를 채우기 위해 `==`를 추가해 `YQ==`값으로 변환되는 것을 알 수 있다.

# 풀이 과정 기록

아무 개념없이 무작정 포렌식관련 워게임을 풀어봤는데, 역시나 어디서부터 시작해야할지 전혀 감이 안잡혔다. 처음에는 hex editor를 통해 hex값을 보려고 했는데, 전혀 단서를 찾을 수 없었고, 이것저것 찾아본 결과 `exiftool`(file metadata 확인 툴), `pdf-parser.py`(pdf 파일 구조 파악 및 자동 zlib 압축 해제 코드)를 알게됐다.
문제에 힌트인 metadata를 통해 `exiftool`를 통해 정리된 metadata를 보게되었고 그 중 의심되는 부분인 base64로 인코딩 되어있는 부분을 통해 문제를 해결했다.

# 익스플로잇 코드 정리

# 심화 학습 (Deep Dive)

문제를 풀고난 뒤에 hex editor를 통해 다시 확인한 결과, 아래와 같이 `obj 2`부분의 `/Author`를 통해 정답을 유추할 수 있는 부분을 확인했다

`cGljb0NURntwdXp6bDNkX20zdGFkYXRhX2YwdW5kIV9jYTc2YmJiMn0\075`에서 `\075`가 8진수이기 때문에 ASCII로 변환하면 `=`이 되고 `cGljb0NURntwdXp6bDNkX20zdGFkYXRhX2YwdW5kIV9jYTc2YmJiMn0=`와 같이 flag로 변환되기 이전 base64 값을 찾을 수 있었다.

```bash
2 0 obj
<<
/Producer (PyPDF2)
/Author (cGljb0NURntwdXp6bDNkX20zdGFkYXRhX2YwdW5kIV9jYTc2YmJiMn0\075)
>>
```

# 참고
