# Cookies
Bài này cho ta một đường link dẫn trên trang web và không có gợi ý gì cả. Truy cập trang web đó tại [đây](http://mercury.picoctf.net:21485/check).

Trang web có giao diện như sau:

![image](https://github.com/user-attachments/assets/dca03bbb-7346-4e49-aa1c-92a6fd7bf9db)

Ta nhập thử gì đó và kết quả là:

![image](https://github.com/user-attachments/assets/537e154d-1d1f-4460-9989-d4cc05d7257e)

`F12` rồi vào phần cookies thì ta thấy cookies có giá trị là `-1`.

![image](https://github.com/user-attachments/assets/8d46d121-3d23-4fb8-ba4c-2e0a74cd7b5f)

Thường thì chuỗi cookies sẽ là một đoạn `abcd...#$%@` gì đó nhưng lần này thì chỉ có một số là `-1`. Ta sẽ thử thay số này thành `0` sau đó làm mới trang web xem có gì xảy ra.

![image](https://github.com/user-attachments/assets/0e3de990-c008-4f2c-ade9-f0382d42e1c1)

Hmm =))) có lẽ ta hãy thử thêm với số `1`, `2`, `3` ,...

Mỗi số thì website cho ra một chuỗi khác nhau, kiên trì một chút đến số `18` thì có bất ngờ =))))

![image](https://github.com/user-attachments/assets/2200cf91-506d-4c37-a023-ea885298aaff)

Và ta có flag:

``` 
picoCTF{3v3ry1_l0v3s_c00k135_94190c8a}
```
