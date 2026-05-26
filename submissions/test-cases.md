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

### IDM — Login (REQ-01)

| Characteristic | Partition | Value | Expected Result |
|----------------|-----------|-------|-----------------|
| Email exists in the system? | Existing email (EP)| `librarian@library.com` | Login succeeds and the systems redirects the user to the homepage  |
| | Non-existing email(EP) | `nobody@test.com` | System displays "Member not found" |
| Password correctness | Correct password(EP) | `admin123` | Login succeds |
| | Incorrect password(EP) | `wrongpassword` | System displays "Incorrect password|
| Input fields | Both email and password provided (EP) | `librarian@library.com` / `admin123` | System processes login normally |
| | Email and password empty (EP) | `""` / `""` | System displays "Please enter email and password" |
| User role after successful login | Librarian account (EP) | `librarian@library.com` | AppBar displays user name and role: Librarian |
| | Member account (EP) | `ba.nguyen@email.com` | AppBar displays user name and role: Member |

---

### IDM — Search Books (REQ-03)

| Characteristic | Partition | Value | Expected Result |
|----------------|-----------|-------|-----------------|
| Does keyword exist in DB? | Yes (book title) | `"Flutter"` | Display books containing "Flutter" |
| | Yes (author name) | `"Nguyen"` | Display books by author Nguyen |
| | No | `"XYZ123"` | Empty list |
| Case-sensitive? | Lowercase | `"flutter"` | Same result as "Flutter" |
| | Uppercase | `"FLUTTER"` | Same result as "Flutter" |

---

### IDM — Borrow Book (REQ-04)

| Characteristic | Partition | Value | Expected Result |
|----------------|-----------|-------|-----------------|
| Book status | Available | BOOK001 | Borrow successful |
| | Borrowed | BOOK003 | "Book already borrowed" |
| | Lost | BOOK007 | "Book not available (lost)" |
| Number of books borrowed | 0, 1, 2 | Borrow more | Borrow successful |
| | 3 (upper bound) | Borrow 4th book | "Reached limit of 3 books" |
| Account status | Active | `ba.nguyen@email.com` | Can borrow |
| | Suspended | `cu.le@email.com` | "Account is suspended" |
| | Expired | `binh.pham@email.com` | "Library card has expired" |

---

### IDM — Return Book (REQ-05)

| Characteristic | Partition | Value | Expected Result |
|----------------|-----------|-------|-----------------|
| Is book currently borrowed? | Yes (borrowed) | BR001 | Return successful, show "Return successful", book status changes from "Borrowed" → "Available", borrow record shows "Return date" = current date |
| | No (not borrowed) | BOOK001 (after reset) | No borrow record found, display error message: "No borrow record found" |
| Return date vs due date | On time | BR003 | Return successful, book becomes "Available", NO overdue warning |
| | Overdue (BVA - lower bound) | Due date = yesterday | Return successful, book becomes "Available", AND display warning: "Book return is overdue" |
| | Overdue (EP) | BR001 | Return successful, book becomes "Available", AND display warning: "Book return is overdue" |

---

### IDM — Overdue Handling (REQ-06)

| Characteristic | Partition | Value | Expected Result |
|----------------|-----------|-------|-----------------|
| User role | Librarian (EP) | `librarian@library.com` | Has "Check overdue" button |
| | Member (EP) | `ba.nguyen@email.com` | Does not see "Check overdue" button |
| Due date vs current date | Not overdue (EP) | BR003 | Not marked as overdue |
| | Just overdue (BVA - boundary) | Due date = yesterday | Marked as "Overdue" |
| | Overdue (EP) | BR001 | Marked as "Overdue" |

---

### IDM — Member Management (REQ-07) - Librarian only

| Characteristic | Partition | Value | Expected Result |
|----------------|-----------|-------|-----------------|
| User role | Librarian (EP) | `librarian@library.com` | Sees "Members" tab |
| | Member (EP) | `ba.nguyen@email.com` | Does not see "Members" tab |
| Email validation | Valid (EP) | `user@domain.com` | Creation successful |
| | Missing dot (BVA) | `user@domain` | Error message "Invalid email" |
| | Missing @ (EP) | `userdomain.com` | Error message "Invalid email" |
| | Empty (EP) | `""` | Error message "Please enter email" |
| Email duplicate? | Not exists (EP) | `user@domain.com` | Creation successful |
| | Already exists (EP) | `ba.nguyen@email.com` | Error message "Email already exists" |

---

### IDM — Borrow Record Lookup (REQ-08)

| Characteristic | Partition | Value | Expected Result |
|----------------|-----------|-------|-----------------|
| User role | Librarian (EP) | `librarian@library.com` | Can view all borrow records (5 records) |
| | Member (EP) | `ba.nguyen@email.com` | Can only view own records (BR001, BR004) |
| Member ID search (Librarian) | ID exists (EP) | `MEM002` | Display MEM002's records |
| | ID does not exist (EP) | `ABC002` | Error message "Member not found" |
| | Empty ID (BVA) | `""` | Error message "Please enter member ID (e.g., MEM001)" |

## Test Design Technique Explanation

### 1. Why choose EP (Equivalence Partitioning)?

**Reason:** Inputs in the same partition produce equivalent behavior → Only need ONE representative per partition. The system has many inputs that can be divided into equivalent partitions, each with similar behavior.

**Applied to REQs:**
- **REQ-01 (Login):** Email exists / does not exist, correct/incorrect password
- **REQ-04 (Borrow Book):** Book status (Available/Borrowed/Lost), account status (Active/Suspended/Expired)
- **REQ-07 (Member Management):** Valid/invalid email, duplicate/non-duplicate email

**Effectiveness:** EP reduces test count dramatically – without losing coverage!

---

### 2. Why choose BVA (Boundary Value Analysis)?

**Reason:** Most bugs cluster at the edges of partitions (off-by-one errors, wrong operators (> vs ≥), missing edge cases). The system has constraints on quantity and dates, where errors often occur at boundary values.

**Applied to REQs:**
- **REQ-04 (Borrow Book):** Maximum limit of 3 books/member. Test at boundaries: when borrowing 2 books (borrow more successful) and when borrowing 3 books (borrow more fails)
- **REQ-05 (Return Book):** Return date vs due date. Test at boundaries: return on time vs 1 day overdue
- **REQ-03 (Search):** Case sensitivity. Test at boundaries: "Flutter" vs "flutter"

**Effectiveness:** Detects errors at boundaries between partitions.

---

### 3. Why choose Decision Table?

**Reason:** REQ-04 (Borrow Book) has multiple conditions that combine together: book status, number of books borrowed, account status. These conditions depend on each other, creating many different scenarios.

**Applied to REQs:**
- **REQ-04 (Borrow Book):** Combines 3 conditions (Book available?, Borrowed books < 3?, Account active?) to produce different actions.

**Effectiveness:** Ensures no combination of conditions is missed, especially edge cases.



> 💡 **Gợi ý kỹ thuật**: Sử dụng **Phân lớp tương đương (EP)** cho các phân vùng rời rạc, **Phân tích giá trị biên (BVA)** cho các phân vùng số (ví dụ: giới hạn 3 sách). Xem textbook §6.1–6.3.

---

## Bước 2: Test Cases

| TC ID | Test Objective | Precondition | Steps | Input Data | Expected Result | REQ | Technique |
|-------|----------------|--------------|-------|-------------|-----------------|-----|-----------|
| TC-01 | Successful login with Librarian account | User not logged in, on login page | 1. Enter email `librarian@library.com` <br> 2. Enter password `admin123` <br> 3. Click "Login" | Email = librarian@library.com <br> Password = admin123 | Login successful, redirect to home page; display tabs "Books", "Borrow/Return", "Members" | REQ-01 | EP (Happy path) |
| TC-02 | Login fails with non-existent email | User not logged in | 1. Enter email `wrong@email.com` <br> 2. Enter password `admin123` <br> 3. Click "Login" | Email = wrong@email.com | Display error message "Member not found" | REQ-01 | EP (Negative) |
| TC-03 | Login fails with wrong password | User not logged in | 1. Enter email `librarian@library.com` <br> 2. Enter password `wrong123` <br> 3. Click "Login" | Password = wrong123 | Display error message "Incorrect password" | REQ-01 | EP (Negative) |
| TC-04 | Login fails with empty email and password | User not logged in | 1. Leave email empty <br> 2. Leave password empty <br> 3. Click "Login" | Email = "" <br> Password = "" | Display error message "Please enter email and password" | REQ-01 | EP (Negative) |
| TC-05 | View all books in the system | User logged in successfully | Go to "Books" tab | - | Display all 20 books with information: Book ID, Title, Author, Category, Status | REQ-02 | EP (Happy path) |
| TC-06 | Search books by keyword with results | User logged in, on Books tab | 1. Enter keyword `Flutter` in search box <br> 2. Press Enter | Keyword = Flutter | Display book BOOK001 "Lập trình Flutter cơ bản" | REQ-03 | EP (Happy path) |
| TC-07 | Search books by keyword with no results | User logged in, on Books tab | 1. Enter keyword `xyzxyz123` in search box <br> 2. Press Enter | Keyword = xyzxyz123 | Display message "No books found" | REQ-03 | EP (Negative) |
| TC-08 | Search books is case-insensitive | User logged in, on Books tab | 1. Enter keyword `flutter` (lowercase) in search box <br> 2. Press Enter | Keyword = flutter | System displays same results as TC-06 | REQ-03 | EP (Happy path) |
| TC-09 | Filter books by Literature category | User logged in, on Books tab | Select "Literature" from category dropdown | Category = Literature | System displays only BOOK019 and BOOK020 | REQ-03 | EP (Happy path) |
| TC-10 | Filter books by category is case-insensitive | User logged in, on Books tab | 1. Enter `văn học` (lowercase) in category filter <br> 2. Press Enter | Category = văn học | System displays same results as TC-09 | REQ-03 | EP (Happy path) |
| TC-11 | Combine search and filter with no results | User logged in, on Books tab | 1. Select category "Văn học" <br> 2. Enter keyword `Flutter` <br> 3. Press Enter | Category = Văn học <br> Keyword = Flutter | Display message "No books found" | REQ-03 | EP (Negative) |
| TC-12 | Combine search and filter with results | User logged in, on Books tab | 1. Select category "Văn học" <br> 2. Enter keyword `đại cương` <br> 3. Press Enter | Category = Văn học <br> Keyword = đại cương | Display BOOK019 "Văn học Việt Nam đại cương" | REQ-03 | EP (Happy path) |
| TC-13 | Borrow book successfully with valid member account | Member MEM002 logged in; BOOK001 is "Available" | 1. Find BOOK001 <br> 2. Click "Borrow" <br> 3. Click "Confirm" | Member = MEM002 <br> Book = BOOK001 | Display "Borrow successful"; BOOK001 status changes to "Borrowed" | REQ-04 | DT (Happy path) |
| TC-14 | Borrow fails because book is already borrowed | Member logged in; BOOK003 is "Borrowed" | 1. Find BOOK003 <br> 2. Click "Borrow" | Book = BOOK003 | Display error message "Book already borrowed" | REQ-04 | DT (Negative) |
| TC-15 | Borrow fails when reaching limit of 3 books | Member MEM002 currently has exactly 3 borrowed books | 1. Find an "Available" book <br> 2. Click "Borrow" | Member = MEM002 <br> Borrowed books = 3 | Display error message "Reached limit of 3 books" | REQ-04 | BVA, DT |
| TC-16 | Borrow fails with suspended account | Member MEM005 has "Suspended" status, logged in | 1. Find an "Available" book <br> 2. Click "Borrow" | User status = Suspended | Display error message "Account is suspended" | REQ-04 | DT (Negative) |
| TC-17 | Borrow fails with expired library card | Member MEM006 has "Expired" status, logged in | 1. Find an "Available" book <br> 2. Click "Borrow" | Card status = Expired | Display error message "Library card has expired" | REQ-04 | DT (Negative) |
| TC-18 | Return book successfully when not overdue | Borrow record BR003 is not overdue | 1. Go to "Borrow/Return" tab <br> 2. Select record BR003 <br> 3. Click "Return" | Borrow Record = BR003 | Display "Return successful"; record status changes to "Returned" | REQ-05 | EP (Happy path) |
| TC-19 | Return book when overdue | Borrow record BR001 is overdue | 1. Go to "Borrow/Return" tab <br> 2. Select record BR001 <br> 3. Click "Return" | Borrow Record = BR001 | Display warning "Book return is overdue" and complete return | REQ-05 | BVA |
| TC-20 | Return function not displayed for unborrowed books | BOOK005 is "Available" | 1. Look for borrow information of BOOK005 <br> 2. Check return action | Book = BOOK005 | System does not display "Return" button | REQ-05 | EP (Negative) |
| TC-21 | Check and mark overdue borrow records first time | User logged in as Librarian | Click "Check overdue" | Role = Librarian | System correctly marks overdue records like BR001 and BR004 as "Overdue" | REQ-06 | EP (Happy path) |
| TC-22 | Multiple overdue checks do not create wrong data | Overdue records already marked | Click "Check overdue" a second time | Role = Librarian | System keeps correct overdue status, no duplicate or wrong data changes | REQ-06 | EP (Negative) |
| TC-23 | Add new member successfully with valid email | User logged in as Librarian | 1. Go to "Members" tab <br> 2. Click "Add member" <br> 3. Enter `newmember@email.com` <br> 4. Click "Add member" | Email = newmember@email.com | System adds new member successfully | REQ-07 | EP (Happy path) |
| TC-24 | Add member fails with invalid email | User logged in as Librarian | 1. Go to "Members" tab <br> 2. Click "Add member" <br> 3. Enter `newmember@email` <br> 4. Click "Add member" | Email = newmember@email | Display error message "Invalid email" | REQ-07 | BVA |
| TC-25 | Add member fails with duplicate email | User logged in as Librarian | 1. Go to "Members" tab <br> 2. Click "Add member" <br> 3. Enter existing email <br> 4. Click "Add member" | Email = ba.nguyen@email.com | Display error message "Email already exists" | REQ-07 | EP (Negative) |
| TC-26 | Librarian views all borrow records in system | User logged in as Librarian | Go to "Borrow/Return" tab | Role = Librarian | System displays all borrow records of all members | REQ-08 | EP (Happy path) |
| TC-27 | Member can only view their own borrow records | User logged in as Member | Go to "Borrow/Return" tab | Role = Member | System only displays borrow records of the logged in account | REQ-08 | EP (Negative) |
| TC-28 | Add member - Invalid email + Valid phone still creates member (BUG-008) | Librarian logged in `librarian@library.com` | 1. Go to "Members" tab<br>2. Click "Add member"<br>3. Enter Name: `Nguyen Van Test`<br>4. Enter Email: `test@email` (missing .com)<br>5. Enter Phone: `00246135792` (valid)<br>6. Click "Add member" | Email = `test@email` (invalid)<br>Phone = `0246135792` (valid) | Error message: "Invalid email", member not created | REQ-07 | BVA |
| TC-29 | Add member - Invalid email + Invalid phone only shows phone error (BUG-009) | Librarian logged in `librarian@library.com` | 1. Go to "Members" tab<br>2. Click "Add member"<br>3. Enter Name: `Nguyen Van Test`<br>4. Enter Email: `test@email` (missing .com)<br>5. Enter Phone: `abc` (invalid)<br>6. Click "Add member" | Email = `test@email` (invalid)<br>Phone = `abc` (invalid) | Show both errors or prioritize email error (e.g., "Invalid email") | REQ-07 | EP |
| TC-30 | Add member - Valid email + Invalid phone shows wrong error | Librarian logged in `librarian@library.com` | 1. Go to "Members" tab<br>2. Click "Add member"<br>3. Enter Name: `Nguyen Van Test`<br>4. Enter Email: `test@email.com` (valid)<br>5. Enter Phone: `abc` (invalid)<br>6. Click "Add member" | Email = `test@email.com` (valid)<br>Phone = `abc` (invalid) | Error message: "Invalid phone number" | REQ-07 | BVA |
| TC-31 | Add member - Valid email + Valid phone | Librarian logged in `librarian@library.com` | 1. Go to "Members" tab<br>2. Click "Add member"<br>3. Enter Name: `Nguyen Van A`<br>4. Enter Email: `vana@email.com` (valid)<br>5. Enter Phone: `0246135792` (valid)<br>6. Click "Add member" | Email = `vana@email.com` (valid)<br>Phone = `0246135792` (valid) | Member created successfully | REQ-07 | EP |
| TC-32 | Search books using English keyword | User logged in, on Books tab | 1. Enter keyword `Flutter` in search box <br> 2. Press Enter | Keyword = Flutter | Display book BOOK001 "Lập trình Flutter cơ bản" | REQ-03 | EP (Happy path) |
| TC-33 | Filter books by English category name | User logged in, on Books tab | 1. Open category dropdown <br> 2. Select `Technology` | Category = Technology | System displays books in Technology category | REQ-03 | EP (Happy path) |
| TC-34 | Verify category dropdown language display | User logged in, on Books tab | 1. Open category dropdown | - | Dropdown displays categories in English: Technology, Education, Economics, Soft Skills, Management, Literature | REQ-03 | EP (UI Validation) |

#### Decision Table:

| | Rule 1 | Rule 2 | Rule 3 | Rule 4 | Rule 5 | Rule 6 |
|---|---|---|---|---|---|---|
| **Conditions** | | | | | | |
| Book "Available"? | Y | N | Y | Y | Y | Y |
| Number of borrowed books < 3? | Y | Y | N | Y | Y | Y |
| Account Active? | Y | Y | Y | N | N | N |
| Account Suspended? | N | N | N | Y | N | N |
| Account Expired? | N | N | N | N | Y | N |
| **Actions** | | | | | | |
| Borrow successful | X | | | | | |
| "Book already borrowed" | | X | | | | |
| "Reached limit of 3 books" | | | X | | | |
| "Account is suspended" | | | | X | | |
| "Library card has expired" | | | | | X | |

## Summary

### Test case count by REQ

| Function Group | TC Count | REQ Covered | IDM Techniques Applied |
|----------------|----------|-------------|------------------------|
| Login | 4 | REQ-01 | EP |
| View Book List | 1 | REQ-02 | EP |
| Search & Filter Books | 10 | REQ-03 | EP |
| Borrow Book | 5 | REQ-04 | DT, BVA |
| Return Book | 3 | REQ-05 | EP, BVA |
| Overdue Handling | 2 | REQ-06 | EP |
| Member Management | 7 | REQ-07 | EP, BVA |
| Borrow Record Lookup | 2 | REQ-08 | EP |
| **Total** | **34** | **REQ-01 → REQ-08** | **EP, BVA, DT** |

### SUGG-001: Gợi ý thêm yêu cầu trim khoảng trắng vào SRS

| Trường | Nội dung |
|--------|----------|
| **Mô tả** | Hệ thống hiện tại có thể đăng nhập thành công khi email có dấu cách ở đầu/cuối |
| **Đề xuất** | Nên bổ sung vào SRS REQ-01: "Hệ thống tự động loại bỏ khoảng trắng ở đầu và cuối email trước khi xử lý" |