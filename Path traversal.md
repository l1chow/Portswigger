# Lab 1: File path traversal, simple case
![image.png](https://hackmd.io/_uploads/SypT80P7p.png)
- Lab này chứa lỗ hổng path traversal ở phần hiển thị hình ảnh sản phẩm.
- Cần truy xuất nội dung của tệp `/etc/passwd`

Lab hiển thị hình ảnh với đường dẫn `/image?filename=name.jpg`
![image.png](https://hackmd.io/_uploads/SJv2FRvmT.png)

Sửa tham số `filename` bằng cách thêm một số `../` vào trước `/etc/passwd`
![image.png](https://hackmd.io/_uploads/Syvoq0PQp.png)
![image.png](https://hackmd.io/_uploads/Hym2qAD76.png)
![image.png](https://hackmd.io/_uploads/Bk6hcAPXa.png)
Vậy là đọc được nội dung bên trong file passwd

![image.png](https://hackmd.io/_uploads/H1VNo0v7p.png)
Done!

# Lab 2: File path traversal, traversal sequences blocked with absolute path bypass
![image.png](https://hackmd.io/_uploads/BJPf3AP76.png)

Tương tự lab trên, thay bằng đường dẫn tuyệt đối là oke
![image.png](https://hackmd.io/_uploads/rkdq1JOXp.png)
![image.png](https://hackmd.io/_uploads/HkLlxk_XT.png)

# Lab 3: File path traversal, traversal sequences stripped non-recursively
![image.png](https://hackmd.io/_uploads/SyGVekuQa.png)

Tương tự lab 1, nhưng `../` đã bị loại bỏ, vì vậy ta sẽ thay thế bằng `....//`
![image.png](https://hackmd.io/_uploads/ryEcZ1OXp.png)
![image.png](https://hackmd.io/_uploads/BJ7sbkOX6.png)

# Lab 4: File path traversal, traversal sequences stripped with superfluous URL-decode
![image.png](https://hackmd.io/_uploads/BkePf1_QT.png)
Lab này chặn đầu vào chứa các chuỗi truyền tải đường dẫn. Sau đó, nó thực hiện giải mã URL của đầu vào trước khi sử dụng. Vì vậy, ta sẽ mã hóa `../` trước khi truyền vào

![image.png](https://hackmd.io/_uploads/SkKNVJuX6.png)

Encode 1 lần nhưng không đúng
![image.png](https://hackmd.io/_uploads/Hy85N1d7p.png)

Encode lần 2 xem sao
![image.png](https://hackmd.io/_uploads/BJ7CNJOQa.png)
![image.png](https://hackmd.io/_uploads/SyozHJuma.png)
![image.png](https://hackmd.io/_uploads/rkb4ryOQa.png)

# Lab 5: File path traversal, validation of start of path
![image.png](https://hackmd.io/_uploads/rkhEI1dma.png)
Lab này yêu cầu đường dẫn phải từ expected folder.

Lab hiển thị hình ảnh với đường dẫn `/var/www/images/name.jpg`
![image.png](https://hackmd.io/_uploads/SkBVPy_Qp.png)

Payload `filename=/var/www/images/../../../etc/passwd`
![image.png](https://hackmd.io/_uploads/B1_wPJ_Qa.png)
![image.png](https://hackmd.io/_uploads/SJAdwkdm6.png)

# Lab 6: File path traversal, validation of file extension with null byte bypass
![image.png](https://hackmd.io/_uploads/SJUCDJdX6.png)

Xem nguồn trang ta thấy tên tệp được cung cấp như sau
![image.png](https://hackmd.io/_uploads/r1RWik_7p.png)

Ảnh rating nằm trong thư mục `images`, ta sẽ thử xem ảnh sản phẩm có nằm cùng 1 thư mục hay không
![image.png](https://hackmd.io/_uploads/r17P31d7p.png)
![image.png](https://hackmd.io/_uploads/HktqnkdXa.png)
Chỉ có đuôi `.jpg` được chấp nhận, còn `.png` thì không

Lab yêu cầu lấy ra nội dung của `etc/passwd` mà file passwd không có đuôi là `.jpg`, vì vậy ta phải
- Thêm 1 byte null là `%00` để kết thúc đường dẫn tệp
- Thêm đuôi mà file yêu cầu: `.jpg`

![image.png](https://hackmd.io/_uploads/H1BQJldm6.png)
![image.png](https://hackmd.io/_uploads/ByqNygOm6.png)
