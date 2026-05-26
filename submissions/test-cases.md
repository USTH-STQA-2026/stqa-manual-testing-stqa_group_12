# Test Cases — Bảng trường hợp kiểm thử

> **Hướng dẫn**: Viết tối thiểu **20 TC** phủ đủ các chức năng chính (REQ-01 → REQ-08).
> Xem [examples/sample-test-case.md](../examples/sample-test-case.md) để hiểu cách viết TC tốt.
> Tự tổ chức và phân nhóm test case theo cách hợp lý nhất.

| Thông tin | |
|---|---|
| **Nhóm** | Group 12 |
| **Ngày tạo** | 20/5/2026 |
| **Hệ thống** | https://stqa.rbc.vn |
| **Tham chiếu** | SRS v1.0 |

---

## Bước 1: Mô hình hóa miền đầu vào — Input Domain Modeling (IDM)

> 📖 **Textbook:** Chương 6 — *Input Domain Modeling*, Paul Ammann & Jeff Offutt.
>
> **Trước khi viết Test Case**, nhóm **phải** phân tích miền đầu vào bằng bảng IDM bên dưới.
> Mỗi chức năng cần xác định: **Đặc tính (Characteristic)**, **Phân vùng (Block/Partition)**, và **Giá trị đại diện (Value)**.

### IDM — Đăng nhập (REQ-01)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Email có tồn tại trong DB? | Có | `librarian@library.com` | Đăng nhập thành công |
| | Không | `nobody@test.com` | Thông báo lỗi |
| Mật khẩu có đúng? | Đúng | `admin123` | Đăng nhập thành công |
| | Sai | `wrongpass` | Thông báo lỗi |
| Ô nhập có rỗng? | Không rỗng | (giá trị bất kỳ) | Xử lý bình thường |
| | Rỗng | `""` | Thông báo "Vui lòng nhập..." |

### IDM — Tìm kiếm sách (REQ-03)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Từ khóa có tồn tại trong DB? | Có (tên sách) | `"Flutter"` | Hiển thị sách chứa "Flutter" |
| | Có (tên tác giả) | `"Nguyễn"` | Hiển thị sách của tác giả Nguyễn |
| | Không | `"XYZ123"` | Danh sách rỗng |
| Phân biệt HOA/thường? | Chữ thường | `"flutter"` | Kết quả giống "Flutter" |
| | Chữ HOA | `"FLUTTER"` | Kết quả giống "Flutter" |

### IDM – Mượn sách (REQ-04)

| Đặc tính | Phân vùng | Giá trị đại diện | Kết quả mong đợi |
|----------|-----------|------------------|------------------|
| Trạng thái sách | Có sẵn | BOOK001 | Mượn thành công |
| | Đã mượn | BOOK003 | "Sách đã được mượn" |
| | Thất lạc | BOOK007 | "Sách không khả dụng (thất lạc)" |
| Số sách đang mượn | 0, 1, 2 | Mượn thêm | Mượn thành công |
| | 3 (biên trên) | Mượn sách thứ 4 | "Đã đạt giới hạn 3 sách" |
| Trạng thái tài khoản | Hoạt động | `ba.nguyen@email.com` | Mượn được |
| | Tạm ngưng (Suspended) | `cu.le@email.com` | **"Tài khoản đang bị tạm ngưng"** |
| | Hết hạn (Expired) | `binh.pham@email.com` | **"Thẻ thư viện đã hết hạn"** |

#### Bảng Decision Table:

| | Rule 1 | Rule 2 | Rule 3 | Rule 4 | Rule 5 | Rule 6 |
|---|---|---|---|---|---|---|
| **Điều kiện** | | | | | | |
|  Sách "Có sẵn"? | Y | N | Y | Y | Y | Y |
|  Số sách đang mượn < 3? | Y | Y | N | Y | Y | Y |
|  Tài khoản Hoạt động? | Y | Y | Y | N | N | N |
|  Tài khoản Tạm ngưng? | N | N | N | Y | N | N |
|  Tài khoản Hết hạn? | N | N | N | N | Y | N |
| **Hành động** | | | | | | |
|  Mượn thành công | ✓ | | | | | |
|  "Sách đã được mượn" | | ✓ | | | | |
|  "Đã đạt giới hạn 3 sách" | | | ✓ | | | |
|  "Tài khoản đang bị tạm ngưng" | | | | ✓ | | |
|  "Thẻ thư viện đã hết hạn" | | | | | ✓ | |



### IDM – Trả sách (REQ-05)

|Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|----------|-----------|------------------|------------------|
| Trạng thái mượn của sách | Đang mượn (EP) | BR001 (Kiểm thử phần mềm) | Trả thành công, sách thành "Có sẵn" |
| | Chưa mượn (EP) | BOOK001 (chưa ai mượn) | Thông báo lỗi "Không tìm thấy phiếu mượn" |
| So sánh ngày trả với hạn trả | Trả đúng hạn (EP) | BR003 (hạn 15/10/2024) | Trả bình thường, không cảnh báo |
| | Trả quá hạn (BVA - biên dưới) | Ngày trả = hạn trả + 1 ngày | Cảnh báo "Sách trả quá hạn" |
| | Trả quá hạn (EP) | BR001 (hạn 15/09/2024) | Cảnh báo "Sách trả quá hạn" |

### IDM – Xử lý sách quá hạn (REQ-06)

|Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|----------|-----------|------------------|------------------|
| Vai trò người dùng | Thủ thư (EP) | `librarian@library.com` | Có nút "Kiểm tra sách quá hạn" |
| | Thành viên (EP) | `ba.nguyen@email.com` | Không thấy nút "Kiểm tra sách quá hạn" |
| Ngày hết hạn so với ngày hiện tại | Còn hạn (EP) | BR003 (hạn 15/10/2024) | Không bị đánh dấu quá hạn |
| | Vừa hết hạn (BVA - biên) | Hạn trả = hôm qua  | Đánh dấu "Quá hạn" |
| | Quá hạn (EP) | BR001 (hạn 15/09/2024) | Đánh dấu "Quá hạn" |


### IDM – Quản lý thành viên (REQ-07) - Chỉ Thủ thư ///Hỏi thầy chưa xong

|Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|----------|-----------|------------------|------------------|
| Vai trò người dùng | Thủ thư (EP) | `librarian@library.com` | Thấy mục "Thành viên" |
| | Thành viên (EP) | `ba.nguyen@email.com` | Không thấy mục "Thành viên" |
| Xác thực email | Hợp lệ (EP) | `user@domain.com` | Tạo thành công |
| | Thiếu dấu chấm (BVA) | `user@domain` | Thông báo "Email không hợp lệ" |
| | Thiếu @ (EP) | `userdomain.com` | Thông báo "Email không hợp lệ" |
| | Rỗng (EP) | `""` | Thông báo "Vui lòng nhập email" |
| Email có bị trùng không? | Chưa tồn tại (EP) | `user@domain.com` | Tạo thành công |
| | Đã tồn tại (EP) | `ba.nguyen@email.com` | Thông báo "Email đã tồn tại" |

### IDM – Tra cứu phiếu mượn (REQ-08)

|Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|----------|-----------|------------------|------------------|
| Vai trò người dùng | Thủ thư (EP) | `librarian@library.com` | Xem được tất cả phiếu mượn(5 phiếu) |
| | Thành viên (EP) | `ba.nguyen@email.com` | Chỉ xem được phiếu của mình (BR001, BR004) |
| Mã phiếu (Thủ thư) | Mã tồn tại (EP) | `MEM002` | Hiển thị phiếu của MEM002 |
| | Mã không tồn tại (EP) | `ABC002` | Thông báo "Không tìm thấy thành viên" |
| | Mã rỗng (BVA) | `""` | Thông báo "Nhập mã thành viên(VD: MEM001)" |

## Giải thích kỹ thuật thiết kế kiểm thử

### 1. Tại sao chọn EP (Equivalence Partitioning)?

**Lý do:** + Inputs in the same partition produce equivalent behavior
              → Only need ONE representative per partition (Session 2)
           + Hệ thống có nhiều đầu vào có thể chia thành các phân vùng tương đương, mỗi phân vùng có hành vi giống nhau.

**Áp dụng cho REQ:**
- **REQ-01 (Đăng nhập):** Email tồn tại / không tồn tại, mật khẩu đúng/sai
- **REQ-04 (Mượn sách):** Trạng thái sách (Có sẵn/Đã mượn/Thất lạc), trạng thái tài khoản (Hoạt động/Tạm ngưng/Hết hạn)
- **REQ-07 (Quản lý thành viên):** Email hợp lệ/không hợp lệ, email trùng/chưa trùng

**Hiệu quả:** EP reduces test count dramatically – without losing coverage!
              Giảm số lượng test case nhưng vẫn đảm bảo phủ đủ các tình huống.

### 2. Tại sao chọn BVA (Boundary Value Analysis)?

**Lý do:** + Most bugs cluster at the edges of partitions
           Off-by-one errors, wrong operators (> vs ≥), missing edge case
           + Hệ thống có các ràng buộc về số lượng và ngày tháng, lỗi thường xảy ra ở giá trị biên.
    -> Boundaries are where bugs hide. BVA finds them.
       Always test: boundary value, boundary-1, boundary+1

**Áp dụng cho REQ:**
- **REQ-04 (Mượn sách):** Giới hạn tối đa 3 sách/thành viên. Test tại biên: khi đang mượn 2 sách (mượn thêm thành công) và khi đang mượn 3 sách (mượn thêm thất bại)
- **REQ-05 (Trả sách):** Ngày trả so với hạn trả. Test tại biên: trả đúng hạn vs trả quá hạn 1 ngày
- **REQ-03 (Tìm kiếm):** Phân biệt hoa/thường. Test tại biên: "Flutter" vs "flutter"

**Hiệu quả:** Phát hiện lỗi ở ranh giới giữa các phân vùng.

### 3. Tại sao chọn Decision Table?

**Lý do:** REQ-04 (Mượn sách) có nhiều điều kiện kết hợp với nhau: trạng thái sách, số sách đang mượn, trạng thái tài khoản. Các điều kiện này phụ thuộc lẫn nhau, tạo ra nhiều tình huống khác nhau.

**Áp dụng cho REQ:**
- **REQ-04 (Mượn sách):** Kết hợp 3 điều kiện (Sách có sẵn?, Số sách < 3?, Tài khoản hoạt động?) cho ra các hành động khác nhau.

**Hiệu quả:** Đảm bảo không bỏ sót tổ hợp điều kiện nào, đặc biệt là các trường hợp ngoại lệ.


> 💡 **Gợi ý kỹ thuật**: Sử dụng **Phân lớp tương đương (EP)** cho các phân vùng rời rạc, **Phân tích giá trị biên (BVA)** cho các phân vùng số (ví dụ: giới hạn 3 sách). Xem textbook §6.1–6.3.

---

## Bước 2: Test Cases

<!-- Tự tổ chức bảng test case: có thể chia nhóm theo chức năng, theo REQ, hoặc theo luồng nghiệp vụ — tùy nhóm quyết định. -->
<!-- Mỗi TC phải ánh xạ ngược về ít nhất 1 dòng trong bảng IDM ở Bước 1. -->

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| | | | | | | | |

---

## Tổng hợp

| Nhóm chức năng | Số TC | REQ phủ | Kỹ thuật IDM áp dụng |
|----------------|-------|---------|----------------------|
| | | | |
| **Tổng** | **<!-- ≥ 20 -->** | | |
