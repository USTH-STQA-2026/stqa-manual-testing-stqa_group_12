# Bug Reports — Báo cáo lỗi

> **Hướng dẫn**: Tạo 1 mục bug cho mỗi TC có kết quả **Fail**.
> Xem [examples/sample-bug-report.md](../examples/sample-bug-report.md) để hiểu cách viết bug report tốt.
> Mỗi bug cần: tiêu đề mô tả hành vi lỗi, bước tái hiện, expected vs actual, severity + giải thích.

| Thông tin | |
|---|---|
| **Nhóm** | Group 12 |
| **Ngày báo cáo** | 27/05/2026 |

---

# 5. Defect Reports

## Common Information

| Field | Value |
|---------|---------|
| Project | ABC Library Borrowing Management System |
| Version | v1.0 |
| Environment | Chrome Browser on Windows |
| Test Period | June 2026 |
| Status | Open |

---

# 5.1 Search & Filter Books (REQ-03)

## BUG-001: Search and Filter Do Not Combine

| Field | Value |
|-------|-------|
| **Bug ID** | BUG-001 |
| **Related TC** | TC-11 |
| **Related REQ** | REQ-03 |
| **Severity** | High |
| **Detected by** | Nguyen An Khanh |
| **Detection Date** | 25/05/2026 |
| **Status** | Open |

### Title
Search and category filter do not work together.

### Precondition
Logged in and currently on Books page.

### Steps to Reproduce
1. Select category "Literature".
2. Enter keyword "Flutter".
3. Press Enter.

### Expected Result (SRS REQ-03)
- Search and filter must work together.
- System displays "No books found".

### Actual Result
- BOOK001 "Lập trình Flutter cơ bản" is displayed.
- Category filter is ignored.

### Impact
Users cannot accurately search within a selected category.

### Evidence

![BUG-001-01](images/BUG-001-01.png)

*Insert screenshot showing Literature filter and Flutter search returning Technology book.*

### Recommendation
Apply search and category conditions using AND logic.

---

## BUG-002: English Category Name Filtering Does Not Work

| Field | Value |
|-------|-------|
| **Bug ID** | BUG-002 |
| **Related TC** | TC-14 |
| **Related REQ** | REQ-03 |
| **Severity** | Medium |
| **Detected by** | Nguyen An Khanh |
| **Detection Date** | 25/05/2026 |
| **Status** | Open |

### Title
System cannot filter books using English category names.

### Precondition
Logged in and currently on Books page.

### Steps to Reproduce
1. Open category dropdown.
2. Select "Technology".

### Expected Result (SRS REQ-03)
Technology books should be displayed.

### Actual Result
No books found.

### Impact
English category filtering feature does not work as specified.

### Evidence

![BUG-002-01](images/BUG-002-01.png)

*Insert screenshot showing empty results after selecting Technology category.*

### Recommendation
Map English category names correctly to corresponding category data.

---

# 5.2 Borrow Book (REQ-04)

## BUG-003: Borrow Limit Validation Missing

| Field | Value |
|-------|-------|
| **Bug ID** | BUG-003 |
| **Related TC** | TC-17 |
| **Related REQ** | REQ-04 |
| **Severity** | Critical |
| **Detected by** | Nguyen An Khanh |
| **Detection Date** | 25/05/2026 |
| **Status** | Open |

### Title
Member can borrow more than the maximum limit of 3 books.

### Precondition
Member MEM002 already has 3 borrowed books.

### Steps to Reproduce
1. Login as MEM002.
2. Select an available book.
3. Click Borrow.

### Expected Result (SRS REQ-04)
- Display message "Reached limit of 3 books".
- Borrow request is rejected.

### Actual Result
- Borrow succeeds.
- Member can borrow a 4th book.

### Impact
Violates library borrowing policy.

### Evidence

![BUG-003-01](images/BUG-003-01.png)

*Insert screenshot showing successful borrowing despite reaching the limit.*

### Recommendation
Validate borrow count before creating a new borrow record.

---

## BUG-004: Wrong Error Message for Suspended Member

| Field | Value |
|-------|-------|
| **Bug ID** | BUG-004 |
| **Related TC** | TC-18 |
| **Related REQ** | REQ-04 |
| **Severity** | Medium |
| **Detected by** | Nguyen An Khanh |
| **Detection Date** | 25/05/2026 |
| **Status** | Open |

### Title
Suspended member receives an incorrect error message when borrowing a book.

### Precondition
Member MEM004 has status "Suspended".

### Steps to Reproduce
1. Login as MEM004.
2. Select an available book.
3. Click Borrow.

### Expected Result (SRS REQ-04)
Display "Account is suspended".

### Actual Result
Display "Member expired. Cannot borrow book".

### Impact
Misleading information is shown to users.

### Evidence

![BUG-004-01](images/BUG-004-01.png)

*Insert screenshot showing incorrect error message.*

### Recommendation
Return the correct validation message based on account status.

---

# 5.3 Return Book (REQ-05)

## BUG-005: Overdue Return Warning Is Missing

| Field | Value |
|-------|-------|
| **Bug ID** | BUG-005 |
| **Related TC** | TC-20 |
| **Related REQ** | REQ-05 |
| **Severity** | Medium |
| **Detected by** | Nguyen An Khanh |
| **Detection Date** | 25/05/2026 |
| **Status** | Open |

### Title
System does not display overdue warning when returning an overdue book.

### Precondition
Borrow record BR001 is overdue.

### Steps to Reproduce
1. Open Borrow/Return page.
2. Select BR001.
3. Click Return.

### Expected Result (SRS REQ-05)
- Return succeeds.
- Warning message "Book return is overdue" is displayed.

### Actual Result
- Return succeeds.
- No overdue warning is displayed.

### Impact
Librarians may not notice overdue returns.

### Evidence

![BUG-005-01](images/BUG-005-01.png)

*Insert screenshot showing successful return without overdue warning.*

### Recommendation
Display overdue notification before completing return process.

---

# 5.4 Overdue Handling (REQ-06)

## BUG-006: Repeated Overdue Check Produces Incorrect Result

| Field | Value |
|-------|-------|
| **Bug ID** | BUG-006 |
| **Related TC** | TC-23 |
| **Related REQ** | REQ-06 |
| **Severity** | Medium |
| **Detected by** | Nguyen An Khanh |
| **Detection Date** | 25/05/2026 |
| **Status** | Open |

### Title
Repeated overdue checking reports incorrect overdue count.

### Precondition
Overdue records have already been marked.

### Steps to Reproduce
1. Click "Check overdue books".
2. Click "Check overdue books" again.

### Expected Result (SRS REQ-06)
System should maintain correct overdue status.

### Actual Result
System reports 0 overdue records.

### Impact
Overdue information becomes inconsistent.

### Evidence

![BUG-006-01](images/BUG-006-01.png)

*Insert screenshot showing incorrect overdue count after second check.*

### Recommendation
Make overdue checking idempotent and preserve existing overdue status.

---

# 5.5 Member Management (REQ-07)

## BUG-007: Valid Email Is Rejected During Member Creation

| Field | Value |
|-------|-------|
| **Bug ID** | BUG-007 |
| **Related TC** | TC-24 |
| **Related REQ** | REQ-07 |
| **Severity** | High |
| **Detected by** | Nguyen An Khanh |
| **Detection Date** | 25/05/2026 |
| **Status** | Open |

### Title
System rejects valid email address when creating a member.

### Precondition
Librarian logged in.

### Steps to Reproduce
1. Open Members page.
2. Click Add Member.
3. Enter valid information.
4. Submit form.

### Expected Result (SRS REQ-07)
Member is created successfully.

### Actual Result
System displays "Invalid email".

### Impact
Valid members cannot be added.

### Evidence

![BUG-007-01](images/BUG-007-01.png)

*Insert screenshot showing valid email rejected.*

### Recommendation
Review email validation logic.

---

## BUG-008: Invalid Email Is Accepted

| Field | Value |
|-------|-------|
| **Bug ID** | BUG-008 |
| **Related TC** | TC-26 |
| **Related REQ** | REQ-07 |
| **Severity** | High |
| **Detected by** | Nguyen An Khanh |
| **Detection Date** | 25/05/2026 |
| **Status** | Open |

### Title
System accepts invalid email format and creates member account.

### Precondition
Librarian logged in.

### Steps to Reproduce
1. Open Add Member form.
2. Enter email `test@email`.
3. Enter valid phone number.
4. Submit form.

### Expected Result (SRS REQ-07)
Display "Invalid email" and reject creation.

### Actual Result
Member is created successfully.

### Impact
Invalid member data is stored in the system.

### Evidence

![BUG-008-01](images/BUG-008-01.png)

*Insert screenshot showing member created with invalid email.*

### Recommendation
Validate email format before saving.

---

## BUG-009: Email Validation Not Triggered When Multiple Fields Are Invalid

| Field | Value |
|-------|-------|
| **Bug ID** | BUG-009 |
| **Related TC** | TC-27 |
| **Related REQ** | REQ-07 |
| **Severity** | Medium |
| **Detected by** | Nguyen An Khanh |
| **Detection Date** | 25/05/2026 |
| **Status** | Open |

### Title
System only validates phone number when both email and phone are invalid.

### Precondition
Librarian logged in.

### Steps to Reproduce
1. Open Add Member form.
2. Enter email `test@email`.
3. Enter phone `abc`.
4. Submit form.

### Expected Result (SRS REQ-07)
Display email error or both validation errors.

### Actual Result
Only phone validation error is displayed.

### Impact
Users cannot identify all invalid fields.

### Evidence

![BUG-009-01](images/BUG-009-01.png)

*Insert screenshot showing only phone validation error.*

### Recommendation
Validate all fields before returning errors.

---

# 5.6 Borrow Record Lookup (REQ-08)

## BUG-010: Member Can View Other Members' Borrow Records

| Field | Value |
|-------|-------|
| **Bug ID** | BUG-010 |
| **Related TC** | TC-29 |
| **Related REQ** | REQ-08 |
| **Severity** | Critical |
| **Detected by** | Nguyen An Khanh |
| **Detection Date** | 25/05/2026 |
| **Status** | Open |

### Title
Member account can access borrow records belonging to another member.

### Precondition
Logged in as a normal member.

### Steps to Reproduce
1. Login as a member.
2. Open Borrow/Return page.
3. View borrow records.

### Expected Result (SRS REQ-08)
Member can only view their own borrow records.

### Actual Result
Member can view records belonging to MEM003.

### Impact
Violates access control and user privacy requirements.

### Evidence

![BUG-010-01](images/BUG-010-01.png)

*Insert screenshot showing another member's records visible.*

### Recommendation
Apply authorization checks based on logged-in user ID.
