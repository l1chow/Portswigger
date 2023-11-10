# Lab 1: Information disclosure in error messages
![image](https://hackmd.io/_uploads/rkvPB6o7p.png)
- Lab này đang sử dụng một framework dễ bị tấn công của bên thứ 3.
- Để giải lab: submit phiên bản của framework.

Ta có request `/GET /product?productId` nhận giá trị là số, chèn chuỗi vào thì nhận được lỗi
![image](https://hackmd.io/_uploads/HylNB_psQa.png)

Ta biết được framework web sử dụng là `Apache Struts 2 2.3.31`
![image](https://hackmd.io/_uploads/H1dHOTi76.png)

# Lab 2: Information disclosure on debug page
![image](https://hackmd.io/_uploads/HJxVFpsmT.png)
- Lab này chứa một trang debug tiết lộ thông tin nhạy cảm về ứng dụng. 
- Để giải lab: submit giá trị của biến `SECRET_KEY`.

Check thử các comment
![image](https://hackmd.io/_uploads/r1XSoToQp.png)

Có 1 đoạn code `Debug` được comment
![image](https://hackmd.io/_uploads/Sk9dhps76.png)

Chuyển hướng đến `/cgi-bin/phpinfo.php` và ta tìm thấy `SECRET_KEY`
![image](https://hackmd.io/_uploads/HkKlaTjQ6.png)
![image](https://hackmd.io/_uploads/BykB66jQT.png)

# Lab 3: Source code disclosure via backup files
![image](https://hackmd.io/_uploads/BkVOaTjXp.png)
- Lab này rò rỉ mã nguồn của nó thông qua file backup trong một thư mục ẩn. 
- Để giải lab: submit mật khẩu database

Truy cập file `/robots.txt`
![image](https://hackmd.io/_uploads/rJJd7As76.png)
Thấy `/backup` đã bị ẩn

Truy cập file `/backup` này
![image](https://hackmd.io/_uploads/BJ8iXAsm6.png)

Có vẻ đây là password đã được mã hóa
![image](https://hackmd.io/_uploads/ry5RQ0iQT.png)

Submit thui
![image](https://hackmd.io/_uploads/rkXGVComa.png)

# Lab 4: Authentication bypass via information disclosure
![image](https://hackmd.io/_uploads/rJQL40jma.png)
- Giao diện admin của lab chứa lỗ hổng xác thực
- Để giải lab: lấy tên header để bypass. Truy cập vào admin và xóa người dùng carlos.

Sau khi check web ta tìm được `/admin`
![image](https://hackmd.io/_uploads/H1YaDCs7a.png)
Tuy nhiên truy cập thì nhận được lỗi

Dựa theo hint của đề thì ta có thể dùng HTTP header để xử lý.`OPTIONS` và `TRACE` được dùng để lấy thêm thông tin.

Ở đây ta sẽ dùng `TRACE`
![image](https://hackmd.io/_uploads/BJlrsO0jX6.png)
Web trả về `X-Custom-IP-Authorization: 171.224.177.4` chứa 1 địa chỉ IP, việc này để xác định xem request có phải là `localhost` không

Thay thế bằng `127.0.0.1` để giả mạo `localhost`
![image](https://hackmd.io/_uploads/ByuL90jXa.png)

Truy cập vào `/admin` thành công
![image](https://hackmd.io/_uploads/BJcj5CoQp.png)

# Lab 5: Information disclosure in version control history
![image](https://hackmd.io/_uploads/S13bjAsQT.png)

Ta thấy có 1 thư mục `.git`
![image](https://hackmd.io/_uploads/ryYG1kn76.png)

Tạo 1 bản sao của thư mục này
![image](https://hackmd.io/_uploads/B1i-XkhX6.png)

![image](https://hackmd.io/_uploads/Skftmknmp.png)

Kiểm tra nhật ký git bằng `git log`
![image](https://hackmd.io/_uploads/B1T6XJnXT.png)
Lần commit cuối cùng có liên quan đến password của admin, vì vậy ta sẽ checkout lần commit trước đó
![image](https://hackmd.io/_uploads/S1YcHk2X6.png)
Tìm thấy 1 file `admin.conf`

Đọc file này thì ta lấy được password
![image](https://hackmd.io/_uploads/rkP0BJ3Xp.png)

Login vào admin thui
![image](https://hackmd.io/_uploads/SkfQLyhQ6.png)
![image](https://hackmd.io/_uploads/SkpmUJ3QT.png)




