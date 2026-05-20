# Category

general skills

# Overview

Can you reverse a series of Linux text transformations to recover the original flag?

Start searching for the flag here nc foggy-cliff.picoctf.net 60463

# Analysis

제시된 서버에 접속하게 되면 다음과 같이 순차적으로 문제를 해결하는 화면이 출력된다.

# Exploitation

```sh
===Welcome to the Text Transformations Challenge!===

Your goal: step by step, recover the original flag.
At each step, you'll see the transformed flag and a hint.
Enter the correct Linux command to reverse the last transformation.

--- Step 1 ---
Current flag: KTgxMzkzOW4zLWZhMDFnQHplMHNmYTRlRy1nazNnLXRhMWZlcmlyRShTR1BicHZj
Hint: Base64 encoded the string.
Enter the Linux command to reverse it:
```

step 1 base64로 인코딩되어있는 문장을 디코딩하는 명령어 `base64 -d`를 입력하여 통과할 수 있다.

```sh
--- Step 2 ---
Current flag: )813939n3-fa01g@ze0sfa4eG-gk3g-ta1ferirE(SGPbpvc
Hint: Reversed the text.
Enter the Linux command to reverse it:
```

step 2 문자열을 뒤집은 명령어인 `rev`를 통해 통과할 수 있다.

```sh
--- Step 3 ---
Current flag: cvpbPGS(Eriref1at-g3kg-Ge4afs0ez@g10af-3n939318)
Hint: Replaced underscores with dashes.
Enter the Linux command to reverse it:
```

step 3 `-`를 `_`로 치환하기 위해 `tr '-' '_'`를 사용하여 통과할 수 있다.

```sh
--- Step 4 ---
Current flag: cvpbPGS(Eriref1at_g3kg_Ge4afs0ez@g10af_3n939318)
Hint: Replaced curly braces with parentheses.
Enter the Linux command to reverse it:
```

step 4 `()`를 `{}`로 치환하기 위해 `tr '()' '{}'`를 사용하여 통과할 수 있다.

```sh
--- Step 5 ---
Current flag: cvpbPGS{Eriref1at_g3kg_Ge4afs0ez@g10af_3n939318}
Hint: Applied ROT13 to letters.
Enter the Linux command to reverse it:
```

step 5 rot13이 적용된 문자열을 다시 치환하기 위해 `tr 'a-zA-Z' 'n-za-mN-ZA-M'`를 사용하여 통과할 수 있다.

```sh
Congratulations! You've recovered the original flag:
>>> picoCTF{Re...18}
```

# Flag

`picoCTF{Re...18}`
