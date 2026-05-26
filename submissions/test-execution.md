# Test Execution — Kết quả thực thi kiểm thử

> **Hướng dẫn**: Chạy từng TC trên hệ thống https://stqa.rbc.vn, ghi lại kết quả thực tế.
> Kết luận: **Pass** (kết quả đúng), **Fail** (kết quả sai → tạo bug report), **Blocked** (không thực hiện được vì lỗi khác chặn), **Not Run** (chưa chạy).

| Thông tin | |
|---|---|
| **Nhóm** | Nhóm 12 |
| **Ngày thực thi** | 26/5/2026 |
| **Trình duyệt** | Chrome |
| **Hệ điều hành** | Windows |

---

## Kết quả chi tiết

| TC ID | Function Group | Expected Result (summary) | Actual Result | Conclusion | Evidence | Bug |
|-------|---------------|---------------------------|----------------|---------|-----------|-----|
| TC-01 | Login | Login successful, display 3 tabs | Login successful, display full 3 tabs | ✅ **PASS** | - | - |
| TC-02 | Login | Display "Member not found" | Display "Member not found" | ✅ **PASS** | - | - |
| TC-03 | Login | Display "Incorrect password" | Display "Incorrect password" | ✅ **PASS** | - | - |
| TC-04 | Login | Display "Please enter email and password" | Display "Please enter email and password" | ✅ **PASS** | - | - |
| TC-05 | View book list | Display 20 books with full information | Display 20 books, full columns | ✅ **PASS** | - | - |
| TC-06 | Search books | Display book "Lập trình Flutter cơ bản" | Display book "Lập trình Flutter cơ bản" | ✅ **PASS** | - | - |
| TC-07 | Search books | Display "No books found" | Display "No books found" | ✅ **PASS** | - | - |
| TC-08 | Search books | Display book "Lập trình Flutter cơ bản" (case-insensitive) | Display book "Lập trình Flutter cơ bản" | ✅ **PASS** | - | - |
| TC-09 | Filter books | Display only BOOK019, BOOK020 | Display only BOOK019, BOOK020 | ✅ **PASS** | - | - |
| TC-10 | Filter books | Display only BOOK019, BOOK020 (case-insensitive) | Display only BOOK019, BOOK020 | ✅ **PASS** | - | - |
| TC-11 | Search + Filter | No books displayed, show "No books found" | Still displays Flutter book (belongs to Technology category) | ❌ **FAIL** | - | BUG-001 |
| TC-12 | Search + Filter | Display BOOK019 | Display BOOK019 | ✅ **PASS** | - | - |
| TC-13 | Borrow book | Borrow successful, book status changes to "Borrowed" | Borrow successful | ✅ **PASS** | - | - |
| TC-14 | Borrow book | Display error message "Book already borrowed" | Display error message "Book already borrowed" | ✅ **PASS** | - | - |
| TC-15 | Borrow book | Cannot borrow 4th book, show "Reached limit of 3 books" | Still can borrow 4th book | ❌ **FAIL** | - | BUG-002 |
| TC-16 | Borrow book | "Account is suspended" | "Member expired. Cannot borrow book" | ❌ **FAIL** | - | BUG-003 |
| TC-17 | Borrow book | "Library card has expired" | "Member expired. Cannot borrow book" | ❌ **FAIL** | - | BUG-003 |
| TC-18 | Return book | Return successful, no warning | Return successful, no warning | ✅ **PASS** | - | - |
| TC-19 | Return book | Return successful + show warning "Book return is overdue" | Return successful, NO warning | ❌ **FAIL** | - | BUG-004 |
| TC-20 | Return book | No return button displayed for unborrowed books | No return button displayed | ✅ **PASS** | - | - |
| TC-21 | Overdue handling | Detect and mark 2 overdue records | Detect and mark 2 overdue records | ✅ **PASS** | - | - |
| TC-22 | Overdue handling | Still detect and mark 2 overdue records | Report 0 overdue records | ❌ **FAIL** | - | BUG-005 |
| TC-23 | Add member | Add successful (valid email) | System shows error "Invalid email" and member is not created | ❌ **FAIL** | - | BUG-006 |
| TC-24 | Add member | Show error "Invalid email" | Member added successfully despite invalid email | ❌ **FAIL** | - | BUG-006 |
| TC-25 | Add member | Show error "Email already exists" | Display error "Email already exists" | ✅ **PASS** | - | - |
| TC-26 | Borrow record lookup (Librarian) | Display 5 records: BR001-BR005 | Display 5 records | ✅ **PASS** | - | - |
| TC-27 | Borrow record lookup (Member) | Display only BR001, BR004 (own records) | Member account can view borrow records of another member (MEM003) | ❌ **FAIL** | - | BUG-007 |
| TC-28 | Add member | Show error "Invalid email", cannot create (invalid email + valid phone) | Add successful | ❌ **FAIL** | - | BUG-008 |
| TC-29 | Add member | Show email error or both errors (invalid email + invalid phone) | Only show "Invalid phone number" | ❌ **FAIL** | - | BUG-009 |
| TC-30 | Add member | Show error "Invalid phone number" (valid email + invalid phone) | Show error "Invalid email" | ❌ **FAIL** | - | BUG-010 |
| TC-31 | Add member | Add successful (valid email + valid phone) | Add successful | ✅ **PASS** | - | - |
| TC-32 | Search (English) | Display book "Lập trình Flutter cơ bản" | Display book "Lập trình Flutter cơ bản" | ✅ **PASS** | - | - |
| TC-33 | Filter category (English) | Display Technology category books | No books found | ❌ **FAIL** | - | BUG-011 |
| TC-34 | Category dropdown (English) | Dropdown displays English (Technology, Education, Economics, Soft Skills, Management, Literature) | Dropdown displays Vietnamese (Công nghệ, Giáo dục, Kinh tế, Kỹ năng mềm, Quản trị, Văn học) | ❌ **FAIL** | - | BUG-012 |

## Summary of Results

| Metric | Value |
|--------|-------|
| Total Test Cases | 34 |
| Pass | 19 |
| Fail | 15 |
| Blocked | 0 |
| Not Run | 0 |
| **Pass Rate** | **55.9%** |

### Results by Function Group

| Function Group | Total TC | Pass | Fail | Pass Rate |
|----------------|----------|------|------|-----------|
| Login (REQ-01) | 4 | 4 | 0 | 100% |
| View Book List (REQ-02) | 1 | 1 | 0 | 100% |
| Search & Filter Books (REQ-03) | 10 | 6 | 4 | 60% |
| Borrow Book (REQ-04) | 5 | 2 | 3 | 40% |
| Return Book (REQ-05) | 4 | 2 | 2 | 50% |
| Overdue Handling (REQ-06) | 2 | 1 | 1 | 50% |
| Member Management (REQ-07) | 8 | 2 | 6 | 25% |
| Borrow Record Lookup (REQ-08) | 2 | 1 | 1 | 50% |
| **Total** | **34** | **19** | **15** | **55.9%** |
