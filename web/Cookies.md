# Cookies
Bài này cho ta một đường link dẫn trên trang web và không có gợi ý gì cả. Truy cập trang web đó tại [đây](http://mercury.picoctf.net:21485/check).

Trang web có giao diện như sau:

![alt text](image.png)

Ta nhập thử gì đó và kết quả là:

![alt text](image-1.png)

`F12` rồi vào phần cookies thì ta thấy cookies có giá trị là `-1`.

![alt text](image-2.png)

Thường thì chuỗi cookies sẽ là một đoạn `abcd...#$%@` gì đó nhưng lần này thì chỉ có một số là `-1`. Ta sẽ thử thay số này thành `0` sau đó làm mới trang web xem có gì xảy ra.

![alt text](image-3.png)

Hmm =))) có lẽ ta hãy thử thêm với số `1`, `2`, `3` ,...

Mỗi số thì website cho ra một chuỗi khác nhau, kiên trì một chút đến số `18` thì có bất ngờ =))))

![alt text](image-4.png)

Và ta có flag:

``` 
picoCTF{3v3ry1_l0v3s_c00k135_94190c8a}
```
