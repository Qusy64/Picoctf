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

![alt text](image.png)

Khả năng challenge này sẽ thử thách chúng ta lấy thông tin từ bộ nhớ và giải mã nó. Giờ ta sẽ thử lấy nhiều thông tin hơn và giải mã bằng [CyberChef](https://gchq.github.io/CyberChef/).

Thử với 20 lần `%x`

![alt text](image-1.png)

Có vẻ không có kết quả gì. Lúc này ta chú ý đến gợi ý của bài `Is this a 32-bit or 64-bit binary?`. Giờ hãy đổi thành 20 lần `%llx`.

![alt text](image-2.png)

Kết quả thì dường như chữ `picoCTF{...}` đã hiện ra nhưng ta cần sắp xếp lại một chút.

Sau khi loại bỏ các " kí tự " không liên quan thì những gì còn lại sẽ là:

![alt text](image-3.png)

Giờ ta sẽ sắp xếp đống này lại thành flag đúng.

Mò mẫm một lúc lú cái đầu thì ra được flag :3

![alt text](image-5.png)

Flag: picoCTF{4n1m41_57y13_4x4_f14g_9135fd4e}