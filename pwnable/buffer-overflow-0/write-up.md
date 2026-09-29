# Category

pwnable

# Overview

Let's start off simple, can you overflow the correct buffer?

# Analysis

- 프로그램 동작 중 `SIGSEGV` 시그널이 발생하면 `sigsegv_handler` 함수가 실행되며, flag를 출력한다.

  ```c
  signal(SIGSEGV, sigsegv_handler);

  void sigsegv_handler(int sig) {
    printf("%s\n", flag);
    fflush(stdout);
    exit(1);
  }
  ```

- 사용자의 입력을 받아 `vuln` 함수를 호출하며, `vuln` 함수 내 `strcpy`함수는 `input`에 대한 길이 검증을 하지 않기 때문에 메모리를 덮어 쓸 수 있는 취약점이 존재한다.

  ```c
  char buf1[100];
  gets(buf1);
  vuln(buf1);

  void vuln(char *input){
    char buf2[16];
    strcpy(buf2, input);
  }
  ```

# Exploitation

- `strcpy()`에서 사용하는 16바이트 `buf2` 변수보다 큰 입력값을 전달하게 되면 `vuln` 함수 호출 후 되돌아가야하는 메모리주소를 사용자의 초과된 입력값만큼 덮어쓰게되며 `SIGSEGV` 오류가 발생하게된다.

  이를 통해 `sigsegv_handler`가 호출되며 flag를 획득할 수 있다.

- `input`에 따른 비교 시, 비정상의 경우 데이터가 할당되어야하는 16 bytes를 넘겨 저장되는 것을 알 수 있다.

  ```assembly
  # 정상
  ──────────────────────────────────────────[ STACK ]───────────────────────────────────────────
  00:0000│ esp 0xffffd31c —▸ 0x5655638b (main+241) ◂— add esp, 0x10
  01:0004│-088 0xffffd320 —▸ 0xffffd334 ◂— 'bbbbb'
  02:0008│-084 0xffffd324 ◂— 0x3e8
  03:000c│-080 0xffffd328 ◂— 0x3e8
  04:0010│-07c 0xffffd32c —▸ 0x56556333 (main+153) ◂— mov dword ptr [ebp - 0x10], eax
  05:0014│-078 0xffffd330 ◂— 0x178bfbfd
  06:0018│ eax 0xffffd334 ◂— 'bbbbb'
  07:001c│-070 0xffffd338 ◂— 0x62 /* 'b' */

  # 비정상
  ──────────────────────────────────────────[ STACK ]───────────────────────────────────────────
  00:0000│ esp 0xffffd31c —▸ 0x5655638b (main+241) ◂— add esp, 0x10
  01:0004│-088 0xffffd320 —▸ 0xffffd334 ◂— 'aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa'
  02:0008│-084 0xffffd324 ◂— 0x3e8
  03:000c│-080 0xffffd328 ◂— 0x3e8
  04:0010│-07c 0xffffd32c —▸ 0x56556333 (main+153) ◂— mov dword ptr [ebp - 0x10], eax
  05:0014│-078 0xffffd330 ◂— 0x178bfbfd
  06:0018│ eax 0xffffd334 ◂— 'aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa'
  07:001c│-070 0xffffd338 ◂— 'aaaaaaaaaaaaaaaaaaaaaaaaaaaa'
  ```

# Flag

`academy{ov...8f}`
