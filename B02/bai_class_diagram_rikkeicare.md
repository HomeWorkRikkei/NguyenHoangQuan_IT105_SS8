# THỰC HÀNH PHÁT HIỆN LỖI THIẾT KẾ VÀ TÁI CẤU TRÚC CLASS DIAGRAM HỆ THỐNG RIKKEICARE

## Bước 1: Soi lỗi thiết kế

**Thuộc tính bị khai báo trùng lặp ở cả 2 lớp BacSi và YTa:**
- `maNhanVien: String`
- `hoTen: String`
- `soDienThoai: String`

**Hậu quả khi cần sửa cấu trúc thuộc tính `hoTen` cho toàn bộ nhân viên:**

> Vì `hoTen` được khai báo lặp lại độc lập ở cả hai lớp BacSi và YTa, mọi thay đổi (đổi kiểu dữ liệu, tách thành firstName/lastName, thêm ràng buộc...) đều phải sửa ở nhiều nơi cùng lúc, dễ bỏ sót và gây sai lệch dữ liệu giữa các lớp — vi phạm nguyên tắc DRY (Don't Repeat Yourself) và làm tăng chi phí bảo trì hệ thống, đặc biệt khi sau này có thêm các loại nhân viên y tế khác.

**Nguyên nhân gốc rễ:** BacSi và YTa cùng là chuyên hóa (specialization) của một khái niệm chung "Nhân viên y tế", nhưng bản thiết kế ban đầu chưa áp dụng cơ chế **Inheritance (Kế thừa)** để gom nhóm phần chung.

## Bước 2: Tái cấu trúc bằng Inheritance

| Lớp cha (Superclass) | Lớp con (Subclass) | Thuộc tính dùng chung (Lớp cha) | Thuộc tính riêng biệt (Lớp con) |
|---|---|---|---|
| NhanVienYTe | BacSi | maNhanVien, hoTen, soDienThoai | **chuyenKhoa** |
| NhanVienYTe | YTa | maNhanVien, hoTen, soDienThoai | khuVucTruc |

- Lớp cha `NhanVienYTe` giữ 3 thuộc tính dùng chung.
- `BacSi` kế thừa `NhanVienYTe`, chỉ còn `chuyenKhoa` + phương thức `kham(benhNhan): void`.
- `YTa` kế thừa `NhanVienYTe`, chỉ còn `khuVucTruc` + phương thức `tiemThuoc(benhNhan): void`.
- Khi cần sửa `hoTen`, chỉ sửa 1 lần tại lớp cha, các lớp con tự động thừa hưởng.

## Bước 3: Quan hệ Aggregation giữa KhoaKham và NhanVienYTe

**Loại quan hệ:** Aggregation (Tổng hợp) — ký hiệu hình thoi **rỗng** (◇) đặt tại đầu `KhoaKham`.

**Lý do không phải Composition:** Đề bài nêu rõ nhân viên y tế vẫn tồn tại độc lập, có thể điều chuyển sang khoa khác khi khoa cũ giải thể → không có ràng buộc vòng đời chặt (whole-part) → đây là Aggregation, không phải Composition.

**Bội số:**
```
KhoaKham  "1"  ◇────────  "1..*"  NhanVienYTe
```
- Một KhoaKham có từ 1 đến nhiều (1..*) NhanVienYTe.
- Một NhanVienYTe thuộc về 1 KhoaKham tại một thời điểm.

## Bước 4: Class Diagram sau tái cấu trúc (4 lớp)

File diagram gốc (.drawio) đi kèm: `bai_class_diagram_rikkeicare.drawio`

```
                    ┌───────────────────┐
                    │    NhanVienYTe      │
                    ├───────────────────┤
                    │ - maNhanVien: String│
                    │ - hoTen: String      │
                    │ - soDienThoai: String│
                    └───────────────────┘
                         △        △        (Generalization - tam giác rỗng)
              ┌──────────┘        └──────────┐
    ┌───────────────────┐          ┌───────────────────┐
    │       BacSi          │          │        YTa           │
    ├───────────────────┤          ├───────────────────┤
    │ - chuyenKhoa: String │          │ - khuVucTruc: String │
    ├───────────────────┤          ├───────────────────┤
    │ + kham(benhNhan)      │          │ + tiemThuoc(benhNhan) │
    └───────────────────┘          └───────────────────┘

    ┌───────────────────┐
    │      KhoaKham        │  "1"  ◇──────  "1..*"  NhanVienYTe (ở trên)
    ├───────────────────┤
    │ - maKhoa: String     │
    │ - tenKhoa: String    │
    └───────────────────┘
```

- **Kế thừa (Inheritance):** mũi tên tam giác rỗng, nét liền, từ BacSi và YTa hướng lên NhanVienYTe.
- **Aggregation:** đường nối có hình thoi rỗng tại đầu KhoaKham, tới NhanVienYTe, kèm bội số 1 và 1..*.
