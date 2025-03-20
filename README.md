**Bài 1. PIETIME**
Đầu tiên dùng ida để dịch chương trình ra pesudo code.

![Image](https://github.com/user-attachments/assets/2c64474d-d4c6-452f-9278-4cc7f25b9131)

Ta thấy chương trình sẽ nhảy đến địa chỉ mà chúng ta nhập vào, lướt sơ qua thì ta thấy hàm win và có thể hàm này sẽ cho ta flag.

![Image](https://github.com/user-attachments/assets/cdb08ec6-761b-4243-ba9f-c4417c4427be)

Đúng thật, vậy giờ ta chỉ cần tìm được địa chỉ của hàm win.

![Image](https://github.com/user-attachments/assets/a956a898-1165-4b6b-958e-fcdcad7465ac)

Ta thấy PIE được bật, và ta sẽ đi tìm hiểu thử có gì đặc biệt với địa chỉ hàm win trong trường hợp này hay không.

Dễ thấy hàm win có địa chỉ bằng địa chỉ hàm main - 0x96

![Image](https://github.com/user-attachments/assets/957c56a7-5368-4383-9fe9-55ff27cbea89)

Ta có script sau:

![Image](https://github.com/user-attachments/assets/88722ed6-101c-4e1e-a43f-ad19dbf7b5d1)

Flag: picoCTF{b4s1c_p051t10n_1nd3p3nd3nc3_80c3b8b7}

**Bài 2. PIETIME2**

Bài này cũng giống PIETIME, nhưng có khác chút

![Image](https://github.com/user-attachments/assets/f4618308-5f29-4ff2-8454-44164a875bd7)

Ta có hàm call_funtions()

![Image](https://github.com/user-attachments/assets/a899e2c5-7d73-4058-a879-cb9a47526028)

Và hàm win()

![Image](https://github.com/user-attachments/assets/b33ff8c1-3795-4b70-ab51-3ce47b42e2f0)

Ta vẫn thấy PIE được bật 

![Image](https://github.com/user-attachments/assets/0a87c906-9af3-4ef7-b438-71b234b67e9b)

Sự khác biệt là lần này, chương trình không còn cho sẵn địa chỉ hàm main() nữa. Việc của chúng ta là đi tìm địa chỉ của hàm main().

![Image](https://github.com/user-attachments/assets/5fb9a2f8-12c1-419d-a706-6ddda343844d)

Ta thấy nếu đặt breakpoint ngay trước lệnh printf(s) có lỗi format string, ta có stack như sau:

![Image](https://github.com/user-attachments/assets/46a8fc8e-d11c-4dc8-a631-088df89522ac)

Vậy suy ra tham số 19 từ stack là địa chỉ của hàm main + 65

(0x68 ÷ 8) + 6 = 13 + 6 = 19

Ta có script exploit như sau:

![Image](https://github.com/user-attachments/assets/047c2755-57bc-4c7d-921d-d43dd9c5859d)
