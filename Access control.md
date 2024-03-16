# Lab 1: Unprotected admin functionality
![image](https://hackmd.io/_uploads/B1BhjKRma.png)
Lab này có phần admin panel không được bảo vệ.

Truy cập vào `/robots.txt`
![image](https://hackmd.io/_uploads/HkfKqYAXT.png)
Ta thấy đường dẫn đến `/administrator-panel` không được cho phép

Nhưng cứ truy cập vào
![image](https://hackmd.io/_uploads/rkC59KAQp.png)
Thành công 

![image](https://hackmd.io/_uploads/Hy9sqY076.png)

# Lab 2: Unprotected admin functionality with unpredictable URL
![image](https://hackmd.io/_uploads/H1JRjF0Xp.png)

Xem source thì thấy ta có thể vào Admin panel bằng cách truy cập `/admin--9dtzec`
![image](https://hackmd.io/_uploads/S1uRntCXp.png)
![image](https://hackmd.io/_uploads/ryKraYRmT.png)
![image](https://hackmd.io/_uploads/SkzIpF0Xp.png)

# Lab 3: User role controlled by request parameter
![image](https://hackmd.io/_uploads/SJEiTYAQ6.png)

Kiểm tra request thì ta thấy phần cookie lưu trữ thông tin về Admin
![image](https://hackmd.io/_uploads/BkEpb5AXp.png)

Sửa giá trị này thành true thì đã vào được admin panel
![image](https://hackmd.io/_uploads/r1dVzc0Qp.png)
![image](https://hackmd.io/_uploads/r1Zuz5AXp.png)
![image](https://hackmd.io/_uploads/Sy5_MqAQT.png)

# Lab 4: User role can be modified in user profile
![image](https://hackmd.io/_uploads/S1XsG9A7a.png)

Đổi email thì ta thấy trong response có key `roleid`
![image](https://hackmd.io/_uploads/Byo1EqCmp.png)

Đổi value thành 2 thì response cũng được thay đổi theo
![image](https://hackmd.io/_uploads/Syqp450Qa.png)

Truy cập vào admin thành công
![image](https://hackmd.io/_uploads/r1ygSq0X6.png)
![image](https://hackmd.io/_uploads/SJ7fHcCX6.png)

# Lab 5: URL-based access control can be circumvented
![image](https://hackmd.io/_uploads/H1cBAeyEp.png)
- Admin panel chưa được xác thực tại /admin, nhưng hệ thống được cấu hình để chặn quyền truy cập từ bên ngoài vào đường dẫn đó. 
- Tuy nhiên, back-end hỗ trợ header `X-Original-URL`.

`/admin` đã bị chặn
![image](https://hackmd.io/_uploads/rkRelZyNT.png)

Thêm header `X-Original-URL` để bypass
![image](https://hackmd.io/_uploads/r1wVxZy46.png)

Ta thấy trong response khi xóa user được chuyển đến 1 path
![image](https://hackmd.io/_uploads/SyTBZWJV6.png)

Chuyển đến path này để xóa user `carlos`
![image](https://hackmd.io/_uploads/ryegA-Z1Vp.png)
![image](https://hackmd.io/_uploads/H1alfbJNT.png)

# Lab 6: Method-based access control can be circumvented
![image](https://hackmd.io/_uploads/BypHGbyNa.png)
- Lab này triển khai các biện pháp kiểm soát quyền truy cập dựa trên HTTP.
- Để giải lab: login với `wiener:peter` và thăng cấp thành admin

Đối với admin có thể thăng cấp hoặc hạ quyền admin của user. Ta bắt được request như sau
![image](https://hackmd.io/_uploads/Sk5oaS14a.png)

Gửi request này với session của `wiener`
![image](https://hackmd.io/_uploads/H1IlRSJVa.png)
Kết quả trả về `"Unauthorized"` tức là không được phép thăng cấp

Sửa thành phương thức `GET`
![image](https://hackmd.io/_uploads/B1XVAByVp.png)

Chọn `Follow redirectory`
![image](https://hackmd.io/_uploads/rkrDRHk46.png)
Done!

# Lab 7: User ID controlled by request parameter
![image](https://hackmd.io/_uploads/ryhttWyVa.png)

Đăng nhập với `wiener`
![image](https://hackmd.io/_uploads/H1Cr5-kN6.png)
URL có `?id=wiener`

Sửa tham số `id` thành `carlos` thì ta đã chuyển sang được tài khoản `carlos`
![image](https://hackmd.io/_uploads/rkjw5ZkVT.png)
![image](https://hackmd.io/_uploads/H1chcW1V6.png)

# Lab 8: User ID controlled by request parameter, with unpredictable user IDs
![image](https://hackmd.io/_uploads/S1xSo-J4T.png)

Lab này đã sử dụng GUID cho `id` nên ta không thể sửa được như lab trên
![image](https://hackmd.io/_uploads/SkU9iWyV6.png)

Tuy nhiên, trên web lại hiện post của tài khoản carlos
![image](https://hackmd.io/_uploads/Byno6-kE6.png)

Nhấp vào thì ta có được GUID của carlos
![image](https://hackmd.io/_uploads/ry32ab1ET.png)

Đem đi sửa request thui
![image](https://hackmd.io/_uploads/r17CpWJV6.png)
![image](https://hackmd.io/_uploads/HJN1AWkNa.png)

# Lab 9: User ID controlled by request parameter with data leakage in redirect
![image](https://hackmd.io/_uploads/r1RYZQy46.png)

Sửa `id` thành carlos thì lab chuyển hướng đến trang login, tuy nhiên trong response thì lại nhận được API key luôn rùi
![image](https://hackmd.io/_uploads/rypO1MyEp.png)
![image](https://hackmd.io/_uploads/rJQH1M1Vp.png)

# Lab 10: User ID controlled by request parameter with password disclosure
![image](https://hackmd.io/_uploads/Sk5aKMJEa.png)

Login với `wiener` có được response chứa mật khẩu
![image](https://hackmd.io/_uploads/ry2msfkVp.png)

Sửa `id` thành `administrator` cũng nhận được response tương tự
![image](https://hackmd.io/_uploads/SJUdoGkEp.png)

Login vào admin và xóa user `carlos` được rồi
![image](https://hackmd.io/_uploads/H1VAjGyNT.png)

# Lab 11: Insecure direct object references
![image](https://hackmd.io/_uploads/SyVP2GyEp.png)
Lab này cho biết đoạn chat của người dùng được lưu trữ trực tiếp trên file server và chúng được truy xuất bằng URL tĩnh.

`View transcript` cho phép ta tải file chứa nội dung chat về
![image](https://hackmd.io/_uploads/rydYp4JNa.png)

Ta tải 1 file đầu tiên về nhưng nó lại ghi là 2
![image](https://hackmd.io/_uploads/BJQJR4JV6.png)

Đổi về file 1 xem sao
![image](https://hackmd.io/_uploads/Hyg-ANyET.png)
Đoạn chat này chứa mật khẩu của user

Login `carlos` với mật khẩu này
![image](https://hackmd.io/_uploads/BkPrRNkET.png)
Done!

# Lab 12: Multi-step process with no access control on one step
![image](https://hackmd.io/_uploads/SyPjl7yNa.png)

Đăng nhập với admin và thăng cấp cho user ta có thấy có 2 bước
- Bước 1: Chọn thăng cấp hoặc hạ cấp cho user
- Bước 2: Xác nhận điều này 

Ta có lần lượt 2 request sau
![image](https://hackmd.io/_uploads/rJxgeIyVa.png)
![image](https://hackmd.io/_uploads/H1eQg8yET.png)

Giờ ta sẽ đổi session và username về `wiener`
![image](https://hackmd.io/_uploads/Sk9ulIyET.png)
![image](https://hackmd.io/_uploads/rym5eIkEp.png)
![image](https://hackmd.io/_uploads/SJ36lLkV6.png)
Thăng cấp thành công

![image](https://hackmd.io/_uploads/BJuAgUk4T.png)

# Lab 13: Referer-based access control
![image](https://hackmd.io/_uploads/BJq0xmyV6.png)
Tương tự như bài trước

Ta có request khi thăng cấp cho user
![image](https://hackmd.io/_uploads/S1rl9S1Vp.png)

Đổi session và username của request này thành của `wiener`
![image](https://hackmd.io/_uploads/SJt7qHJVp.png)

Chọn `Follow redirectory`
![image](https://hackmd.io/_uploads/HyCN9H1E6.png)
Done!