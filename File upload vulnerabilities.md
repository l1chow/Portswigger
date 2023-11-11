# Lab 1: Remote code execution via web shell upload
![image](https://hackmd.io/_uploads/BkCpnPnma.png)
- Lab này chứa lỗ hổng ở phẩn upload ảnh. Nó không validate file trước khi tải lên server.
- Để giải lab: upload web shell PHP để đọc nội dung của `/home/carlos/secret`. 

Sau khi upload ảnh, .txt không thu được gì
Ta có request
![image](https://hackmd.io/_uploads/ryzSxOnXa.png)
Ảnh được upload có file path `/files/avatars/name`

Giờ ta upload 1 file php xem sao
![image](https://hackmd.io/_uploads/S1zOZ_37a.png)

Response cho thấy ta được phép thực thi các lệnh shell
![image](https://hackmd.io/_uploads/BkjmzOn7T.png)

Thay đổi nội dung file php để đọc file lab yêu cầu
![image](https://hackmd.io/_uploads/SyTSWOnmp.png)
![image](https://hackmd.io/_uploads/BytcQun76.png)
![image](https://hackmd.io/_uploads/B1y67uhXT.png)

# Lab 2: Web shell upload via Content-Type restriction bypass
![image](https://hackmd.io/_uploads/SkGbs_3m6.png)
- Lab này chứa lỗ hổng ở phẩn upload ảnh. Nó validate file trước khi tải lên server nhưng không xác nhận lại.
- Để giải lab: upload web shell PHP để đọc nội dung của `/home/carlos/secret`. 

Ảnh vẫn được tải lên ở `/files/avatars/name`
![image](https://hackmd.io/_uploads/BkM0hunmp.png)

Hệ thống chỉ cho phép upload file `image/jpeg`
![image](https://hackmd.io/_uploads/H1Gja_nXp.png)
Tuy nhiên ta không thấy request nào phản hồi việc hệ thống nhận đúng dạng `image/jpeg` hay chưa

Upload file php như lab trước
![image](https://hackmd.io/_uploads/HyoV1F27T.png)
![image](https://hackmd.io/_uploads/rJUBkYnm6.png)
![image](https://hackmd.io/_uploads/rkpv1Ynm6.png)

# Lab 3: Web shell upload via path traversal
![image](https://hackmd.io/_uploads/Hk5B_n2Q6.png)
Yêu cầu tương tự lab trên

Đoạn code PHP này không được thực thi ở server
![image](https://hackmd.io/_uploads/H1Z5FnnQ6.png)

Cỏ vẻ thư mục `avatars` không được thực thi lệnh, vì vậy ta sẽ quay lại thư mục `files` xem sao
![image](https://hackmd.io/_uploads/rkd7in37T.png)
Vẫn là path `avatars`, tức là upload không thành công, có vẻ `../` đã bị hệ thống loại bỏ

Đi mã hóa chúng
![image](https://hackmd.io/_uploads/HJsnj23XT.png)

Upload thành công
![image](https://hackmd.io/_uploads/HJwe2nnQa.png)
![image](https://hackmd.io/_uploads/Hym533hQp.png)
![image](https://hackmd.io/_uploads/H1XT2237a.png)

# Lab 4: Web shell upload via extension blacklist bypass
![image](https://hackmd.io/_uploads/BJ61633QT.png)

Lab này đã không cho phép upload file `.php`
![image](https://hackmd.io/_uploads/SkSYA2nma.png)

Đổi tên file thành `exploit.php%00.jpg` nhưng vẫn không thành công
![image](https://hackmd.io/_uploads/S1Ddeahma.png)

Ta biết file `.htaccess` được dùng để cấu hình file upload nên ta sẽ tải lên 1 file `.htaccess` với nội dung `AddType application/x-httpd-php .abc`
![image](https://hackmd.io/_uploads/HkKPMThXp.png)
Như vậy, bây giờ có thể upload bất cứ file nào có đuôi `.abc`

![image](https://hackmd.io/_uploads/S1aAMahXp.png)
![image](https://hackmd.io/_uploads/rkolQp2XT.png)
![image](https://hackmd.io/_uploads/SkRZQT3Qa.png)

# Lab 5: Web shell upload via obfuscated file extension
![image](https://hackmd.io/_uploads/H1T9qeTma.png)
Lab này cũng không cho phép tải lên các file có đuôi trong blacklist

Chỉ có các file có đuôi `.jpg` và `.png` được phép upload
![image](https://hackmd.io/_uploads/BkFqTxaXp.png)

Chèn thêm byte null vào cuối kèm theo đuôi `.jpg` (vì byte null bỏ qua đuôi jpg, lúc đó file php sẽ được thực thi) thì upload thành công
![image](https://hackmd.io/_uploads/ryetpxpX6.png)
![image](https://hackmd.io/_uploads/r1tdpxTXa.png)
![image](https://hackmd.io/_uploads/B1JCAgaXp.png)

# Lab 6: Remote code execution via polyglot web shell upload
![image](https://hackmd.io/_uploads/B1pbVTnQa.png)

Lab này kiểm tra nội dung của file, nếu là ảnh mới được upload
![image](https://hackmd.io/_uploads/rkgkHTnQp.png)

Có vẻ nó nhận dạng bằng các ký tự header
![image](https://hackmd.io/_uploads/HkN0LT3Xa.png)
![image](https://hackmd.io/_uploads/BkRAIa27T.png)

Thay đổi script thành `<?php echo file_get_contents('/home/carlos/secret'); ?>` để đọc được nội dung file yêu cầu
![image](https://hackmd.io/_uploads/SkssipnmT.png)
![image](https://hackmd.io/_uploads/H1yTi627T.png)
![image](https://hackmd.io/_uploads/ryO0sT2ma.png)

# Lab 7: Web shell upload via race condition
![image](https://hackmd.io/_uploads/Sk6Wg-6Q6.png)

Chỉ có các file có đuôi `.jpg` và `.png` được phép upload
![image](https://hackmd.io/_uploads/rJSdrWp7a.png)

Ta sửa đuôi thành `%00.jpg` 
![image](https://hackmd.io/_uploads/S1IBwZ6mp.png)
Upload thành công

Tuy nhiên, hệ thống phát hiện tệp này không thỏa mãn nên đã xóa nó
![image](https://hackmd.io/_uploads/Hy9X5bTm6.png)
Quá trình kiểm tra này mất vài ms nhưng nó vẫn tồn tại trên server, ta sẽ tận dụng thời gian này để khai thác.

Tạo macro
![image](https://hackmd.io/_uploads/H1g-0-T7a.png)
![image](https://hackmd.io/_uploads/B1c_JG6Qp.png)

Test macro
![image](https://hackmd.io/_uploads/S1f5kf6Qp.png)

Gửi `POST /my-account/avatar` và `GET /files/avatars/exploit.php` qua Intruder
- Attack type: Sniper
- Payload: null payload, continue indefinitely
- Maximum concurrent requests: 2

![image](https://hackmd.io/_uploads/S1Tfj-TXp.png)
![image](https://hackmd.io/_uploads/Sk0VjWpm6.png)

Start attack đồng thời 2 yêu cầu này, status 200 tức là đã đọc thành công file upload
![image](https://hackmd.io/_uploads/rkwrgzp7p.png)
![image](https://hackmd.io/_uploads/H1Zcefpma.png)



