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

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|----------------|----------------|-----------------|------------------|-----|----------|
| TC-01 | Đăng nhập thành công - Thủ thư | Chưa đăng nhập, mở https://stqa.rbc.vn/ | 1. Nhập email: `librarian@library.com`<br>2. Nhập mật khẩu: `admin123`<br>3. Click "Đăng nhập" | Email = `librarian@library.com`, Mật khẩu = `admin123` | Chuyển sang trang chủ, AppBar hiển thị "Nguyễn Thủ Thư (Thủ thư)", xuất hiện 3 tab: Sách, Mượn/Trả, Thành viên | REQ-01 | EP |
| TC-02 | Đăng nhập thất bại - Sai email | Chưa đăng nhập | 1. Nhập email: `sai@email.com`<br>2. Nhập mật khẩu: `admin123`<br>3. Click "Đăng nhập" | Email = `sai@email.com`, Mật khẩu = `admin123` | Ở lại màn hình đăng nhập, hiển thị "Không tìm thấy thành viên" | REQ-01 | EP |
| TC-03 | Đăng nhập thất bại - Sai mật khẩu | Chưa đăng nhập | 1. Nhập email: `librarian@library.com`<br>2. Nhập mật khẩu: `sai123`<br>3. Click "Đăng nhập" | Email = `librarian@library.com`, Mật khẩu = `sai123` | Ở lại màn hình đăng nhập, hiển thị "Mật khẩu không đúng" | REQ-01 | EP |
| TC-04 | Đăng nhập thất bại - Bỏ trống email và mật khẩu | Chưa đăng nhập | 1. Để trống email<br>2. Để trống mật khẩu<br>3. Click "Đăng nhập" | Email = `""`, Mật khẩu = `""` | Hiển thị "Vui lòng nhập email và mật khẩu" | REQ-01 | EP |
| TC-05 | Xem danh sách sách | Đã đăng nhập | Vào tab "Sách" | - | Hiển thị 20 sách (BOOK001-BOOK020) với các cột: Tên sách, Tác giả, Thể loại, Năm XB, Trạng thái | REQ-02 | EP |
| TC-06 | Tìm kiếm theo tên - Có kết quả | Đã đăng nhập, đang ở tab Sách | 1. Gõ "Flutter" vào ô tìm kiếm<br>2. Nhấn Enter | Từ khóa = "Flutter" | Hiển thị 1 dòng: "Lập trình Flutter cơ bản" | REQ-03 | EP |
| TC-07 | Tìm kiếm - Không có kết quả | Đã đăng nhập, đang ở tab Sách | 1. Gõ "xyzxyz123" vào ô tìm kiếm<br>2. Nhấn Enter | Từ khóa = "xyzxyz123" | Hiển thị "Không tìm thấy sách nào" | REQ-03 | EP |
| TC-08 | Tìm kiếm không phân biệt hoa/thường (BVA) | Đã đăng nhập, đang ở tab Sách | 1. Gõ "flutter" (chữ thường) vào ô tìm kiếm<br>2. Nhấn Enter | Từ khóa = "flutter" | Kết quả giống hệt TC-06 (vẫn ra sách Flutter) | REQ-03 | BVA |
| TC-09 | Lọc theo thể loại | Đã đăng nhập, đang ở tab Sách | Chọn "Văn học" từ dropdown lọc | Thể loại = "Văn học" | Chỉ hiển thị BOOK019, BOOK020 | REQ-03 | EP |
| TC-10 | Lọc theo thể loại - Chữ thường (BVA) | Đã đăng nhập, đang ở tab Sách | 1. Gõ "văn học" (chữ thường) vào ô lọc<br>2. Nhấn Enter | Thể loại = "văn học" | Kết quả giống hệt TC-09 (chỉ hiển thị BOOK019, BOOK020) | REQ-03 | BVA |
| TC-11 | Kết hợp lọc và tìm kiếm - Lọc Văn học + Tìm "Flutter" | Đã đăng nhập, đang ở tab Sách | 1. Chọn thể loại "Văn học"<br>2. Gõ "Flutter" vào ô tìm kiếm<br>3. Nhấn Enter | Thể loại = "Văn học", Từ khóa = "Flutter" | Không hiển thị sách nào, báo "Không tìm thấy sách" | REQ-03 | EP |
| TC-12 | Kết hợp lọc và tìm kiếm - Lọc Văn học + Tìm "Văn" | Đã đăng nhập, đang ở tab Sách | 1. Chọn thể loại "Văn học"<br>2. Gõ "Văn" vào ô tìm kiếm<br>3. Nhấn Enter | Thể loại = "Văn học", Từ khóa = "Văn" | Hiển thị BOOK019: "Văn học Việt Nam đại cương" | REQ-03 | EP |
| TC-13 | Mượn sách thành công | Đăng nhập `ba.nguyen@email.com`, BOOK001 "Có sẵn", đang mượn 1 sách | 1. Vào tab Sách<br>2. Tìm BOOK001<br>3. Click "Mượn"<br>4. Click "Xác nhận" | Sách = BOOK001, Tài khoản = `ba.nguyen@email.com` | "Mượn sách thành công", BOOK001 thành "Đang mượn", số sách mượn tăng từ 1 lên 2 | REQ-04 | EP |
| TC-14 | Mượn sách thất bại - Sách đã mượn | Đăng nhập `ba.nguyen@email.com`, BOOK003 đang "Đang mượn" | Tìm BOOK003, click "Mượn" | Sách = BOOK003 | "Sách đã được mượn", trạng thái sách vẫn là "Đang mượn" | REQ-04 | EP |
| TC-15 | Mượn sách thất bại - Giới hạn 3 sách (BVA) | Đăng nhập `ba.nguyen@email.com`, đang mượn đúng 3 sách | 1. Tìm sách thứ 4 "Có sẵn"<br>2. Click "Mượn"<br>3. Click "Xác nhận" | Số sách đang mượn = 3 | Không thể mượn, báo "Đã đạt giới hạn 3 sách" | REQ-04 | BVA |
| TC-16 | Mượn sách thất bại - Tài khoản tạm ngưng | Đăng nhập `cu.le@email.com` (Tạm ngưng) | Mượn bất kỳ sách "Có sẵn" | Tài khoản = `cu.le@email.com` | "Tài khoản đang bị tạm ngưng" | REQ-04 | EP |
| TC-17 | Mượn sách thất bại - Thẻ hết hạn | Đăng nhập `binh.pham@email.com` (Hết hạn) | Mượn bất kỳ sách "Có sẵn" | Tài khoản = `binh.pham@email.com` | "Thẻ thư viện đã hết hạn" | REQ-04 | EP |
| TC-18 | Trả sách thành công (đúng hạn) | Đăng nhập Thủ thư, có BR003 (hạn 15/10/2024), ngày hiện tại < 15/10/2024 | 1. Vào tab "Mượn/Trả"<br>2. Tìm BR003<br>3. Click "Trả sách" | Phiếu = BR003 | "Trả sách thành công", sách thành "Có sẵn", không có cảnh báo quá hạn | REQ-05 | EP |
| TC-19 | Trả sách quá hạn - Kiểm tra cảnh báo | Đăng nhập Thủ thư, có BR001 (hạn 15/09/2024) | 1. Vào tab "Mượn/Trả"<br>2. Tìm BR001<br>3. Click "Trả sách" | Phiếu = BR001 | Trả thành công + hiển thị cảnh báo "Sách trả quá hạn" | REQ-05 | BVA |
| TC-20 | Trả sách thất bại - Sách chưa được mượn | Đăng nhập Thủ thư, BOOK001 đang "Có sẵn" | Vào tab "Mượn/Trả", tìm phiếu mượn của BOOK001 | Sách = BOOK001 | Không có phiếu mượn, không có nút "Trả sách" | REQ-05 | EP |
| TC-21 | Trả sách quá hạn (BVA) | Có phiếu mượn quá hạn (BR001) | Trả BR001 | Ngày trả > hạn trả | Trả thành công + hiển thị "Sách trả quá hạn" | REQ-05 | BVA |
| TC-22 | Kiểm tra quá hạn - Lần đầu | Đăng nhập Thủ thư, có BR001 quá hạn | 1. Vào tab "Mượn/Trả"<br>2. Click "Kiểm tra quá hạn" lần đầu | Quyền = Thủ thư | Phát hiện và đánh dấu 2 phiếu quá hạn | REQ-06 | EP |
| TC-23 | Kiểm tra quá hạn - Lần thứ hai | Đã nhấn "Kiểm tra quá hạn" ít nhất 1 lần | Click "Kiểm tra quá hạn" lần thứ hai | Quyền = Thủ thư | Vẫn phải phát hiện và đánh dấu 2 phiếu quá hạn | REQ-06 | EP |
| TC-24 | Thêm thành viên thất bại - Email hợp lệ bị từ chối | Đăng nhập Thủ thư | 1. Vào tab "Thành viên"<br>2. Click "Thêm thành viên"<br>3. Nhập Email: `vana@email.com`<br>4. Click "Lưu" | Email = `vana@email.com` | Thêm thành công (vì email hợp lệ) | REQ-07 | EP |
| TC-25 | Thêm thành viên - Email không hợp lệ vẫn tạo được | Đăng nhập Thủ thư | 1. Vào tab "Thành viên"<br>2. Click "Thêm thành viên"<br>3. Nhập Email: `vana@email`<br>4. Click "Lưu" | Email = `vana@email` | Báo lỗi "Email không hợp lệ", không tạo được | REQ-07 | BVA |
| TC-26 | Thêm thành viên thất bại - Email trùng | Đăng nhập Thủ thư | 1. Vào tab "Thành viên"<br>2. Click "Thêm thành viên"<br>3. Nhập Email: `ba.nguyen@email.com`<br>4. Click "Lưu" | Email = `ba.nguyen@email.com` | "Email đã tồn tại" | REQ-07 | EP |
| TC-27 | Thủ thư xem tất cả phiếu | Đăng nhập Thủ thư | Vào tab "Mượn/Trả" | Quyền = Thủ thư | Hiển thị 5 phiếu: BR001, BR002, BR003, BR004, BR005 | REQ-08 | EP |
| TC-28 | Thành viên chỉ xem phiếu của mình | Đăng nhập `ba.nguyen@email.com` | Vào tab "Mượn/Trả" | Quyền = Thành viên (MEM002) | Chỉ hiển thị BR001, BR004 (phiếu của MEM002) | REQ-08 | EP |

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