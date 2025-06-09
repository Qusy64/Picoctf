# Two-sum
Challenge này cho ta 1 file .c `flag.c`. Mở file này lên thì ta có source code của challenge này như sau:

``` c
#include <stdio.h>
#include <stdlib.h>

static int addIntOvf(int result, int a, int b) {
    result = a + b;
    if(a > 0 && b > 0 && result < 0)
        return -1;
    if(a < 0 && b < 0 && result > 0)
        return -1;
    return 0;
}

int main() {
    int num1, num2, sum;
    FILE *flag;
    char c;

    printf("n1 > n1 + n2 OR n2 > n1 + n2 \n");
    fflush(stdout);
    printf("What two positive numbers can make this possible: \n");
    fflush(stdout);
    
    if (scanf("%d", &num1) && scanf("%d", &num2)) {
        printf("You entered %d and %d\n", num1, num2);
        fflush(stdout);
        sum = num1 + num2;
        if (addIntOvf(sum, num1, num2) == 0) {
            printf("No overflow\n");
            fflush(stdout);
            exit(0);
        } else if (addIntOvf(sum, num1, num2) == -1) {
            printf("You have an integer overflow\n");
            fflush(stdout);
        }

        if (num1 > 0 || num2 > 0) {
            flag = fopen("flag.txt","r");
            if(flag == NULL){
                printf("flag not found: please run this on the server\n");
                fflush(stdout);
                exit(0);
            }
            char buf[60];
            fgets(buf, 59, flag);
            printf("YOUR FLAG IS: %s\n", buf);
            fflush(stdout);
            exit(0);
        }
    }
    return 0;
}
```

Không vội quan tâm đến chương trình, ta phân tích cách lấy `flag`, ta thấy điều kiện để lấy được `flag` ở đây là `num1 > 0 || num2 > 0` nhưng chú ý hàm `addIntOvf()`:

``` c
static int addIntOvf(int result, int a, int b) {
    result = a + b;
    if(a > 0 && b > 0 && result < 0)
        return -1;
    if(a < 0 && b < 0 && result > 0)
        return -1;
    return 0;
}
```

Ta thấy rằng nếu không có hiện tượng `overflow` xảy ra thì hàm này sẽ trả về giá trị `0`. Cụ thể là 2 số cùng `a` và `b` cùng dấu (cùng lớn hoặc bé hơn 0) nhưng cộng lại lại cho ra kết quả là `result` mang dấu ngược lại.

Để ý đoạn code:

``` c
if (addIntOvf(sum, num1, num2) == 0) {
    printf("No overflow\n");
    fflush(stdout);
    exit(0);
}
```

Thấy rằng nếu hàm `addIntOvf` trả về giá trị `0` thì chương trình sẽ thoát trước khi in flag. Vậy giờ mục tiêu của ta chỉ cần khiến cho hiện tượng `overflow` xảy ra với phép cộng 2 số nguyên dương thì có thể lấy được flag.

Và yeah, ta dễ dàng có được flag:

```
picoCTF{Tw0_Sum_Integer_Bu773R_0v3rfl0w_fe14e9e9}
```