# WebDecode
Ở bài này ta sẽ có một trang web, nhiệm vụ của ta là tìm kiếm flag trong đó.
![image](https://hackmd.io/_uploads/BJLSSewMge.png)
Ta sẽ xem source code lần lượt của các trang `HOME` , `ABOUT` , `CONTACT`.
Trang `HOME`.
``` html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link rel="stylesheet" href="style.css">
  <link rel="shortcut icon" href="img/favicon.png" type="image/x-icon">
  <!-- font (google) -->
  <link href="https://fonts.googleapis.com/css2?family=Lato:ital,wght@0,400;0,700;1,400&display=swap" rel="stylesheet">
  <title>Home</title>
</head>
<body>
<header>
  <nav>
    <div class="logo-container">
      <a href="index.html"><img src="img/binding_dark.gif" alt="logo"></a>
    </div>
    <div class="navigation-container">
      <ul>
        <li><a href="index.html">Home</a></li>
        <li><a href="about.html">About</a></li>
        <li><a href="contact.html">Contact</a></li>
      </ul>
    </div>
  </nav>
</header>
  <section class="banner">
    <h1>Ha!!!!!! You looking for a flag?</h1>
    <p>Keep Navigating</p>
  
  </section><!-- .banner -->
  <section class="sec-intro">
    <div class="col">
      <p>Haaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa</p>
      <p>Keepppppppppppppp Searchinggggggggggggggggggg</p>
      <img src="./img/multipage-html-img1.jpg" alt="person">
      <figcaption>Don't give up!</figcaption>
    </div>
  </section><!-- .sec-intro -->
  
  <footer>
    <div class="bottombar">Copyright © 2023 Your_Name. All rights reserved.</div>
  </footer>
  
</body>
</html>
```

Trang `ABOUT`.
``` html
<!DOCTYPE html>
<html lang="en">
 <head>
  <meta charset="utf-8"/>
  <meta content="IE=edge" http-equiv="X-UA-Compatible"/>
  <meta content="width=device-width, initial-scale=1.0" name="viewport"/>
  <link href="style.css" rel="stylesheet"/>
  <link href="img/favicon.png" rel="shortcut icon" type="image/x-icon"/>
  <!-- font (google) -->
  <link href="https://fonts.googleapis.com/css2?family=Lato:ital,wght@0,400;0,700;1,400&amp;display=swap" rel="stylesheet"/>
  <title>
   About me
  </title>
 </head>
 <body>
  <header>
   <nav>
    <div class="logo-container">
     <a href="index.html">
      <img alt="logo" src="img/binding_dark.gif"/>
     </a>
    </div>
    <div class="navigation-container">
     <ul>
      <li>
       <a href="index.html">
        Home
       </a>
      </li>
      <li>
       <a href="about.html">
        About
       </a>
      </li>
      <li>
       <a href="contact.html">
        Contact
       </a>
      </li>
     </ul>
    </div>
   </nav>
  </header>
  <section class="about" notify_true="cGljb0NURnt3ZWJfc3VjYzNzc2Z1bGx5X2QzYzBkZWRfMDdiOTFjNzl9">
   <h1>
    Try inspecting the page!! You might find it there
   </h1>
   <!-- .about-container -->
  </section>
  <!-- .about -->
  <section class="why">
   <footer>
    <div class="bottombar">
     Copyright © 2023 Your_Name. All rights reserved.
    </div>
   </footer>
  </section>
 </body>
</html>
```

Trang `CONTACT`.
``` html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link rel="stylesheet" href="style.css">
  <link rel="shortcut icon" href="img/favicon.png" type="image/x-icon">
  <!-- font (google) -->
  <link href="https://fonts.googleapis.com/css2?family=Lato:ital,wght@0,400;0,700;1,400&display=swap" rel="stylesheet">
  <title>Contact me</title>
</head>
<body>
<header>
  <nav>
    <div class="logo-container">
      <a href="index.html"><img src="img/binding_dark.gif" alt="logo"></a>
    </div>
    <div class="navigation-container">
      <ul>
        <li><a href="index.html">Home</a></li>
        <li><a href="about.html">About</a></li>
        <li><a href="contact.html">Contact</a></li>
      </ul>
    </div>
  </nav>
</header>
  <section class="contact">
    <div class="contact-wrapper">
      <h1>Keep searching page.</h1>
      <h2> Don't give up!!! </h2>

          </div>
  </section> 
  <footer>
    <div class="bottombar">Copyright © 2023 Your_Name. All rights reserved.</div>
  </footer>
  
</body>
</html>
```

Ta nhận thấy trong source code của trang `ABOUT` có đoạn mã:
``` html
<section class="about" notify_true="cGljb0NURnt3ZWJfc3VjYzNzc2Z1bGx5X2QzYzBkZWRfMDdiOTFjNzl9">
```
Trong đó, thuộc tính notify_true không phải là một thuộc tính HTML hợp lệ. Có thể đây là flag được mã hóa.

Thực hiện giải mã 
``` bash
echo "cGljb0NURnt3ZWJfc3VjYzNzc2Z1bGx5X2QzYzBkZWRfMDdiOTFjNzl9" | base64 -d
```
Và ta có được flag:
``` bash
picoCTF{web_succ3ssfully_d3c0ded_07b91c79}
```

Flag: picoCTF{web_succ3ssfully_d3c0ded_07b91c79}