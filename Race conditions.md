# Lab 1: Limit overrun race conditions
![image](https://hackmd.io/_uploads/Skm50YdE6.png)

![image](https://hackmd.io/_uploads/SkOlYodE6.png)
- Sau khi đọc docx thì ta biết được server sẽ xử lý request đầu tiên và kết thúc khi cập nhật DB để cho biết rằng bạn đã sử dụng mã này. 
- Thời gian này được gọi là race window, và ta có thể liên tục yêu cầu giảm giá bao nhiêu lần tùy thích.
- Do đó, ta sẽ tấn công race condition bằng cách gửi song song nhiều request cùng lúc.

Ta có request áp dụng coupon 
![image](https://hackmd.io/_uploads/By0u89_E6.png)

Sau đó tạo khoảng 20 request như vậy để gửi. Nhóm chúng lại để gửi cùng 1 lúc (như 1 cuộc đua)
![image](https://hackmd.io/_uploads/BJlK8ju4p.png)

Chọn gửi request song song
![image](https://hackmd.io/_uploads/rJhYLs_Np.png)
![image](https://hackmd.io/_uploads/HkRkXsdNa.png)
![image](https://hackmd.io/_uploads/HyAnLsO4p.png)

Giờ thì mua được áo với giá siêu hời
![image](https://hackmd.io/_uploads/B12rvju4a.png)

# Lab 2: Bypassing rate limits via race conditions
![image](https://hackmd.io/_uploads/SJ-o5idNT.png)
Vẫn là gửi nhiều request để tận dụng race window nhưng áp dụng kỹ thuật này để bypass mật khẩu `carlos`

Đầu tiên, kiểm tra chức năng login
![image](https://hackmd.io/_uploads/Hyhxe3uVa.png)
Nếu nhập sai password quá 3 lần thì sẽ bị khóa

Thử làm giống lab trước, gửi 20 request với password sai xem sao
![image](https://hackmd.io/_uploads/SJXvr2ONT.png)
Như vậy, ta sẽ login được nhiều hơn 3 lần trước khi bị khóa

Gửi qua Turbo intruder để xử lý
![image](https://hackmd.io/_uploads/BJCTOhON6.png)
![image](https://hackmd.io/_uploads/rkqgYhd46.png)
Vậy tìm được `carlos: shadow`

![image](https://hackmd.io/_uploads/HJ3KFndNT.png)
Done!

# Lab 3: Multi-endpoint race conditions
![image](https://hackmd.io/_uploads/HkJ1dAdE6.png)

Đầu tiên, gửi 1 request đến server thì mất hơn 600ms để phản hồi
![image](https://hackmd.io/_uploads/r1-k9C_46.png)

Nếu gửi nhanh 2 request thì mất 300ms để phản hồi
![image](https://hackmd.io/_uploads/r1e-qAON6.png)

- Hệ thống xảy ra điều này bởi vì khi gửi request 1 đến thì server sẽ kiểm tra session trong 600ms, ngay lập tức lại nhận được request 2 (300ms) và không cần kiểm tra lại session nên phản hồi nhanh hơn.
- Ta có độ trễ khoảng 300ms. Tận dụng độ trễ này thực hiện attack race condition.
    - Thêm 2 request sau vào 1 group
![image](https://hackmd.io/_uploads/r1nZMkFV6.png)
![image](https://hackmd.io/_uploads/r16lZ1YEp.png)
    - Sử dụng request `GET /` để xác thực giúp quá trình phản hồi của 2 request trên nhanh hơn
![image](https://hackmd.io/_uploads/r1fQW1Y46.png)

Attack race condition trong quá trình mua gift card nào
![image](https://hackmd.io/_uploads/HkRVfkYNa.png)

Đầu tiên, send "homepage", tiếp theo gửi nhanh 2 lần "group"
![image](https://hackmd.io/_uploads/HJ8ZPJY4T.png)
Done!

# Lab 4: Single-endpoint race conditions
![image](https://hackmd.io/_uploads/B1DexetN6.png)
- Lab chứa chức năng update email bằng cách nhấp vào link xác nhận được gửi qua email
![image](https://hackmd.io/_uploads/Byv9mgK4T.png)

![image](https://hackmd.io/_uploads/B1rr7ltEp.png)
- Nếu gửi 2 hoặc nhiều request đến 1 điểm cuối thì có thể gây ra nhầm lẫn: reset password cho victim nhưng mã xác nhận lại gửi cho hacker.
- Vì vậy, nếu ta gửi nhiều request update email đến server thì cũng gây ra hiện tượng tương tự.

Thử xem đúng không nào, gửi song song 5 request với 5 địa chỉ email khác nhau
![image](https://hackmd.io/_uploads/HJSrrgtE6.png)
![image](https://hackmd.io/_uploads/H1-FrxYN6.png)
Server xảy ra nhầm lẫn như dự đoán

Áp dụng đối với email `carlos` để giải lab
![image](https://hackmd.io/_uploads/H1YvLlF4p.png)
![image](https://hackmd.io/_uploads/rk5O_xt4p.png)

Lặp lại vài lần ta mới gặp được email này =))))
![image](https://hackmd.io/_uploads/BJdYugKNT.png)
![image](https://hackmd.io/_uploads/BJWjdxFN6.png)
Done!

# Lab 5: Partial construction race conditions
![image](https://hackmd.io/_uploads/BkGiON5Na.png)

Đầu tiên, kiểm tra chức năng register
![image](https://hackmd.io/_uploads/HJS4BB9V6.png)
![image](https://hackmd.io/_uploads/HyN7Br9N6.png)
Hệ thống chỉ cho phép đăng ký với domain `@ginandjuice.shop`

Kiểm tra request thì thấy hệ thống sử dụng script js để xác thực người dùng, script này submit form đến `/confirm?action`, `action` này chứa truy vấn có mã token
![image](https://hackmd.io/_uploads/SyxUrS9Va.png)

Kiểm tra cách xác thực của server với các giá trị token 
![image](https://hackmd.io/_uploads/H1M5HH5NT.png)
![image](https://hackmd.io/_uploads/Bkp9rr5E6.png)
![image](https://hackmd.io/_uploads/S1KiSB5Np.png)
Nhận được các thông báo lỗi khác nhau tức là nó đọc được các loại dữ liệu khác nhau.

Nếu cần xác thực lại thì sẽ mất 1 thời gian race window nhỏ. Vì vậy sẽ có 1 token hợp lệ để xác nhận đăng ký của người dùng.

Bây giờ đi register đồng thời để tìm được token đó. Script như sau:
```python
def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=1,
                           engine=Engine.BURP2
                           )
    confirmEmail = '''POST /confirm?token[]= HTTP/2
Host: 0ac200a503fc8e228002126700410079.web-security-academy.net
Cookie: phpsessionid=XeQZfcHZmjjjPbvNmfrs4JgAhtGqnnIG
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/119.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0ac200a503fc8e228002126700410079.web-security-academy.net/register
Sec-Fetch-Dest: script
Sec-Fetch-Mode: no-cors
Sec-Fetch-Site: same-origin
Te: trailers
'''
    for user in range(20):
        username = 'a1' + str(user)
        engine.queue(target.req, username, gate=str(user))

        for confirm in range(50):
            engine.queue(confirmEmail, gate=str(user))

        engine.openGate(str(user))


def handleResponse(req, interesting):
    table.add(req)
```
![image](https://hackmd.io/_uploads/B1wmnIqVT.png)

Reponse đăng ký thành công cho tài khoản `a111: 123`
![image](https://hackmd.io/_uploads/ryxOhU5Vp.png)

Login với tài khoản vừa đăng ký được
![image](https://hackmd.io/_uploads/ry-22IcEp.png)
![image](https://hackmd.io/_uploads/rJhnnIcET.png)
Done!

# Lab 6: Exploiting time-sensitive vulnerabilities
![image](https://hackmd.io/_uploads/Syc4KlFEp.png)

Đầu tiên, kiểm tra lab này có attack theo race condition được không
Ta cần gửi ít nhất 2 request
![image](https://hackmd.io/_uploads/rkq4ClYEp.png)

Mặc dù gửi 2 request song song nhưng request thứ 2 nhận được phản hồi trễ 300ms
![image](https://hackmd.io/_uploads/SypP0xYVT.png)
![image](https://hackmd.io/_uploads/BkNP0xtNT.png)
Vậy là server xử lý lần lượt chứ không xử lý đồng thời

Ta sẽ thay đổi giá trị của session và csrf xem sao
![image](https://hackmd.io/_uploads/B1ib1-FV6.png)

Giờ thì 2 request đã được xử lý cùng lúc
![image](https://hackmd.io/_uploads/ryqLJZYNp.png)
Còn token thì vẫn nhận được 1 token duy nhất
![image](https://hackmd.io/_uploads/ryTIebYV6.png)

Race condition tương tự lab trên thui 
Đổi username thành `carlos`
![image](https://hackmd.io/_uploads/rJVFe-KN6.png)
![image](https://hackmd.io/_uploads/HyaAlZFV6.png)
![image](https://hackmd.io/_uploads/HyxebbtE6.png)
Không thành công

Lặp lại vài lần nữa thì oki rùi
![image](https://hackmd.io/_uploads/BkOhD4c4a.png)
![image](https://hackmd.io/_uploads/H1aAPVqEp.png)
