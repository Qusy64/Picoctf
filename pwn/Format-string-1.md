# Format-string-1
Challenge này cho chúng ta 2 file. Một file nhị phân `format-string-1` và file .c `format-string-1.c`, tải cả 2 file về và ta sẽ mở file .c để xem thử thách này có gì:

``` c
#include <stdio.h>


int main() {
  char buf[1024];
  char secret1[64];
  char flag[64];
  char secret2[64];

  // Read in first secret menu item
  FILE *fd = fopen("secret-menu-item-1.txt", "r");
  if (fd == NULL){
    printf("'secret-menu-item-1.txt' file not found, aborting.\n");
    return 1;
  }
  fgets(secret1, 64, fd);
  // Read in the flag
  fd = fopen("flag.txt", "r");
  if (fd == NULL){
    printf("'flag.txt' file not found, aborting.\n");
    return 1;
  }
  fgets(flag, 64, fd);
  // Read in second secret menu item
  fd = fopen("secret-menu-item-2.txt", "r");
  if (fd == NULL){
    printf("'secret-menu-item-2.txt' file not found, aborting.\n");
    return 1;
  }
  fgets(secret2, 64, fd);

  printf("Give me your order and I'll read it back to you:\n");
  fflush(stdout);
  scanf("%1024s", buf);
  printf("Here's your order: ");
  printf(buf);
  printf("\n");
  fflush(stdout);

  printf("Bye!\n");
  fflush(stdout);

  return 0;
}
```

Nhìn qua thì ta thấy một lỗi `format string` ở chỗ:

``` c
printf(buf);
```

Còn lại, chương trình cũng không có hàm nào là có vẻ sẽ **"In flag"** cả. Nên ta sẽ thử nhập gì đó xem sao.

![image](https://github.com/user-attachments/assets/6a275cf6-a804-4ee5-afc1-6440c112c64d)

Khả năng challenge này sẽ thử thách chúng ta lấy thông tin từ bộ nhớ và giải mã nó. Giờ ta sẽ thử lấy nhiều thông tin hơn và giải mã bằng [CyberChef](https://gchq.github.io/CyberChef/).

Thử với 20 lần `%x`

![image](https://github.com/user-attachments/assets/d289e788-53f4-4292-b0f5-2f3cb2b88630)

Có vẻ không có kết quả gì. Lúc này ta chú ý đến gợi ý của bài `Is this a 32-bit or 64-bit binary?`. Giờ hãy đổi thành 20 lần `%llx`.

![image](https://github.com/user-attachments/assets/8d56b2ea-8e67-4cd3-b62b-950e3ada2a75)

Kết quả thì dường như chữ `picoCTF{...}` đã hiện ra nhưng ta cần sắp xếp lại một chút.

Sau khi loại bỏ các " kí tự " không liên quan thì những gì còn lại sẽ là:

![image](https://github.com/user-attachments/assets/46605bcd-0e4b-48da-8dd0-59f90b32ba8f)

Giờ ta sẽ sắp xếp đống này lại thành flag đúng.

Mò mẫm một lúc lú cái đầu thì ra được flag :3

![image](https://github.com/user-attachments/assets/d2f06af8-c1fc-4917-ad13-9573b0ac4c67)

Flag: picoCTF{4n1m41_57y13_4x4_f14g_9135fd4e}
