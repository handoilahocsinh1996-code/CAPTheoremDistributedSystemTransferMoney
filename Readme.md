# Bài tập: Thiết kế Distributed System

## 1. Chọn Database

### Database được chọn: MySQL

Hệ thống sử dụng **MySQL (SQL Database)** để lưu trữ thông tin tài khoản và số dư.

Lý do lựa chọn:

- Dữ liệu tài chính yêu cầu tính toàn vẹn.
- MySQL hỗ trợ Transaction.
- Có thể đảm bảo nhiều thao tác cập nhật dữ liệu được thực hiện một cách nhất quán.

---

## 2. Quyết định CAP Strategy

### Lựa chọn: CP

CP gồm:

- **C - Consistency (Tính nhất quán)**
- **P - Partition Tolerance (Khả năng chịu phân vùng mạng)**

Hệ thống ưu tiên **Consistency** khi xảy ra lỗi mạng giữa các node.

Ví dụ:

```text
Tài khoản A: 1.000.000
Tài khoản B:   500.000
```

A chuyển 200.000 cho B:

```text
Tài khoản A:   800.000
Tài khoản B:   700.000
```

Hệ thống không nên để xảy ra trạng thái:

```text
Tài khoản A:   800.000
Tài khoản B:   500.000
```

vì tiền đã bị trừ ở A nhưng chưa được cộng cho B.

---

## 3. Giải thích lựa chọn CP

Hệ thống được xây dựng theo mô hình **Banking / Money Transfer**, trong đó tính chính xác của dữ liệu tài chính là ưu tiên quan trọng.

Khi xảy ra **network partition**, hệ thống có thể từ chối hoặc tạm thời không xử lý giao dịch nếu không thể đảm bảo trạng thái dữ liệu nhất quán.

Ví dụ:

```text
Application Node 1
        |
        X  Network Partition
        |
Application Node 2
```

Thay vì tiếp tục xử lý giao dịch và có nguy cơ tạo ra dữ liệu không nhất quán, hệ thống ưu tiên bảo vệ **Consistency**.

Do đó, chiến lược được lựa chọn là:

```text
CP = Consistency + Partition Tolerance
```

> CAP áp dụng cho hành vi của toàn bộ hệ thống phân tán khi xảy ra network partition, không có nghĩa đơn giản rằng "MySQL luôn là CP".

---

# 4. Architecture Diagram

Kiến trúc hệ thống:

```text
                         Client
                            |
                            v
                     +-------------+
                     | API Gateway |
                     +------+------+
                            |
                 +----------+----------+
                 |                     |
                 v                     v
        +----------------+    +----------------+
        | Application    |    | Application    |
        |    Node 1      |    |    Node 2      |
        +-------+--------+    +-------+--------+
                |                     |
                +----------+----------+
                           |
                           v
                    +-------------+
                    |    MySQL    |
                    +-------------+
```

### Giải thích

- **Client:** gửi request đến hệ thống.
- **API Gateway:** tiếp nhận và phân phối request.
- **Application Node 1 / Node 2:** các instance của ứng dụng chạy phân tán.
- **MySQL:** lưu trữ dữ liệu tài khoản.
- Nhiều Application Node giúp hệ thống có khả năng chạy trên nhiều node.

---

# 5. Bonus: Implement Small API

Hệ thống cung cấp 2 API đơn giản để demo.

### API 1: Tạo tài khoản

```http
POST /accounts
```

Request:

```json
{
  "ownerName": "Nguyen Van A",
  "initialBalance": 1000000
}
```

---

### API 2: Chuyển tiền

```http
PO
```
