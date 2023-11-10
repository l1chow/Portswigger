# Lab 1: Excessive trust in client-side controls
![image.png](https://hackmd.io/_uploads/BkXECd_7p.png)
- Lab này không xác nhận đầy đủ thông tin đầu vào của người dùng và chứa lỗ hổng logic trong quy trình mua hàng.
- Để giải lab: mua `Lightweight l33t leather jacket` với mức giá ngoài ý muốn

Login với tài khoản lab cung cấp để kiểm tra chức năng mua hàng
![image.png](https://hackmd.io/_uploads/BkolXKuQ6.png)

Sau khi thêm sản phẩm vào giỏ thì trong request chứa cả giá sản phẩm
![image.png](https://hackmd.io/_uploads/HkwVmt_Q6.png)

Ta sửa giá này về số nhỏ 
![image.png](https://hackmd.io/_uploads/Hkc8mKd76.png)

Giá ở web đã thay đổi, lại còn có thể xN số lượng nữa
![image.png](https://hackmd.io/_uploads/S1v_7K_Qa.png)

Đặt hàng thui
![image.png](https://hackmd.io/_uploads/SJbjQtdX6.png)

# Lab 2: High-level logic vulnerability
![image.png](https://hackmd.io/_uploads/Hy-RVtOQT.png)
Yêu cầu giống lab trên

Đầu tiên, thêm thử 1 sản phẩm giá rẻ vào giỏ hàng
![image.png](https://hackmd.io/_uploads/SyG_wYdmT.png)

Trong request lúc này không chứa `price` nữa
![image.png](https://hackmd.io/_uploads/S1CtvYuQT.png)

Nhưng ta có thể thay đổi `quantity` xem sao
![image.png](https://hackmd.io/_uploads/BJYpwYuQ6.png)
![image.png](https://hackmd.io/_uploads/SJzRvt_Q6.png)

Nếu đổi `quantity` thành 1 số âm thì sao
![image.png](https://hackmd.io/_uploads/H1Xm_Kd76.png)

Lúc này, tổng tiền trở thành âm
![image.png](https://hackmd.io/_uploads/SJGUdKO7T.png)
Hệ thống kiểm tra số lượng < kho nên vẫn cho phép, đây chính là lỗ hổng logic hệ thống gặp phải

Giờ ta sẽ đi mua hàng bằng cách trên để có thể mua được sản phẩm với giá rẻ nhất
![image.png](https://hackmd.io/_uploads/HknmoY_QT.png)
Thay đổi số lượng của sản phẩm âm sao cho tổng dương cộng tổng âm bằng số dương bé nhất, và số dương này nhỏ hơn `store credit`

Đặt hàng thui
![image.png](https://hackmd.io/_uploads/BJ4hiY_m6.png)

# Lab 3: Low-level logic flaw
![image.png](https://hackmd.io/_uploads/BJ2AjKO7a.png)
Yêu cầu vẫn như lab trên

Sửa `quantity` thành 1000 thì trả về lỗi `Invalid parameter: quantity`
![image.png](https://hackmd.io/_uploads/HJE8cMt76.png)

Thử với các số bé thì không gặp lỗi gì
![image.png](https://hackmd.io/_uploads/SyJV2fKXT.png)

Ta cần xác định xem `quantity` tối đa là bao nhiêu bằng Intruder
![image.png](https://hackmd.io/_uploads/Bk71nfYQa.png)
![image.png](https://hackmd.io/_uploads/rJXunGY7T.png)

Đến payload 100 thì gặp lỗi, chứng tỏ giỏ hàng tối đa là `99`
![image.png](https://hackmd.io/_uploads/SkL-Aft7p.png)

Tiếp theo, ta sẽ gây ra sự tràn số, nếu có tràn tức là tổng tiền trở thành âm, lại giống lab 2 rồi
- Attack type: Sniper
- Payload: Null payloads, continue indefinitely
- Resource Pool: 1 concurrent request

![image.png](https://hackmd.io/_uploads/ryjALXtm6.png)
![image.png](https://hackmd.io/_uploads/H1gbvXKma.png)
![image.png](https://hackmd.io/_uploads/SJofPXKma.png)

![image.png](https://hackmd.io/_uploads/S17Y9mt76.png)

Như vậy chỉ cần thêm 1 sản phẩm khác và tăng số lượng cho đến khi âm, tổng tiền 2 sản phầm lúc này phải nằm trong khoảng 0 -> 100$ (store credit)
![image.png](https://hackmd.io/_uploads/H1kuC4tQa.png)
![image.png](https://hackmd.io/_uploads/ry6OCNKX6.png)

# Lab 4: Inconsistent handling of exceptional input
![image.png](https://hackmd.io/_uploads/H1313KOm6.png)
- Lab này chứa lỗ hổng ở phần đăng ký tài khoản.

Thử đăng ký tài khoản và kiểm tra các request nhưng không thu được gì
![image.png](https://hackmd.io/_uploads/rkCGLVYXp.png)

Khi đăng ký hệ thống sẽ gửi link xác nhận qua email nên ta thử đăng ký với một email dài
![image.png](https://hackmd.io/_uploads/BkcFgrKX6.png)

Hệ thống chỉ hiển thị độ dài của email là 255 ký tự
![image.png](https://hackmd.io/_uploads/S1CheBYm6.png)

Vì email client có thể nhận được subdomain nên ta sẽ thay đối 
- domain trở thành `@dontwannacry.com.exploit-0a2c00ce03fd7427848af9ce019b00db.exploit-server.net`
- `@dontwannacry.com` có 17 ký tự, cần thêm 238 ký tự tiền tố nữa
```py
text = "a" * 238 + "@dontwannacry.com" + ".exploit-0a2c00ce03fd7427848af9ce019b00db.exploit-server.net"
print(text)
```
![image.png](https://hackmd.io/_uploads/SkQvQrYQp.png)
![image.png](https://hackmd.io/_uploads/S1_pXSKQT.png)
![image.png](https://hackmd.io/_uploads/HyDgNrKXa.png)

Có thể truy cập trang admin rùi
![image.png](https://hackmd.io/_uploads/BkhMEHK7T.png)
![image.png](https://hackmd.io/_uploads/Hkj4NSKXT.png)

# Lab 5: Inconsistent security controls
![image.png](https://hackmd.io/_uploads/ry3JSrtQT.png)

Đăng ký tài khoản 
![image.png](https://hackmd.io/_uploads/SyIY8SKm6.png)

Sau khi update email thành `a@dontwannacry.com` thì có thể truy cập vào trang admin
![image.png](https://hackmd.io/_uploads/S1LgLHKmT.png)
![image.png](https://hackmd.io/_uploads/S1y7LBKmp.png)

# Lab 6: Weak isolation on dual-use endpoint
![image.png](https://hackmd.io/_uploads/rkPWX0tmp.png)
- Lab này đưa ra giả định sai sót về quyền của người dùng dựa trên thông tin đầu vào của họ. 
- Do đó, khai thác tính logic của các tính năng quản lý tài khoản để có quyền truy cập vào tài khoản của người khác tùy ý.

Sau khi đăng nhập, ta thấy chức năng đổi mật khẩu
![image.png](https://hackmd.io/_uploads/Sy25q0FX6.png)
![image.png](https://hackmd.io/_uploads/H14j5CY7T.png)

Xóa đi tham số `current-password` thì đã đổi mật khẩu thành công
![image.png](https://hackmd.io/_uploads/SyWp9CKma.png)

Đổi `username` thành `administrator` rồi đổi mật khẩu xem sao
![image.png](https://hackmd.io/_uploads/ryrXjCt7p.png)

Thành công, giờ login với `administrator: 1` và xóa `carlos`
![image.png](https://hackmd.io/_uploads/BJC8j0K76.png)
![image.png](https://hackmd.io/_uploads/HJvdjAFQ6.png)

# Lab: 7 Insufficient workflow validation
![image.png](https://hackmd.io/_uploads/rJluaRKQ6.png)
- Lab này có lỗi thiếu xác thực trong quá trình mua hàng
- Đầu tiên, kiểm tra các lỗi lúc thêm giỏ hàng thì không thu được gì

Đi mua thử 1 sản phẩm rẻ xem sao
![image.png](https://hackmd.io/_uploads/SJ24Gk9Xp.png)
![image.png](https://hackmd.io/_uploads/ryznl1576.png)
Sau khi mua thì nhận được request chuyển đến đường dẫn `/cart/order-confirmation?order-confirmed=true` để xác nhận đã mua hàng

Tiếp theo, thêm *Lightweight l33t leather jacket* vào giỏ hàng và gửi lại request trên
![image.png](https://hackmd.io/_uploads/HJWwfJq7T.png)

# Lab 8: Authentication bypass via flawed state machine
![image.png](https://hackmd.io/_uploads/SyB4VJ57T.png)
Lab này chứa lỗ hổng trong quá trình đăng nhập.

Thử truy cập trực tiếp vào trang admin thì không thành công
![image.png](https://hackmd.io/_uploads/HypYLJqX6.png)

Sau khi login ta được chuyển đến trang chọn role
![image.png](https://hackmd.io/_uploads/HyLgrJcmp.png)
Sau khi chọn role xong thì cũng không khai thác được gì

Giờ ta sẽ dùng burp suite bắt `GET /role-selector` và drop request này
![image.png](https://hackmd.io/_uploads/S1SnPy9QT.png)

Truy cập đến my account bằng cách thêm `/my-account` vào URL
![image.png](https://hackmd.io/_uploads/HkQ1d19m6.png)
![image.png](https://hackmd.io/_uploads/HkpM_y97a.png)

# Lab 9: Flawed enforcement of business rules
![image.png](https://hackmd.io/_uploads/SkFkty9QT.png)

Vào web ta sẽ nhận được mã giảm giá
![image.png](https://hackmd.io/_uploads/H1Pd9JcmT.png)

Lướt xuống và signup lại nhận được mã thứ 2
![image.png](https://hackmd.io/_uploads/Sy_Tqy57T.png)

Áp mã `NEWCUST5` rồi đến `SINGUP30` xem nào
![image.png](https://hackmd.io/_uploads/S1Kgsy9XT.png)
Vẫn chưa đủ tiền mua

Tiếp tục áp `SINGUP30`
![image.png](https://hackmd.io/_uploads/HkILsJqmT.png)
Không cho phép vì mã này đã được sử dụng

Vậy ta lại áp mã `NEWCUST5`
![image.png](https://hackmd.io/_uploads/BJk5iJc7a.png)

Thành công, vì vậy ta sẽ áp luân phiên 2 mã này cho đến khi đủ tiền mua áo
![image.png](https://hackmd.io/_uploads/rJeZ3Jqma.png)

# Lab 10: Infinite money logic flaw
![image.png](https://hackmd.io/_uploads/HyWPhJqXp.png)

Lab này có thêm Gift card code
![image.png](https://hackmd.io/_uploads/S1DHa157T.png)

Mua ngay 1 cái gift card thuii
![image.png](https://hackmd.io/_uploads/HydKC15X6.png)

Nhập mã và store credit có thêm 3$
![image.png](https://hackmd.io/_uploads/rkIpCyqm6.png)

Nếu lặp lại các bước trên nhiều lần, chắc sẽ có rất nhiều tiền

Tự động hóa quy trình này như sau:
- Tạo macro cho chuỗi yêu cầu
```
POST /cart
POST /cart/coupon
POST /cart/checkout
GET /cart/order-confirmation?order-confirmed=true
POST /gift-card
```
![image.png](https://hackmd.io/_uploads/ryUp-g9XT.png)
![image.png](https://hackmd.io/_uploads/SksTHg9ma.png)
![image.png](https://hackmd.io/_uploads/H1ORSg5mp.png)
![image.png](https://hackmd.io/_uploads/rJ82Ie97p.png)
![image.png](https://hackmd.io/_uploads/SkA1Pgcm6.png)
![image.png](https://hackmd.io/_uploads/rygivx9Xp.png)

![image.png](https://hackmd.io/_uploads/S1QTvx5Qa.png)
![image.png](https://hackmd.io/_uploads/ByNldg9Qa.png)

- Test macro: status 302 và nhận được gift-card tức là macro chạy đúng
![image.png](https://hackmd.io/_uploads/ryDzdeqQp.png)

- Gửi `GET /my-account` qua Intruder
    - Attack type: Sniper
    - Payload: Null payloads, Generate `409` payloads (vì ta có 112$ , cần 1225$ nữa, mỗi 1 payload được cộng 3$)
    - Maximum concurrent request: 1
    
![image.png](https://hackmd.io/_uploads/SyYDOgqXa.png)
![image.png](https://hackmd.io/_uploads/HkAocl9Qp.png)
![image.png](https://hackmd.io/_uploads/HJ_n5x9Xp.png)

- F5 để kiểm tra 
![image.png](https://hackmd.io/_uploads/BJNO6l5Xp.png)

- Mua áo thui
![image.png](https://hackmd.io/_uploads/S1Xcpx576.png)

# Lab 11: Authentication bypass via encryption oracle
![image.png](https://hackmd.io/_uploads/BJqMhec76.png)

Ở phần commnet nếu nhập sai email thì ta nhận được thông báo
![image](https://hackmd.io/_uploads/rJYaLdo76.png)
![image](https://hackmd.io/_uploads/BJcmFdjXp.png)

Trong cookie trả về `notification` đã được mã hóa
![image](https://hackmd.io/_uploads/S1JIudo7T.png)

Sau khi login thì ta cũng nhận được cookie có dạng tương tự
![image](https://hackmd.io/_uploads/HJbgcdsX6.png)

Ở `GET /post?postId` ta thấy`notification` đã được encode, vì vậy ta sẽ thay thế cookie này bằng giá trị của `stay-logged-in`
![image](https://hackmd.io/_uploads/S1IJ2uo7T.png)
Cookie duy trì đăng nhập chứa thông tin người dùng và một số trông giống như dấu thời gian

Vậy đổi chuỗi này thành `administrator:1699609413705` để duy trì đăng nhập cho admin
![image](https://hackmd.io/_uploads/H1Kd6OjX6.png)

Gặp lỗi `Invalid email address` có vẻ do cookie đã mã hóa
![image](https://hackmd.io/_uploads/Hy7r0_j7a.png)

Decode cookie này
- Mật mã sử dụng kích thước khối 16 byte. Dựa trên những hình ảnh trên 32 byte cũng có thể. Tuy nhiên, điều này sẽ rất bất thường và chỉ sử dụng quản trị viên làm tham số email sẽ hiển thị ba dòng được sử dụng và xác nhận 16 byte là kích thước khối.
- Trong mỗi khối, một số sự khuếch tán xảy ra. Khối thứ hai khác nhau trên mỗi byte trong khi 7 byte cuối cùng trong thông báo lỗi là tĩnh.

![image](https://hackmd.io/_uploads/S11C1KsXa.png)

Ta sẽ xóa đi khối đầu tiên (23 byte) sau đó encode base64 -> url
![image](https://hackmd.io/_uploads/HJs8-Komp.png)

Có thể xóa toàn bộ khối văn bản mã hóa mà không ảnh hưởng đến các khối sau. Có 7 byte thông báo lỗi nằm trong khối thứ hai: `dress: `. Ta không thể đơn giản xóa 7 byte này khỏi khối thứ hai vì điều này vi phạm tính toàn vẹn của khối
![image](https://hackmd.io/_uploads/HJ8cZFjma.png)

Tuy nhiên, nếu thêm 9 byte khác vào trước bản rõ mong muốn thì nó sẽ lấp đầy hoàn toàn khối 16 byte thứ hai và bản rõ bắt đầu ở đầu khối thứ ba.
![image](https://hackmd.io/_uploads/Skk9MYoma.png)

Decode url -> base64 và xóa đi 2 khối đầu tiên (32 byte)
![image](https://hackmd.io/_uploads/HkNymto7p.png)

Encode 1 lần nữa: base64 -> url
![image](https://hackmd.io/_uploads/rypWNKjX6.png)

Vậy là cookie này đã đúng
![image](https://hackmd.io/_uploads/ByaNVtom6.png)

Thay thế `stay-logged-in` bằng cookie vừa tìm được
![image](https://hackmd.io/_uploads/Bkw0BFsmT.png)
![image](https://hackmd.io/_uploads/HkL1IFsmp.png)
![image](https://hackmd.io/_uploads/ByZl8KjmT.png)
![image](https://hackmd.io/_uploads/SJrNIFi7a.png)

![image](https://hackmd.io/_uploads/rJ_D8toX6.png)
Done!