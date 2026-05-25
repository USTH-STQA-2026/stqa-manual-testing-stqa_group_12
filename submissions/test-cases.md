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
|---|---|---|---|---|---|---|---|
| TC-01 | Đăng nhập thành công với tài khoản Thủ thư | Người dùng chưa đăng nhập, đang ở màn hình đăng nhập | 1. Nhập email `librarian@library.com` <br> 2. Nhập mật khẩu `admin123` <br> 3. Click “Đăng nhập” | Email = librarian@library.com <br> Password = admin123 | Hệ thống đăng nhập thành công và chuyển đến trang chủ; hiển thị các tab “Sách”, “Mượn/Trả”, “Thành viên” | REQ-01 | EP |
| TC-02 | Đăng nhập thất bại với email không tồn tại | Người dùng chưa đăng nhập | 1. Nhập email `wrong@email.com` <br> 2. Nhập mật khẩu `admin123` <br> 3. Click “Đăng nhập” | Email = wrong@email.com | Hiển thị thông báo “Không tìm thấy thành viên” | REQ-01 | EP |
| TC-03 | Đăng nhập thất bại với mật khẩu sai | Người dùng chưa đăng nhập | 1. Nhập email `librarian@library.com` <br> 2. Nhập mật khẩu `wrong123` <br> 3. Click “Đăng nhập” | Password = wrong123 | Hiển thị thông báo “Mật khẩu không đúng” | REQ-01 | EP |
| TC-04 | Đăng nhập thất bại khi để trống email và mật khẩu | Người dùng chưa đăng nhập | 1. Để trống email <br> 2. Để trống mật khẩu <br> 3. Click “Đăng nhập” | Email = "" <br> Password = "" | Hiển thị thông báo “Vui lòng nhập email và mật khẩu” | REQ-01 | EP |
| TC-05 | Xem danh sách tất cả sách trong hệ thống | Người dùng đã đăng nhập thành công | Vào tab “Sách” | - | Hệ thống hiển thị đầy đủ 20 đầu sách cùng thông tin mã sách, tên sách, tác giả, thể loại và trạng thái | REQ-02 | EP |
| TC-06 | Tìm kiếm sách theo từ khóa có kết quả | Người dùng đã đăng nhập và đang ở tab “Sách” | 1. Nhập từ khóa `Flutter` vào ô tìm kiếm <br> 2. Nhấn Enter | Keyword = Flutter | Hiển thị sách BOOK001 “Lập trình Flutter cơ bản” | REQ-03 | EP |
| TC-07 | Tìm kiếm sách theo từ khóa không có kết quả | Người dùng đã đăng nhập và đang ở tab “Sách” | 1. Nhập từ khóa `xyzxyz123` vào ô tìm kiếm <br> 2. Nhấn Enter | Keyword = xyzxyz123 | Hiển thị thông báo “Không tìm thấy sách nào” | REQ-03 | EP |
| TC-08 | Tìm kiếm sách không phân biệt chữ hoa/thường | Người dùng đã đăng nhập và đang ở tab “Sách” | 1. Nhập từ khóa `flutter` vào ô tìm kiếm <br> 2. Nhấn Enter | Keyword = flutter | Hệ thống hiển thị kết quả giống TC-06 | REQ-03 | EP |
| TC-09 | Lọc sách theo thể loại Văn học | Người dùng đã đăng nhập và đang ở tab “Sách” | Chọn thể loại “Văn học” | Category = Văn học | Hệ thống chỉ hiển thị BOOK019 và BOOK020 | REQ-03 | EP |
| TC-10 | Lọc sách theo thể loại không phân biệt chữ hoa/thường | Người dùng đã đăng nhập và đang ở tab “Sách” | 1. Nhập `văn học` vào bộ lọc thể loại <br> 2. Nhấn Enter | Category = văn học | Hệ thống hiển thị kết quả giống TC-09 | REQ-03 | EP |
| TC-11 | Kết hợp tìm kiếm và lọc không có kết quả | Người dùng đã đăng nhập và đang ở tab “Sách” | 1. Chọn thể loại “Văn học” <br> 2. Nhập từ khóa `Flutter` <br> 3. Nhấn Enter | Category = Văn học <br> Keyword = Flutter | Hiển thị thông báo “Không tìm thấy sách” | REQ-03 | EP |
| TC-12 | Kết hợp tìm kiếm và lọc có kết quả | Người dùng đã đăng nhập và đang ở tab “Sách” | 1. Chọn thể loại “Văn học” <br> 2. Nhập từ khóa `đại cương` <br> 3. Nhấn Enter | Category = Văn học <br> Keyword = đại cương | Hiển thị BOOK019 “Văn học Việt Nam đại cương” | REQ-03 | EP |
| TC-13 | Mượn sách thành công với tài khoản thành viên hợp lệ | Thành viên MEM002 đã đăng nhập; BOOK001 đang ở trạng thái “Có sẵn” | 1. Tìm BOOK001 <br> 2. Click “Mượn” <br> 3. Click “Xác nhận” | Member = MEM002 <br> Book = BOOK001 | Hiển thị thông báo “Mượn sách thành công”; trạng thái BOOK001 chuyển thành “Đang mượn” | REQ-04 | DT |
| TC-14 | Mượn sách thất bại khi sách đã được mượn | Thành viên đã đăng nhập; BOOK003 đang ở trạng thái “Đang mượn” | 1. Tìm BOOK003 <br> 2. Click “Mượn” | Book = BOOK003 | Hiển thị thông báo “Sách đã được mượn” | REQ-04 | DT |
| TC-15 | Mượn sách thất bại khi đạt giới hạn tối đa 3 sách | Thành viên MEM002 đang mượn đúng 3 sách | 1. Tìm một sách còn trạng thái “Có sẵn” <br> 2. Click “Mượn” | Member = MEM002 <br> Borrowed books = 3 | Hiển thị thông báo “Đã đạt giới hạn 3 sách” | REQ-04 | BVA, DT |
| TC-16 | Mượn sách thất bại với tài khoản bị tạm ngưng | Thành viên MEM005 có trạng thái “Suspended” đã đăng nhập | 1. Tìm sách còn trạng thái “Có sẵn” <br> 2. Click “Mượn” | User status = Suspended | Hiển thị thông báo “Tài khoản đang bị tạm ngưng” | REQ-04 | DT |
| TC-17 | Mượn sách thất bại với thẻ thư viện hết hạn | Thành viên MEM006 có trạng thái “Expired” đã đăng nhập | 1. Tìm sách còn trạng thái “Có sẵn” <br> 2. Click “Mượn” | Card status = Expired | Hiển thị thông báo “Thẻ thư viện đã hết hạn” | REQ-04 | DT |
| TC-18 | Trả sách thành công khi chưa quá hạn | Phiếu mượn BR003 đang ở trạng thái chưa quá hạn | 1. Vào tab “Mượn/Trả” <br> 2. Chọn phiếu BR003 <br> 3. Click “Trả sách” | Borrow Record = BR003 | Hiển thị thông báo “Trả sách thành công”; trạng thái phiếu mượn chuyển thành “Returned” | REQ-05 | EP |
| TC-19 | Trả sách khi phiếu mượn đã quá hạn | Phiếu mượn BR001 đang ở trạng thái quá hạn | 1. Vào tab “Mượn/Trả” <br> 2. Chọn phiếu BR001 <br> 3. Click “Trả sách” | Borrow Record = BR001 | Hiển thị cảnh báo “Sách trả quá hạn” và hoàn tất thao tác trả sách | REQ-05 | BVA |
| TC-20 | Không hiển thị chức năng trả sách với sách chưa được mượn | BOOK005 đang ở trạng thái “Có sẵn” | 1. Tìm thông tin mượn của BOOK005 <br> 2. Kiểm tra thao tác trả sách | Book = BOOK005 | Hệ thống không hiển thị nút “Trả sách” | REQ-05 | EP |
| TC-21 | Kiểm tra và đánh dấu các phiếu mượn quá hạn lần đầu | Người dùng đăng nhập bằng tài khoản Thủ thư | Click “Kiểm tra quá hạn” | Role = Librarian | Hệ thống đánh dấu chính xác các phiếu quá hạn như BR001 và BR004 với trạng thái “Overdue” | REQ-06 | EP |
| TC-22 | Kiểm tra quá hạn nhiều lần không tạo dữ liệu sai | Các phiếu quá hạn đã được đánh dấu trước đó | Click “Kiểm tra quá hạn” lần thứ hai | Role = Librarian | Hệ thống vẫn giữ đúng trạng thái quá hạn, không tạo duplicate hoặc thay đổi dữ liệu sai | REQ-06 | EP |
| TC-23 | Thêm thành viên mới thành công với email hợp lệ | Người dùng đăng nhập bằng tài khoản Thủ thư | 1. Vào tab “Thành viên” <br> 2. Click “Thêm thành viên” <br> 3. Nhập `newmember@email.com` <br> 4. Click “Lưu” | Email = newmember@email.com | Hệ thống thêm thành viên mới thành công | REQ-07 | EP |
| TC-24 | Thêm thành viên thất bại với email không hợp lệ | Người dùng đăng nhập bằng tài khoản Thủ thư | 1. Vào tab “Thành viên” <br> 2. Click “Thêm thành viên” <br> 3. Nhập `newmember@email` <br> 4. Click “Lưu” | Email = newmember@email | Hiển thị thông báo “Email không hợp lệ” | REQ-07 | BVA |
| TC-25 | Thêm thành viên thất bại với email đã tồn tại | Người dùng đăng nhập bằng tài khoản Thủ thư | 1. Vào tab “Thành viên” <br> 2. Click “Thêm thành viên” <br> 3. Nhập email đã tồn tại trong hệ thống <br> 4. Click “Lưu” | Email = ba.nguyen@email.com | Hiển thị thông báo “Email đã tồn tại” | REQ-07 | EP |
| TC-26 | Thủ thư xem toàn bộ phiếu mượn trong hệ thống | Người dùng đăng nhập bằng tài khoản Thủ thư | Vào tab “Mượn/Trả” | Role = Librarian | Hệ thống hiển thị toàn bộ phiếu mượn của tất cả thành viên | REQ-08 | EP |
| TC-27 | Thành viên chỉ được xem phiếu mượn của chính mình | Người dùng đăng nhập bằng tài khoản Thành viên | Vào tab “Mượn/Trả” | Role = Member | Hệ thống chỉ hiển thị phiếu mượn thuộc tài khoản đang đăng nhập | REQ-08 | EP |

## Decision Table – Borrow Book Function (REQ-04)

| Conditions / Rules | R1 | R2 | R3 | R4 | R5 |
|---|---|---|---|---|---|
| C1. Account active | Y | N | Y | Y | Y |
| C2. Card not expired | Y | Y | N | Y | Y |
| C3. Borrowed books < 3 | Y | Y | Y | N | Y |
| C4. Book available | Y | Y | Y | Y | N |
|  |  |  |  |  |  |
| Actions |  |  |  |  |  |
| A1. Allow borrowing | Y | N | N | N | N |
| A2. Reject borrowing | N | Y | Y | Y | Y |

## Tổng hợp

| Nhóm chức năng | Số TC | REQ phủ | Kỹ thuật IDM áp dụng |
|---|---|---|---|
| Đăng nhập | 4 | REQ-01 | EP |
| Xem danh sách sách | 1 | REQ-02 | EP |
| Tìm kiếm và lọc sách | 7 | REQ-03 | EP |
| Mượn sách | 5 | REQ-04 | DT, BVA |
| Trả sách | 3 | REQ-05 | EP, BVA |
| Xử lý sách quá hạn | 2 | REQ-06 | EP |
| Quản lý thành viên | 3 | REQ-07 | EP, BVA |
| Tra cứu phiếu mượn | 2 | REQ-08 | EP |
| **Tổng** | **27** | **REQ-01 → REQ-08** | **EP, BVA, DT** |

### SUGG-001: Gợi ý thêm yêu cầu trim khoảng trắng vào SRS

| Trường | Nội dung |
|--------|----------|
| **Mô tả** | Hệ thống hiện tại có thể đăng nhập thành công khi email có dấu cách ở đầu/cuối |
| **Đề xuất** | Nên bổ sung vào SRS REQ-01: "Hệ thống tự động loại bỏ khoảng trắng ở đầu và cuối email trước khi xử lý" |