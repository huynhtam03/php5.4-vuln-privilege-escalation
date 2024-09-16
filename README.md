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

[5. Truy cập Cơ sở dữ liệu](#5)

[6. Xóa dấu vết](#6)




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

<a name = '3.1'></a>
## 3.1.	Ưu điểm

- Đơn giản và dễ triển khai: Thuật toán Greedy có logic đơn giản và dễ dàng triển khai.
- Hiệu quả về thời gian: Thuật toán Greedy thường có độ phức tạp thời gian thấp, giúp giải quyết bài toán nhanh chóng.
- Hiệu quả cho bài toán Fractional Knapsack: Thuật toán Greedy cho kết quả tối ưu với bài toán Fractional Knapsack.
<a name = '3.2'></a>
## 3.2.	Nhược điểm

- Không đảm bảo tối ưu cho mọi trường hợp: Thuật toán Greedy không đảm bảo tìm ra lời giải tối ưu cho tất cả các bài toán, đặc biệt là 0/1 Knapsack.
- Không phù hợp cho các bài toán phức tạp: Với các bài toán có nhiều ràng buộc và yếu tố phức tạp, thuật toán Greedy có thể không cho ra kết quả chính xác.
- Cần sắp xếp dữ liệu đầu vào: Thuật toán yêu cầu sắp xếp các mục theo tỷ lệ giá trị/trọng lượng, điều này có thể tốn thời gian nếu dữ liệu đầu vào lớn.
<a name = '4'></a>
# 4.	Cài đặt và sử dụng

<a name = '4.1'></a>
## 4.1.	0/1 Knapsack Problem
```
var items = new List<Item>
{
    new Item { Value = 60, Weight = 10 },
    new Item { Value = 100, Weight = 20 },
    new Item { Value = 120, Weight = 30 }
};
int capacity = 50;
double maxValue = GreedyKnapsack01.Calculate(capacity, items);
Console.WriteLine("Gia tri lon nhat co the dat duoc: " + maxValue);
```
<a name = '4.2'></a>
## 4.2.	Fractional Knapsack Problem
```
var items = new List<Item>
{
    new Item { Value = 60, Weight = 10 },
    new Item { Value = 100, Weight = 20 },
    new Item { Value = 120, Weight = 30 }
};
int capacity = 50;
double maxValue = GreedyKnapsackFractional.Calculate(capacity, items);
Console.WriteLine("Gia tri lon nhat co the dat duoc: " + maxValue);
```
<a name = '4.3'></a>
## 4.3.	Bounded Knapsack Problem
```
// Sử dụng
var items = new List<Item>
{
    new Item { Value = 60, Weight = 10, Quantity = 2 },
    new Item { Value = 100, Weight = 20, Quantity = 2 },
    new Item { Value = 120, Weight = 30, Quantity = 2 }
};
int capacity = 50;
double maxValue = GreedyKnapsackBounded.Calculate(capacity, items);
Console.WriteLine("Gia tri lon nhat co the dat duoc: " + maxValue);
```
