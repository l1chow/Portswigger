# Lab 1: OS command injection, simple case
![image.png](https://hackmd.io/_uploads/S1wpcJgma.png)
- Lab này chứa lỗ hổng OS command injection ở phần kiểm tra kho sản phầm
- Ứng dụng thực thi lệnh shell để web phản hồi giá trị nào đó
- Mục tiêu lab: xác định tên người dùng hiện tại

Chèn thêm lệnh `|whoami` 
- `|` để ngắt câu lệnh
- `whoami` để xác định người dùng hiện tại của server
![image.png](https://hackmd.io/_uploads/Bkghu21gma.png)

![image.png](https://hackmd.io/_uploads/HkeyaJg7p.png)
Done!

# Lab 2: Blind OS command injection with time delays
![image.png](https://hackmd.io/_uploads/HkGCNxeXa.png)
- Lab này chứa lỗ hổng blind OS command injection trong chức năng feedback
- Không có gì được trả về trong response
- Gây ra đỗ trễ 10s để khai thác lỗ hổng này

Dùng Burp Suite để chặn và sửa request submit feedback
![image.png](https://hackmd.io/_uploads/HJL2PxxX6.png)

Thử submit kèm `||id||` thì form submit thành công
![image.png](https://hackmd.io/_uploads/SJ1q_elQa.png)

Ta có lệnh `ping -c 10 127.0.0.1` sẽ ping tới 1 địa chỉ trong thời gian 10s

Chèn vào email `||ping+-c+10+127.0.0.1||`
![image.png](https://hackmd.io/_uploads/SkAOFgxXT.png)

Vậy là thành công
![image.png](https://hackmd.io/_uploads/SJP_Ygg76.png)

# Lab 3: Blind OS command injection with output redirection
![image.png](https://hackmd.io/_uploads/HyJSlWxmp.png)
- Lab có thư mục để ghi ở: `/var/www/images/`
- Mục tiêu lab: chuyển hướng đầu ra của lệnh được chèn đến một tệp trong thư mục này, sau đó sử dụng URL `image` để truy xuất nội dung của tệp.

Chèn lệnh sau để ghi được kết quả của lệnh whoami vào file `output.txt`
```sql
||whoami>/var/www/images/output.txt||
```
![image.png](https://hackmd.io/_uploads/SkMsE-emT.png)

Đọc file thông qua `/image?filename`
![image.png](https://hackmd.io/_uploads/Hk8ZrWxXa.png)

Đọc được nội dung ghi vào file. 
Done!