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
| | Không | `noone@email.com` | Thông báo lỗi |
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
| Số sách đang mượn | 0, 1, 2 | Mượn  | Mượn thành công |
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

| Đặc tính | Phân vùng | Giá trị đại diện | Kết quả mong đợi |
|----------|-----------|------------------|------------------|
| Sách có đang được mượn không? | Có (đang mượn) | BR001 (Kiểm thử phần mềm nhập môn) | Trả thành công, hiển thị thông báo "Trả sách thành công", sách chuyển từ "Đã mượn" → "Có sẵn", phiếu mượn có thêm dòng "Ngày trả" = ngày hiện tại (ngày người dùng click trả sách) |
| | Không (chưa mượn) | Có sẵn + (VD: BOOK001 sau khi reset) | Không tìm thấy phiếu mượn để trả, hiển thị thông báo lỗi: "Không tìm thấy phiếu mượn" |
| Ngày trả so với hạn trả | Trả đúng hạn | BR003 (hạn trả 15/10/2024, trả trước ngày 15/10/2024) | Trả thành công, sách chuyển thành "Có sẵn", KHÔNG có cảnh báo quá hạn |
| | Trả quá hạn (BVA - biên dưới) | Hạn trả = 20/05/2026, trả vào ngày 21/05/2026 (quá hạn 1 ngày) | Trả thành công, sách chuyển thành "Có sẵn", VÀ hiển thị thêm cảnh báo: "Sách trả quá hạn" |
| | Trả quá hạn (EP) | BR001 (hạn trả 15/09/2024, trả vào ngày hôm nay - đã quá hạn gần 2 năm) | Trả thành công, sách chuyển thành "Có sẵn", VÀ hiển thị thêm cảnh báo: "Sách trả quá hạn" |


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
### REQ-01: Đăng nhập

#### TC-01: Đăng nhập thành công - Thủ thư

| **Mã TC** | TC-01 |
| **Mục tiêu** | Kiểm tra đăng nhập với email và mật khẩu đúng |
| **Tiền điều kiện** | Chưa đăng nhập, mở https://stqa.rbc.vn/ |
| **Bước thực hiện** | 1. Nhập email: `librarian@library.com`2. Nhập mật khẩu: `admin123` 3. Click "Đăng nhập" |
| **Dữ liệu đầu vào** | Email = `librarian@library.com`, Mật khẩu = `admin123` |
| **Expected Result** | Chuyển sang trang chủ, AppBar hiển thị "Nguyễn Thủ Thư (Thủ thư)", xuất hiện 3 tab: Sách, Mượn/Trả, Thành viên |
| **Ánh xạ IDM** | IDM REQ-01: Email tồn tại = Có (`librarian@library.com`), Mật khẩu đúng = Đúng (`admin123`) |
| **REQ** | REQ-01 |
| **Kỹ thuật** | EP |

#### TC-02: Đăng nhập thất bại - Sai email

| **Mã TC** | TC-02 |
| **Mục tiêu** | Kiểm tra lỗi khi email không tồn tại |
| **Tiền điều kiện** | Chưa đăng nhập |
| **Bước thực hiện** | 1. Nhập email: `sai@email.com`2. Nhập mật khẩu: `admin123`<br>3. Click "Đăng nhập" |
| **Dữ liệu đầu vào** | Email = `sai@email.com`, Mật khẩu = `admin123` |
| **Expected Result** | Ở lại màn hình đăng nhập, hiển thị "Không tìm thấy thành viên" |
| **Ánh xạ IDM** | IDM REQ-01: Email tồn tại = Không (`noone@email.com`) |
| **REQ** | REQ-01 |
| **Kỹ thuật** | EP |

#### TC-03: Đăng nhập thất bại - Sai mật khẩu

| **Mã TC** | TC-03 |
| **Mục tiêu** | Kiểm tra lỗi khi mật khẩu sai |
| **Tiền điều kiện** | Chưa đăng nhập |
| **Bước thực hiện** | 1. Nhập email: `librarian@library.com`2. Nhập mật khẩu: `sai123`3. Click "Đăng nhập" |
| **Dữ liệu đầu vào** | Email = `librarian@library.com`, Mật khẩu = `sai123` |
| **Expected Result** | Ở lại màn hình đăng nhập, hiển thị "Mật khẩu không đúng" |
| **Ánh xạ IDM** | IDM REQ-01: Mật khẩu đúng = Sai (`wrongpass`) |
| **REQ** | REQ-01 |
| **Kỹ thuật** | EP |

#### TC-04: Đăng nhập thất bại - Bỏ trống email và mật khẩu

| **Mã TC** | TC-04 |
| **Mục tiêu** | Kiểm tra lỗi khi bỏ trống email và mật khẩu |
| **Tiền điều kiện** | Chưa đăng nhập |
| **Bước thực hiện** | 1. Để trống email<br>2. Để trống mật khẩu<br>3. Click "Đăng nhập" |
| **Dữ liệu đầu vào** | Email = `""`, Mật khẩu = `""` |
| **Expected Result** | Hiển thị "Vui lòng nhập email và mật khẩu" |
| **Ánh xạ IDM** | IDM REQ-01: Email rỗng + Mật khẩu rỗng |
| **REQ** | REQ-01 |
| **Kỹ thuật** | EP |

### REQ-02: Xem danh sách sách

#### TC-05: Xem danh sách sách

| **Mã TC** | TC-05 |
| **Mục tiêu** | Kiểm tra hiển thị danh sách sách |
| **Tiền điều kiện** | Đã đăng nhập |
| **Bước thực hiện** | Vào tab "Sách" |
| **Dữ liệu đầu vào** | - |
| **Expected Result** | Hiển thị 20 sách (BOOK001-BOOK020) với từng mục: Tên sách, Tác giả, Thể loại, Năm XB, Mã, Trạng thái. VD: BOOK001 = "Đang mượn", BOOK003 = "Có sẵn" |
| **Ánh xạ IDM** | IDM REQ-02: Quyền truy cập = Thủ thư/Thành viên, Số lượng sách = 20, Trạng thái sách = Có sẵn/Đã mượn |
| **REQ** | REQ-02 |
| **Kỹ thuật** | EP |

### REQ-03: Tìm kiếm và lọc sách

#### TC-06: Tìm kiếm theo tên - Có kết quả

| **Mã TC** | TC-06 |
| **Mục tiêu** | Tìm kiếm sách theo tên |
| **Tiền điều kiện** | Đã đăng nhập, đang ở tab Sách |
| **Bước thực hiện** | Gõ "Flutter" vào ô tìm kiếm, nhấn Enter |
| **Dữ liệu đầu vào** | Từ khóa = "Flutter" |
| **Expected Result** | Hiển thị 1 dòng: "Lập trình Flutter cơ bản" |
| **Ánh xạ IDM** | IDM REQ-03: Từ khóa tìm kiếm = Có kết quả ("Flutter") |
| **REQ** | REQ-03 |
| **Kỹ thuật** | EP |

#### TC-07: Tìm kiếm - Không có kết quả

| **Mã TC** | TC-07 |
| **Mục tiêu** | Thông báo khi không tìm thấy |
| **Tiền điều kiện** | Đã đăng nhập, đang ở tab Sách |
| **Bước thực hiện** | Gõ "xyzxyz123", nhấn Enter |
| **Dữ liệu đầu vào** | Từ khóa = "xyzxyz123" |
| **Expected Result** | Hiển thị "Không tìm thấy sách nào." |
| **Ánh xạ IDM** | IDM REQ-03: Từ khóa tìm kiếm = Không kết quả |
| **REQ** | REQ-03 |
| **Kỹ thuật** | EP |

#### TC-08: Tìm kiếm không phân biệt hoa/thường (BVA)

| **Mã TC** | TC-08 |
| **Mục tiêu** | Kiểm tra case-insensitive |
| **Tiền điều kiện** | Đã đăng nhập, đang ở tab Sách |
| **Bước thực hiện** | Gõ "flutter" (chữ thường), nhấn Enter |
| **Dữ liệu đầu vào** | Từ khóa = "flutter" |
| **Expected Result** | Kết quả giống hệt TC-06 (vẫn ra sách Flutter) |
| **Ánh xạ IDM** | IDM REQ-03: Phân biệt hoa/thường = Chữ thường / Chữ HOA |
| **REQ** | REQ-03 |
| **Kỹ thuật** | BVA |

#### TC-09: Lọc theo thể loại

| **Mã TC** | TC-09 |
| **Mục tiêu** | Lọc sách theo thể loại |
| **Tiền điều kiện** | Đã đăng nhập, đang ở tab Sách |
| **Bước thực hiện** | Chọn "Văn học" từ dropdown lọc |
| **Dữ liệu đầu vào** | Thể loại = "Văn học" |
| **Expected Result** | Chỉ hiển thị BOOK019, BOOK020 |
| **Ánh xạ IDM** | IDM REQ-03: Lọc thể loại = Văn học |
| **REQ** | REQ-03 |
| **Kỹ thuật** | EP |

#### TC-10: Lọc theo thể loại - Chữ thường (BVA)

| **Mã TC** | TC-10 |
| **Mục tiêu** | Kiểm tra lọc thể loại không phân biệt chữ hoa/thường |
| **Tiền điều kiện** | Đã đăng nhập, đang ở tab Sách |
| **Bước thực hiện** | 1. Gõ chữ thường "văn học" vào ô lọc thể loại (nếu là ô nhập)HOẶC chọn thể loại "Văn học" từ dropdown (nếu dropdown có chữ thường) |
| **Dữ liệu đầu vào** | Thể loại = "văn học" (chữ thường) |
| **Expected Result** | Kết quả hiển thị GIỐNG HỆT với TC-09 (gõ "Văn học"):<br>- Chỉ hiển thị sách thể loại Văn học: BOOK019, BOOK020<br>- **Không được** hiển thị "Không tìm thấy sách" |
| **Ánh xạ IDM** | IDM REQ-03: Phân biệt hoa/thường = Chữ thường (áp dụng cho lọc thể loại) |
| **REQ** | REQ-03 |
| **Kỹ thuật** | BVA |

#### TC-11: Kết hợp lọc và tìm kiếm - Lọc Văn học + Tìm "Flutter" (Negative)

| **Mã TC** | TC-11 |
| **Mục tiêu** | Kiểm tra kết hợp lọc thể loại và tìm kiếm với từ khóa khác |
| **Tiền điều kiện** | Đã đăng nhập, đang ở tab Sách |
| **Bước thực hiện** | 1. Chọn thể loại **"Văn học"** từ dropdown lọc<br>2. Trong ô tìm kiếm, gõ từ khóa **"Flutter"**<br>3. Nhấn Enter |
| **Dữ liệu đầu vào** | Thể loại = "Văn học", Từ khóa = "Flutter" |
| **Expected Result** | - **Không hiển thị bất kỳ sách nào** (vì sách Flutter thuộc thể loại Công nghệ, không phải Văn học)<br>- Hiển thị thông báo: **"Không tìm thấy sách"** |
| **Ánh xạ IDM** | IDM REQ-03: Tìm kiếm + Lọc kết hợp (giao nhau rỗng) |
| **REQ** | REQ-03 |
| **Kỹ thuật** | EP (Negative - kết hợp điều kiện) |
| **Kết luận** | BUG-003 |
---
#### TC-12: Kết hợp lọc và tìm kiếm - Lọc Văn học + Tìm "Văn" (Positive)

| **Mã TC** | TC-12 |
| **Mục tiêu** | Kiểm tra kết hợp lọc và tìm kiếm trả về kết quả đúng |
| **Tiền điều kiện** | Đã đăng nhập, đang ở tab Sách |
| **Bước thực hiện** | 1. Chọn thể loại **"Văn học"** từ dropdown lọc<br>2. Trong ô tìm kiếm, gõ từ khóa **"Văn"**<br>3. Nhấn Enter |
| **Dữ liệu đầu vào** | Thể loại = "Văn học", Từ khóa = "Văn" |
| **Expected Result** | - Chỉ hiển thị các sách thể loại Văn học có chứa từ "Văn"<br>- Phải hiển thị **BOOK019: "Văn học Việt Nam đại cương"**<br>- **Không được** hiển thị BOOK020 (nếu BOOK020 không chứa từ "Văn") |
| **Ánh xạ IDM** | IDM REQ-03: Tìm kiếm + Lọc kết hợp (Có kết quả) |
| **REQ** | REQ-03 |
| **Kỹ thuật** | EP (Happy Path - kết hợp điều kiện) |
---

### REQ-04: Mượn sách

#### TC-13: Mượn sách thành công

| **Mã TC** | TC-13 |
| **Mục tiêu** | Mượn sách khi đủ điều kiện |
| **Tiền điều kiện** | - Đăng nhập `ba.nguyen@email.com` (tài khoản **Hoạt động**)<br>- Sách BOOK001 đang có trạng thái **"Có sẵn"**<br>- Thành viên `ba.nguyen` đang mượn **1 sách** (BOOK003 - BR001) |
| **Bước thực hiện** | 1. Vào tab "Sách"<br>2. Tìm sách BOOK001 "Lập trình Flutter cơ bản"<br>3. Click nút **"Mượn"** trên dòng sách<br>4. Hệ thống hiển thị hộp thoại xác nhận: **"Xác nhận mượn sách?"**<br>5. Click **"Mượn"**  |
| **Dữ liệu đầu vào** | Sách = BOOK001, Tài khoản = `ba.nguyen@email.com` |
| **Expected Result** | - Hiển thị thông báo: **"Mượn sách thành công"**<br>- Trạng thái sách BOOK001 chuyển từ "Có sẵn" → **"Đang mượn"**<br>- Số sách đang mượn của `ba.nguyen` tăng từ 1 lên **2**<br>- Xuất hiện phiếu mượn mới trong tab "Mượn/Trả" |
| **Ánh xạ IDM** | IDM REQ-04: Trạng thái sách = Có sẵn (BOOK001), Số sách đang mượn = 2, Tài khoản = Hoạt động |
| **REQ** | REQ-04 |
| **Kỹ thuật** | EP (Happy Path) |

#### TC-14: Mượn sách thất bại - Sách đã mượn

| **Mã TC** | TC-14 |
| **Mục tiêu** | Mượn sách đang có người mượn |
| **Tiền điều kiện** | Đăng nhập `ba.nguyen@email.com`, BOOK003 đang "Đang mượn" |
| **Bước thực hiện** | Tìm BOOK003, click "Mượn" |
| **Dữ liệu đầu vào** | Sách = BOOK003, Tài khoản = `ba.nguyen@email.com` |
| **Expected Result** | "Sách đã được mượn", trạng thái sách vẫn là "Đang mượn" |
| **Ánh xạ IDM** | IDM REQ-04: Trạng thái sách = Đã mượn (BOOK003) |
| **REQ** | REQ-04 |
| **Kỹ thuật** | EP (Negative) |

#### TC-15: Mượn sách thất bại - Giới hạn 3 sách (BVA)

| **Mã TC** | TC-15 |
| **Mục tiêu** | Kiểm tra giới hạn tối đa 3 sách |
| **Tiền điều kiện** | Đăng nhập `ba.nguyen@email.com`, đang mượn đúng 3 sách |
| **Bước thực hiện** | 1. Vào tab "Sách"<br>2. Tìm sách thứ 4 đang "Có sẵn" (ví dụ: BOOK004)<br>3. Click nút "Mượn"<br>4. Xác nhận mượn sách |
| **Dữ liệu đầu vào** | Số sách đang mượn = 3, mượn thêm sách thứ 4 |
| **Expected Result** (theo SRS) | - **Không thể mượn được sách thứ 4**<br>- Hiển thị thông báo: **"Đã đạt giới hạn 3 sách, không thể mượn thêm"**<br>- Số sách đang mượn vẫn là 3 |
| **Ánh xạ IDM** | IDM REQ-04: Số sách đang mượn = 3 (biên trên - BVA) |
| **REQ** | REQ-04 |
| **Kỹ thuật** | BVA |
| **Kết luận** |  **BUG** (Không áp dụng giới hạn 3 sách) |

#### TC-16: Mượn sách thất bại - Tài khoản tạm ngưng

| **Mã TC** | TC-16 |
| **Mục tiêu** | Kiểm tra lỗi với tài khoản Tạm ngưng |
| **Tiền điều kiện** | Đăng nhập `cu.le@email.com` (Tạm ngưng) |
| **Bước thực hiện** | Mượn bất kỳ sách "Có sẵn" |
| **Dữ liệu đầu vào** | Tài khoản = `cu.le@email.com` (Tạm ngưng) |
| **Expected Result** | "Thành viên hết hạn. Không thể mượn sách" |
| **Ánh xạ IDM** | IDM REQ-04: Trạng thái tài khoản = Tạm ngưng (Suspended) |
| **REQ** | REQ-04 |
| **Kỹ thuật** | EP (Negative) |

#### TC-17: Mượn sách thất bại - Thẻ hết hạn

| **Mã TC** | TC-17 |
| **Mục tiêu** | Kiểm tra lỗi với tài khoản Hết hạn |
| **Tiền điều kiện** | Đăng nhập `binh.pham@email.com` (Hết hạn) |
| **Bước thực hiện** | Mượn bất kỳ sách "Có sẵn" |
| **Dữ liệu đầu vào** | Tài khoản = `binh.pham@email.com` (Hết hạn) |
| **Expected Result** | "Thành viên hết hạn. Không thể mượn sách" |
| **Ánh xạ IDM** | IDM REQ-04: Trạng thái tài khoản = Hết hạn (Expired) |
| **REQ** | REQ-04 |
| **Kỹ thuật** | EP (Negative) |

---


### REQ-05: Trả sách

#### TC-18: Trả sách thành công (đúng hạn) - Đối chứng

| **Mã TC** | TC-18 |
| **Mục tiêu** | Kiểm tra trả sách đúng hạn không có cảnh báo |
| **Tiền điều kiện** | - Đăng nhập Thủ thư `librarian@library.com`<br>- Có phiếu mượn còn trong hạn (VD: BR003 - hạn trả 15/10/2024)<br>- Ngày hiện tại < 15/10/2024 |
| **Bước thực hiện** | 1. Vào tab **"Mượn/Trả"**<br>2. Tìm phiếu mượn BR003 (Quản trị nhân sự hiện đại)<br>3. Click nút **"Trả sách"**<br>4. Quan sát thông báo và ngày trả |
| **Dữ liệu đầu vào** | Phiếu mượn = BR003 (còn hạn) |
| **Expected Result** | - Hiển thị thông báo: **"Trả sách thành công"**<br>- **KHÔNG có** cảnh báo quá hạn<br>- Ngày trả = ngày hiện tại (ngày đang thực hiện test)<br>- Sách chuyển từ "Đã mượn" → "Có sẵn" |
| **Ánh xạ IDM** | IDM REQ-05: Trả đúng hạn |
| **REQ** | REQ-05 |
| **Kỹ thuật** | EP (Happy Path - đối chứng) |

#### TC-19: Trả sách quá hạn - Kiểm tra cảnh báo 

| **Mã TC** | TC-19 |
| **Mục tiêu** | Kiểm tra cảnh báo khi trả sách quá hạn |
| **Tiền điều kiện** | - Đăng nhập Thủ thư `librarian@library.com`<br>- Có phiếu mượn đã quá hạn (VD: BR001 - hạn trả 15/09/2024)<br>- Ngày hiện tại > 15/09/2024 |
| **Bước thực hiện** | 1. Vào tab **"Mượn/Trả"**<br>2. Tìm phiếu mượn BR001 (Kiểm thử phần mềm nhập môn)<br>3. Click nút **"Trả sách"**<br>4. Quan sát có cảnh báo quá hạn hay không |
| **Dữ liệu đầu vào** | Phiếu mượn = BR001 (hạn trả 15/09/2024 - đã quá hạn) |
| **Expected Result** (theo SRS REQ-05) | - Trả sách thành công<br>- **HIỂN THỊ CẢNH BÁO: "Sách trả quá hạn"** |
| **Actual Result** (hành vi thực tế - BUG) | - Trả sách thành công<br>- **KHÔNG có cảnh báo quá hạn** |
| **Ánh xạ IDM** | IDM REQ-05: Trả quá hạn (BVA) |
| **REQ** | REQ-05 |
| **Kỹ thuật** | BVA |
| **Kết luận** |  **BUG-001 (lỗi 1): Thiếu cảnh báo quá hạn** |
---

#### TC-20: Trả sách thất bại - Sách chưa được mượn

| **Mã TC** | TC-20 |
| **Mục tiêu** | Kiểm tra không thể trả sách khi chưa có phiếu mượn |
| **Tiền điều kiện** | - Đăng nhập Thủ thư `librarian@library.com`<br>- Sách BOOK001 đang có trạng thái **"Có sẵn"** (chưa ai mượn) |
| **Bước thực hiện** | 1. Vào tab **"Mượn/Trả"**<br>2. Tìm kiếm hoặc quan sát danh sách phiếu mượn |
| **Dữ liệu đầu vào** | Sách BOOK001 (chưa được mượn) |
| **Expected Result** | - **Không có phiếu mượn nào** tương ứng với sách BOOK001 trong danh sách<br>- Do không có phiếu mượn, **không có nút "Trả sách"** để click |
| **Ánh xạ IDM** | IDM REQ-05: Sách đang mượn = Không (Thủ thư) |
| **REQ** | REQ-05 |
| **Kỹ thuật** | EP (Negative) |

#### TC-21: Trả sách quá hạn (BVA)

| **Mã TC** | TC-21 |
| **Mục tiêu** | Kiểm tra cảnh báo quá hạn |
| **Tiền điều kiện** | Có phiếu mượn quá hạn (BR001 - hạn 15/09/2024) |
| **Bước thực hiện** | Trả BR001 |
| **Dữ liệu đầu vào** | Ngày trả > hạn trả (quá hạn) |
| **Expected Result** | Trả thành công + hiển thị thêm "Sách trả quá hạn" |
| **Ánh xạ IDM** | IDM REQ-05: Trả quá hạn (BVA) |
| **REQ** | REQ-05 |
| **Kỹ thuật** | BVA |

---

### REQ-06: Xử lý sách quá hạn

#### TC-22: Kiểm tra quá hạn - Lần đầu (Happy Path)

| **Mã TC** | TC-22 |
| **Mục tiêu** | Kiểm tra nút "Kiểm tra quá hạn" lần đầu phát hiện đúng phiếu quá hạn |
| **Tiền điều kiện** | Đăng nhập Thủ thư, có BR001 và (phiếu quá hạn khác) |
| **Bước thực hiện** | 1. Vào tab "Mượn/Trả"<br>2. Click "Kiểm tra quá hạn" lần đầu |
| **Expected Result** | Phát hiện và đánh dấu **2 phiếu quá hạn** |
| **Ánh xạ IDM** | IDM REQ-06: Quyền = Thủ thư, Phiếu quá hạn = Có |
| **REQ** | REQ-06 |
| **Kỹ thuật** | EP (Happy Path) |

#### TC-23: Kiểm tra quá hạn - Lần thứ hai

| **Mã TC** | TC-23 |
| **Mục tiêu** | Kiểm tra hành vi khi nhấn nút lần thứ hai |
| **Tiền điều kiện** | Đã nhấn "Kiểm tra quá hạn" ít nhất 1 lần trước đó |
| **Bước thực hiện** | 1. Click "Kiểm tra quá hạn" lần thứ hai |
| **Expected Result** | (Theo SRS) Vẫn phải quét lại và Phát hiện và đánh dấu **2 phiếu quá hạn**  |
| **Ánh xạ IDM** | IDM REQ-06: Quyền = Thủ thư, Phiếu quá hạn = Có |
| **REQ** | REQ-06 (Xử lí sách quá hạn) |
| **Kỹ thuật** | EP: Phân vùng "Có phiếu quá hạn" nhưng hệ thống lại rơi vào phân vùng "Không có phiếu quá hạn" → phát hiện BUG |
| **Kết luận** | **BUG** |
---

### REQ-07: Quản lý thành viên

#### TC-24: Thêm thành viên thất bại - Email hợp lệ bị từ chối (BUG)

| **Mã TC** | TC-24 |
| **Mục tiêu** | Kiểm tra thêm thành viên với email đúng định dạng (có @ và .com) |
| **Tiền điều kiện** | Đăng nhập Thủ thư |
| **Bước thực hiện** | 1. Vào tab "Thành viên"<br>2. Click "Thêm thành viên"<br>3. Nhập Email: `vana@email.com`<br>4. Nhập Họ tên, SĐT hợp lệ<br>5. Click "Lưu" |
| **Dữ liệu đầu vào** | Email = `vana@email.com` (có @ và .com) |
| **Expected Result** (theo SRS) |  Thêm thành công (vì email hợp lệ) |
| **REQ** | REQ-07 (Thêm thành viên thất bại) |
| **Kỹ thuật** | EP |
| **Kết luận** |  **BUG** (Email hợp lệ bị từ chối) |

---

#### TC-25: Thêm thành viên thành công - Email không hợp lệ vẫn tạo được (BUG)

| **Mã TC** | TC-25 |
| **Mục tiêu** | Kiểm tra thêm thành viên với email thiếu dấu . trong domain |
| **Tiền điều kiện** | Đăng nhập Thủ thư |
| **Bước thực hiện** | 1. Vào tab "Thành viên"<br>2. Click "Thêm thành viên"<br>3. Nhập Email: `vana@email` (thiếu .com)<br>4. Nhập Họ tên, SĐT hợp lệ<br>5. Click "Lưu" |
| **Dữ liệu đầu vào** | Email = `vana@email` (thiếu dấu .) |
| **Expected Result** (theo SRS) |  Báo lỗi: "Email không hợp lệ" (không tạo được) |
| **REQ** | REQ-07 |
| **Kỹ thuật** | BVA |
| **Kết luận** |  **BUG** (Email không hợp lệ vẫn tạo được) |

#### TC-: Thêm thành viên thất bại - Email trùng

| **Mã TC** | TC- |
| **Mục tiêu** | Email đã tồn tại |
| **Tiền điều kiện** | Đăng nhập Thủ thư |
| **Bước thực hiện** | Email="ba.nguyen@email.com" |
| **Dữ liệu đầu vào** | Email = `ba.nguyen@email.com` (đã tồn tại) |
| **Expected Result** | "Email đã tồn tại" |
| **Ánh xạ IDM** | IDM REQ-07: Email trùng = Đã tồn tại |
| **REQ** | REQ-07 |
| **Kỹ thuật** | EP (Negative) |

---

### REQ-08: Tra cứu phiếu mượn

#### TC-23: Thủ thư xem tất cả phiếu

| **Mã TC** | TC-23 |
| **Mục tiêu** | Thủ thư xem được tất cả phiếu |
| **Tiền điều kiện** | Đăng nhập Thủ thư |
| **Bước thực hiện** | Vào tab "Mượn/Trả" |
| **Dữ liệu đầu vào** | Quyền = Thủ thư |
| **Expected Result** | Hiển thị 5 phiếu: BR001, BR002, BR003, BR004, BR005 |
| **Ánh xạ IDM** | IDM REQ-08: Quyền xem = Thủ thư |
| **REQ** | REQ-08 |
| **Kỹ thuật** | EP (Happy Path) |

#### TC-24: Thành viên chỉ xem phiếu của mình

| **Mã TC** | TC-24 |
| **Mục tiêu** | Thành viên chỉ xem phiếu của chính mình |
| **Tiền điều kiện** | Đăng nhập `ba.nguyen@email.com` |
| **Bước thực hiện** | Vào tab "Mượn/Trả" |
| **Dữ liệu đầu vào** | Quyền = Thành viên (MEM002) |
| **Expected Result** | Chỉ hiển thị BR001, BR004 (phiếu của MEM002) |
| **Ánh xạ IDM** | IDM REQ-08: Quyền xem = Thành viên |
| **REQ** | REQ-08 |
| **Kỹ thuật** | EP |

### REQ-08: Tra cứu phiếu mượn

#### TC-25: Thủ thư tra cứu phiếu mượn theo mã thành viên - Mã tồn tại

| **Mã TC** | TC-25 |
| **Mục tiêu** | Kiểm tra Thủ thư tra cứu phiếu mượn theo mã thành viên có tồn tại |
| **Tiền điều kiện** | Đăng nhập Thủ thư `librarian@library.com` / `admin123` |
| **Bước thực hiện** | 1. Vào tab **"Mượn/Trả"**<br>2. Tìm ô nhập mã thành viên để tra cứu<br>3. Nhập mã `MEM002`<br>4. Click nút **"Tra cứu"** |
| **Dữ liệu đầu vào** | Mã thành viên = `MEM002` (ba.nguyen) |
| **Expected Result** | - Hiển thị các phiếu mượn của thành viên MEM002<br>- Gồm 2 phiếu: **BR001** (đang mượn BOOK003) và **BR004** (đã trả BOOK005) |
| **Ánh xạ IDM** | IDM REQ-08: Tra cứu theo mã thành viên = Mã tồn tại |
| **REQ** | REQ-08 |
| **Kỹ thuật** | EP (Happy Path) |

---

#### TC-26: Thủ thư tra cứu phiếu mượn theo mã thành viên - Mã không tồn tại

| **Mã TC** | TC-26 |
| **Mục tiêu** | Kiểm tra thông báo khi tra cứu mã thành viên không tồn tại |
| **Tiền điều kiện** | Đăng nhập Thủ thư |
| **Bước thực hiện** | 1. Vào tab **"Mượn/Trả"**<br>2. Nhập mã `XXX999`<br>3. Click nút **"Tra cứu"** |
| **Dữ liệu đầu vào** | Mã thành viên = `XXX999` (không tồn tại) |
| **Expected Result** | - Không hiển thị phiếu mượn nào<br>- Hiển thị thông báo: **"Không tìm thấy phiếu mượn"** |
| **Ánh xạ IDM** | IDM REQ-08: Tra cứu mã = Không tồn tại |
| **REQ** | REQ-08 |
| **Kỹ thuật** | EP (Negative) |

---

#### TC-27: Thủ thư tra cứu phiếu mượn theo mã thành viên - Mã rỗng (BVA)

| **Mã TC** | TC-27 |
| **Mục tiêu** | Kiểm tra thông báo khi tra cứu với mã rỗng |
| **Tiền điều kiện** | Đăng nhập Thủ thư |
| **Bước thực hiện** | 1. Vào tab **"Mượn/Trả"**<br>2. Để trống ô nhập mã thành viên<br>3. Click nút **"Tra cứu"** |
| **Dữ liệu đầu vào** | Mã thành viên = `""` (rỗng) |
| **Expected Result** | Hiển thị thông báo: **"Vui lòng nhập mã thành viên"** |
| **Ánh xạ IDM** | IDM REQ-08: Tra cứu mã = Rỗng (BVA) |
| **REQ** | REQ-08 |
| **Kỹ thuật** | BVA |
| **Kết luận** | **BUG-006**  |


---

#### TC-28: Thành viên cố gắng tra cứu phiếu mượn của người khác

| **Mã TC** | TC-28 |
| **Mục tiêu** | Kiểm tra quyền: Thành viên không thể xem phiếu mượn của người khác |
| **Tiền điều kiện** | Đăng nhập `ba.nguyen@email.com` (thành viên MEM002) |
| **Bước thực hiện** | 1. Vào tab **"Mượn/Trả"**<br>2. Tìm kiếm hoặc cố gắng xem phiếu mượn của thành viên khác (VD: MEM003 - dam.tran)<br>3. Nhập mã `MEM003` vào ô tra cứu (nếu có)<br>4. Click "Tra cứu" |
| **Dữ liệu đầu vào** | Quyền = Thành viên (MEM002), tra cứu mã = MEM003 (người khác) |
| **Expected Result** (theo SRS REQ-08) | - **Không thể xem được phiếu mượn của thành viên khác**<br>- Chỉ thấy phiếu của chính mình (BR001, BR004)<br>- Hiển thị thông báo lỗi: **"Bạn không có quyền xem phiếu mượn của thành viên khác"** |
| **Ánh xạ IDM** | IDM REQ-08: Quyền xem = Thành viên |
| **REQ** | REQ-08 |
| **Kỹ thuật** | EP (Kiểm tra quyền) |
| **Kết luận** | **BUG** |
---


---

## Tổng hợp

## Tổng hợp

| Nhóm chức năng | Số TC | REQ phủ | Kỹ thuật IDM áp dụng |
|----------------|-------|---------|----------------------|
| Đăng nhập | 4 | REQ-01 | EP |
| Xem danh sách sách | 1 | REQ-02 | EP |
| Tìm kiếm và lọc sách | 7 | REQ-03 | EP, BVA |
| Mượn sách | 5 | REQ-04 | EP, BVA, Decision Table |
| Trả sách | 4 | REQ-05 | EP, BVA |
| Xử lý sách quá hạn | 2 | REQ-06 | EP |
| Quản lý thành viên | 3 | REQ-07 | EP, BVA |
| Tra cứu phiếu mượn | 2 | REQ-08 | EP, BVA |
| **Tổng** | **28** | **REQ-01 → REQ-08** | **EP, BVA, Decision Table** |

### SUGG-001: Gợi ý thêm yêu cầu trim khoảng trắng vào SRS

| Trường | Nội dung |
|--------|----------|
| **Mô tả** | Hệ thống hiện tại có thể đăng nhập thành công khi email có dấu cách ở đầu/cuối |
| **Đề xuất** | Nên bổ sung vào SRS REQ-01: "Hệ thống tự động loại bỏ khoảng trắng ở đầu và cuối email trước khi xử lý" |