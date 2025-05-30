# Head-Dump
Ở bài ta sẽ truy cập vào trang web sau:
![image](https://hackmd.io/_uploads/rkmtdEPGxl.png)

Nhìn qua thì chẳng có gì đặc biệt, giờ ta sẽ xem source code của trang web.

``` html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>picoCTF News</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body>
    <div class="h-screen overflow-y-scroll bg-white">
        <div class="grid grid-cols-1 gap-4 lg:grid-cols-3 md:grid-cols-2 lg:gap-8">
            <div class="post p-5 lg:p-1 rounded-md">
                <div class="lg:fixed lg:top-7 lg:left-14 lg:w-3/12 md:fixed md:w-5/12">
                    <div class="bg-white p-8 rounded-lg shadow-md max-w-md w-full mb-4">
                        <!-- Banner Profile -->
                        <div class="relative">
                            <img src="/img/picoctf-logo-og.png" alt="Banner Profile" class="w-full rounded-t-lg">
                        </div>
     
                        
                        .....      
                        
                        <div class="flex items-center justify-between text-gray-500">
                            <div class="flex items-center space-x-2">
                                <button class="flex justify-center items-center gap-2 px-2 hover:bg-gray-50 rounded-full p-1">
                                    <svg class="w-5 h-5 fill-current" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">
                                        <path d="M12 21.35l-1.45-1.32C6.11 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-4.11 6.86-8.55 11.54L12 21.35z" />
                                    </svg>
                                    <span>12 Likes</span>
                                </button>
                            </div>
                            <button class="flex justify-center items-center gap-2 px-2 hover:bg-gray-50 rounded-full p-1">
                                <svg width="22px" height="22px" viewBox="0 0 24 24" class="w-5 h-5 fill-current" xmlns="http://www.w3.org/2000/svg">
                                    <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
                                    <g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g>
                                    <g id="SVGRepo_iconCarrier">
                                        <path fill-rule="evenodd" clip-rule="evenodd" d="M12 22C17.5228 22 22 17.5228 22 12C22 6.47715 17.5228 2 12 2C6.47715 2 2 6.47715 2 12C2 13.5997 2.37562 15.1116 3.04346 16.4525C3.22094 16.8088 3.28001 17.2161 3.17712 17.6006L2.58151 19.8267C2.32295 20.793 3.20701 21.677 4.17335 21.4185L6.39939 20.8229C6.78393 20.72 7.19121 20.7791 7.54753 20.9565C8.88837 21.6244 10.4003 22 12 22ZM8 13.25C7.58579 13.25 7.25 13.5858 7.25 14C7.25 14.4142 7.58579 14.75 8 14.75H13.5C13.9142 14.75 14.25 14.4142 14.25 14C14.25 13.5858 13.9142 13.25 13.5 13.25H8ZM7.25 10.5C7.25 10.0858 7.58579 9.75 8 9.75H16C16.4142 9.75 16.75 10.0858 16.75 10.5C16.75 10.9142 16.4142 11.25 16 11.25H8C7.58579 11.25 7.25 10.9142 7.25 10.5Z"></path>
                                    </g>
                                </svg>
                                <span>8 Comment</span>
                            </button>
                        </div>
                    ...
```

Để ý thấy có một đường dẫn 
``` html
<div class="mb-4">
    <p class="text-gray-800">Explore backend development with us <a href="" class="text-blue-600">#nodejs</a> ,
        <a href="" class="text-blue-600">#swagger UI</a> , <a href="/api-docs" class="text-blue-600 hover:underline">#API Documentation</a> 
    </p>
</div>
```
Nhấn vào thử, ta thấy hiện ra 1 trang web gồm danh sách các điểm cuối của API. Quan sát thì thấy ở phần `Diagnosing` có điểm cuối là `/heapdump`. 

![image](https://hackmd.io/_uploads/Bkzmp4DGge.png)

Mở ra ta thấy nút `Try it out`, chẳng suy nghĩ nhiều, tôi ngay lập tức nhấn vào coi có gì xảy ra =)))))))))).

![image](https://hackmd.io/_uploads/Hy1Ha4Dfle.png)

Kết quả là:

![image](https://hackmd.io/_uploads/BJtZTNvGlx.png)

Tải file về (khả năng rất cao là file này chứa flag), mở ra thì thấy file rất lớn. 

![image](https://hackmd.io/_uploads/r190aEDflx.png)

Tìm flag trong file:
```bash
grep "picoCTF{" heapdump-1748614471477.heapsnapshot
picoCTF{Pat!3nt_15_Th3_K3y_f1179e46}
```

Flag: picoCTF{Pat!3nt_15_Th3_K3y_f1179e46}