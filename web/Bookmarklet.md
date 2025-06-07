# Bookmarklet
Truy cập vào website của challenge, ta thấy như sau:

![alt text](image.png)

Từ `bookmark` trông vừa lạ mà vừa quen, tra cứu một chút thì `bookmarklet` là một loại đặc biệt của `bookmark` (dấu trang), nhưng thay vì lưu một đường link thông thường, nó lưu một đoạn mã `JavaScript` nhỏ.

Ta thấy website cho ta một đoạn mã `JavaScript` như sau:

``` javascript
javascript:(function() {
    var encryptedFlag = "àÒÆÞ¦È¬ëÙ£ÖÓÚåÛÑ¢ÕÓ¨ÍÕÄ¦í";
    var key = "picoctf";
    var decryptedFlag = "";
    for (var i = 0; i < encryptedFlag.length; i++) {
        decryptedFlag += String.fromCharCode((encryptedFlag.charCodeAt(i) - key.charCodeAt(i % key.length) + 256) % 256);
    }
        alert(decryptedFlag);
    })();    
```

Ta có thể tạo một `bookmarklet` với đoạn code trên rồi mở nó để lấy flag hoặc là chạy code luôn=)))).

Và ta có flag:

```
picoCTF{p@g3_turn3r_18d2fa20}
```