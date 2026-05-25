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
| --- | --- | --- | --- | --- | --- | --- | --- |
| TC-01 | Đăng nhập thành công - Thủ thư | Chưa đăng nhập, mở hệ thống | 1. Nhập email librarian@library.com  2. Nhập mật khẩu admin123  3. Click "Đăng nhập" | Email = librarian@library.com, Password = admin123 | Chuyển sang trang chủ, hiển thị tab Sách, Mượn/Trả, Thành viên | REQ-01 | EP |
| TC-02 | Đăng nhập thất bại - Sai email | Chưa đăng nhập | 1. Nhập email sai@email.com  2. Nhập mật khẩu admin123  3. Click "Đăng nhập" | Email = sai@email.com | Hiển thị "Không tìm thấy thành viên" | REQ-01 | EP |
| TC-03 | Đăng nhập thất bại - Sai mật khẩu | Chưa đăng nhập | 1. Nhập email librarian@library.com  2. Nhập mật khẩu sai123  3. Click "Đăng nhập" | Password = sai123 | Hiển thị "Mật khẩu không đúng" | REQ-01 | EP |
| TC-04 | Đăng nhập thất bại - Để trống email và mật khẩu | Chưa đăng nhập | 1. Để trống email  2. Để trống mật khẩu  3. Click "Đăng nhập" | Email = "", Password = "" | Hiển thị "Vui lòng nhập email và mật khẩu" | REQ-01 | EP |
| TC-05 | Xem danh sách sách | Đã đăng nhập | Vào tab "Sách" | - | Hiển thị danh sách 20 sách với đầy đủ thông tin | REQ-02 | EP |
| TC-06 | Tìm kiếm sách - Có kết quả | Đã đăng nhập, đang ở tab Sách | 1. Nhập "Flutter" vào ô tìm kiếm  2. Nhấn Enter | Keyword = Flutter | Hiển thị sách "Lập trình Flutter cơ bản" | REQ-03 | EP |
| TC-07 | Tìm kiếm sách - Không có kết quả | Đã đăng nhập, đang ở tab Sách | 1. Nhập "xyzxyz123" vào ô tìm kiếm  2. Nhấn Enter | Keyword = xyzxyz123 | Hiển thị "Không tìm thấy sách nào" | REQ-03 | EP |
| TC-08 | Tìm kiếm không phân biệt chữ hoa/thường | Đã đăng nhập, đang ở tab Sách | 1. Nhập "flutter"  2. Nhấn Enter | Keyword = flutter | Kết quả giống TC-06 | REQ-03 | EP |
| TC-09 | Lọc sách theo thể loại | Đã đăng nhập, đang ở tab Sách | Chọn thể loại "Văn học" | Category = Văn học | Chỉ hiển thị BOOK019 và BOOK020 | REQ-03 | EP |
| TC-10 | Lọc thể loại không phân biệt hoa/thường | Đã đăng nhập, đang ở tab Sách | 1. Nhập "văn học" vào bộ lọc  2. Nhấn Enter | Category = văn học | Kết quả giống TC-09 | REQ-03 | EP |
| TC-11 | Kết hợp lọc và tìm kiếm - Không có kết quả | Đã đăng nhập, đang ở tab Sách | 1. Chọn thể loại "Văn học"  2. Nhập "Flutter"  3. Nhấn Enter | Category = Văn học, Keyword = Flutter | Hiển thị "Không tìm thấy sách" | REQ-03 | EP |
| TC-12 | Kết hợp lọc và tìm kiếm - Có kết quả | Đã đăng nhập, đang ở tab Sách | 1. Chọn thể loại "Văn học"  2. Nhập "Văn"  3. Nhấn Enter | Category = Văn học, Keyword = Văn | Hiển thị BOOK019 "Văn học Việt Nam đại cương" | REQ-03 | EP |
| TC-13 | Mượn sách thành công | Đăng nhập bằng tài khoản thành viên hợp lệ | 1. Tìm BOOK001  2. Click "Mượn"  3. Click "Xác nhận" | Book = BOOK001 | Hiển thị "Mượn sách thành công", trạng thái sách đổi thành "Đang mượn" | REQ-04 | EP |
| TC-14 | Mượn sách thất bại - Sách đã được mượn | Đăng nhập thành viên | 1. Tìm BOOK003  2. Click "Mượn" | Book = BOOK003 | Hiển thị "Sách đã được mượn" | REQ-04 | EP |
| TC-15 | Mượn sách thất bại - Đạt giới hạn 3 sách | Thành viên đang mượn đúng 3 sách | 1. Tìm sách còn trống  2. Click "Mượn" | Borrowed books = 3 | Hiển thị "Đã đạt giới hạn 3 sách" | REQ-04 | BVA |
| TC-16 | Mượn sách thất bại - Tài khoản bị tạm ngưng | Đăng nhập tài khoản tạm ngưng | Thực hiện mượn sách | User status = Suspended | Hiển thị "Tài khoản đang bị tạm ngưng" | REQ-04 | EP |
| TC-17 | Mượn sách thất bại - Thẻ hết hạn | Đăng nhập tài khoản hết hạn | Thực hiện mượn sách | Card status = Expired | Hiển thị "Thẻ thư viện đã hết hạn" | REQ-04 | EP |
| TC-18 | Trả sách thành công - Đúng hạn | Có phiếu mượn còn hạn | 1. Vào tab "Mượn/Trả"  2. Chọn BR003  3. Click "Trả sách" | Borrow record = BR003 | Hiển thị "Trả sách thành công" | REQ-05 | EP |
| TC-19 | Trả sách quá hạn | Có phiếu mượn đã quá hạn | 1. Vào tab "Mượn/Trả"  2. Chọn BR001  3. Click "Trả sách" | Borrow record = BR001 | Hiển thị cảnh báo "Sách trả quá hạn" | REQ-05 | BVA |
| TC-20 | Trả sách thất bại - Sách chưa được mượn | Sách đang ở trạng thái "Có sẵn" | Tìm phiếu mượn của BOOK001 | Book = BOOK001 | Không hiển thị nút "Trả sách" | REQ-05 | EP |
| TC-21 | Kiểm tra sách quá hạn lần đầu | Đăng nhập Thủ thư | Click "Kiểm tra quá hạn" | Role = Librarian | Hệ thống đánh dấu các phiếu quá hạn | REQ-06 | EP |
| TC-22 | Kiểm tra sách quá hạn lần thứ hai | Đã kiểm tra quá hạn ít nhất 1 lần | Click "Kiểm tra quá hạn" lần nữa | Role = Librarian | Hệ thống vẫn đánh dấu đúng các phiếu quá hạn | REQ-06 | EP |
| TC-23 | Thêm thành viên thành công - Email hợp lệ | Đăng nhập Thủ thư | 1. Vào tab "Thành viên"  2. Click "Thêm thành viên"  3. Nhập vana@email.com  4. Click "Lưu" | Email = vana@email.com | Thêm thành viên thành công | REQ-07 | EP |
| TC-24 | Thêm thành viên thất bại - Email không hợp lệ | Đăng nhập Thủ thư | 1. Vào tab "Thành viên"  2. Click "Thêm thành viên"  3. Nhập vana@email  4. Click "Lưu" | Email = vana@email | Hiển thị "Email không hợp lệ" | REQ-07 | BVA |
| TC-25 | Thêm thành viên thất bại - Email trùng | Đăng nhập Thủ thư | 1. Vào tab "Thành viên"  2. Click "Thêm thành viên"  3. Nhập ba.nguyen@email.com  4. Click "Lưu" | Email = ba.nguyen@email.com | Hiển thị "Email đã tồn tại" | REQ-07 | EP |
| TC-26 | Thủ thư xem tất cả phiếu mượn | Đăng nhập Thủ thư | Vào tab "Mượn/Trả" | Role = Librarian | Hiển thị toàn bộ phiếu mượn | REQ-08 | EP |
| TC-27 | Thành viên chỉ xem phiếu của mình | Đăng nhập tài khoản thành viên | Vào tab "Mượn/Trả" | Role = Member | Chỉ hiển thị phiếu mượn của chính thành viên đó | REQ-08 | EP |

## Tổng hợp

| Nhóm chức năng | Số TC | REQ phủ | Kỹ thuật IDM áp dụng |
|---|---|---|---|
| Đăng nhập | 4 | REQ-01 | EP |
| Xem danh sách sách | 1 | REQ-02 | EP |
| Tìm kiếm và lọc sách | 7 | REQ-03 | EP |
| Mượn sách | 5 | REQ-04 | EP, BVA |
| Trả sách | 3 | REQ-05 | EP, BVA |
| Xử lý sách quá hạn | 2 | REQ-06 | EP |
| Quản lý thành viên | 3 | REQ-07 | EP, BVA |
| Tra cứu phiếu mượn | 2 | REQ-08 | EP |
| **Tổng** | **27** | **REQ-01 → REQ-08** | **EP, BVA** |

### SUGG-001: Gợi ý thêm yêu cầu trim khoảng trắng vào SRS

| Trường | Nội dung |
|--------|----------|
| **Mô tả** | Hệ thống hiện tại có thể đăng nhập thành công khi email có dấu cách ở đầu/cuối |
| **Đề xuất** | Nên bổ sung vào SRS REQ-01: "Hệ thống tự động loại bỏ khoảng trắng ở đầu và cuối email trước khi xử lý" |