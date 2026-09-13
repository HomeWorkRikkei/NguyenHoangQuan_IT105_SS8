# THỰC HÀNH TRÍCH XUẤT VÀ XÂY DỰNG CLASS DIAGRAM HỆ THỐNG RIKKEISHOP

## 1. Mô tả nghiệp vụ (đề bài)

> "Mỗi Khách hàng (Customer) có các thông tin như Mã khách hàng, Họ tên, Số điện thoại. Khách hàng có thể thực hiện đặt hàng (placeOrder). Mỗi Đơn hàng (Order) bao gồm Mã đơn hàng, Ngày tạo, và có thể tính tổng tiền (calculateTotal). Một khách hàng có thể có nhiều đơn hàng, nhưng mỗi đơn hàng chỉ thuộc về một khách hàng duy nhất."

## 2. Bước 1: Trích xuất Lớp, Thuộc tính và Phương thức

Nguyên tắc trích xuất: **danh từ → Lớp/Thuộc tính**, **động từ → Phương thức**.

| Thành phần | Lớp Customer | Lớp Order |
|---|---|---|
| **Thuộc tính (Attributes)** | customerId, fullName, phoneNumber | orderId, orderDate |
| **Phương thức (Methods)** | placeOrder() | calculateTotal() |

**Giải thích:**
- "Mã khách hàng, Họ tên, Số điện thoại" mô tả *đặc điểm tĩnh* của đối tượng Customer → là attribute.
- "có thể thực hiện đặt hàng" là *hành vi* mà Customer thực hiện → method `placeOrder()`.
- "Mã đơn hàng, Ngày tạo" là attribute của Order.
- "có thể tính tổng tiền" là hành vi của Order → method `calculateTotal()`.

## 3. Bước 2: Access Modifiers và Multiplicity

### 3.1 Access Modifier (nguyên tắc Encapsulation)

| Thành phần | Modifier | Ký hiệu UML | Lý do |
|---|---|---|---|
| Tất cả thuộc tính (customerId, fullName, phoneNumber, orderId, orderDate) | private | `-` | Dữ liệu nội bộ, không cho truy cập trực tiếp từ bên ngoài, đảm bảo tính đóng gói (encapsulation) |
| Tất cả phương thức (placeOrder, calculateTotal) | public | `+` | Là hành vi mà đối tượng khác (hệ thống, UI, service khác) cần gọi tới để tương tác với đối tượng |

### 3.2 Multiplicity (Bội số) trong quan hệ Association

```
Customer  "1" ──────────── "0..*"  Order
```

- Phía **Customer = 1**: một Order chỉ thuộc về đúng **một** Customer.
- Phía **Order = 0..\***: một Customer có thể có **0 hoặc nhiều** Order.
- Đây là quan hệ **Association** (kết hợp thông thường), không phải Aggregation/Composition, vì:
  - Order không phải là "một phần cấu thành" của Customer (không có quan hệ whole-part).
  - Order vẫn có thể tồn tại độc lập về mặt vòng đời với Customer (chỉ liên kết dữ liệu qua khóa ngoại customerId).

## 4. Bước 3: Class Diagram

Cấu trúc mỗi lớp gồm 3 ngăn theo chuẩn UML:

```
┌──────────────────────────┐                    ┌──────────────────────────┐
│         Customer          │                    │           Order           │
├──────────────────────────┤                    ├──────────────────────────┤
│ - customerId: String      │                    │ - orderId: String         │
│ - fullName: String        │                    │ - orderDate: Date         │
│ - phoneNumber: String     │                    │                           │
├──────────────────────────┤   1        0..*     ├──────────────────────────┤
│ + placeOrder(): void      │───────────────────│ + calculateTotal(): double│
└──────────────────────────┘                    └──────────────────────────┘
```

File diagram gốc (.drawio) đi kèm: `bai_class_diagram_rikkeishop.drawio`

## 5. Ghi chú khi vẽ trong draw.io

- Mỗi lớp là 1 hình chữ nhật 3 ngăn: Tên lớp / Thuộc tính (kèm kiểu dữ liệu) / Phương thức (kèm kiểu trả về).
- Đường nối Association là đường thẳng không mũi tên (endArrow=none), có ghi số bội số (multiplicity) ở hai đầu.
- Toàn bộ diagram được đặt trong 1 khung Pool lớn có tiêu đề "RikkeiShop - Class Diagram" để thể hiện phạm vi hệ thống.
