# Bug Reports — Báo cáo lỗi

> **Hướng dẫn**: Tạo 1 mục bug cho mỗi TC có kết quả **Fail**.
> Xem [examples/sample-bug-report.md](../examples/sample-bug-report.md) để hiểu cách viết bug report tốt.
> Mỗi bug cần: tiêu đề mô tả hành vi lỗi, bước tái hiện, expected vs actual, severity + giải thích.

| Thông tin | |
|---|---|
| **Nhóm** | Group 12 |
| **Ngày báo cáo** | 27/05/2026 |

---

 # Defect Reports

## A. Common Information

| Field       | Value                                   |
| ----------- | --------------------------------------- |
| Project     | ABC Library Borrowing Management System |
| Version     | v1.0                                    |
| Environment | Chrome Browser on Windows               |
| Team        | Group 12                                |
| Test Period | May 2026                               |
| Status      | Open                                    |

---

# B. Bug Reports

## 1. Search & Filter Books (REQ-03)

### BUG-001: Search and Filter Do Not Combine

| Field       | Value   |
| ----------- | ------- |
| Bug ID      | BUG-001 |
| Related TC  | TC-11   |
| Related REQ | REQ-03  |
| Severity    | High    |
| Status      | Open    |

#### Title

Search and category filter do not work together.

#### Precondition

User logged in and currently on Books page.

#### Steps to Reproduce

1. Select category "Literature".
2. Enter keyword "Flutter".
3. Press Enter.

#### Expected Result

* Search and filter conditions are applied together.
* System displays "No books found".

#### Actual Result

* BOOK001 "Lập trình Flutter cơ bản" is displayed.
* Category filter condition is ignored.

#### Impact

Users cannot accurately search within a selected category.

#### Evidence

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0b03c7bc-fa9f-48b2-a35a-1cac282f4020" />


#### Recommendation

Apply search and category conditions using AND logic.

---

### BUG-002: English Category Filtering Does Not Work

| Field       | Value   |
| ----------- | ------- |
| Bug ID      | BUG-002 |
| Related TC  | TC-14   |
| Related REQ | REQ-03  |
| Severity    | Medium  |
| Status      | Open    |

#### Title

System cannot filter books using English category names.

#### Precondition

User logged in and currently on Books page.

#### Steps to Reproduce

1. Open category dropdown.
2. Select "Technology".

#### Expected Result

Books in Technology category are displayed.

#### Actual Result

No books are displayed.

#### Impact

English filtering functionality does not work as specified.

#### Evidence

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d6f090b9-4f1c-4afb-9d16-5487b3fba5a0" />


#### Recommendation

Map English category names correctly to category data.

---

## 2. Borrow Book (REQ-04)

### BUG-003: Borrow Limit Validation Missing

| Field       | Value    |
| ----------- | -------- |
| Bug ID      | BUG-003  |
| Related TC  | TC-17    |
| Related REQ | REQ-04   |
| Severity    | Critical |
| Status      | Open     |

#### Title

Member can borrow more than the maximum limit of 3 books.

#### Precondition

Member MEM002 already has 3 borrowed books.

#### Steps to Reproduce

1. Login as MEM002.
2. Select an available book.
3. Click Borrow.

#### Expected Result

* Display message "Reached limit of 3 books".
* Borrow request is rejected.

#### Actual Result

* Borrow request succeeds.
* Member can borrow a fourth book.

#### Impact

Violates core library borrowing policy.

#### Evidence

![BUG-003](images/BUG-003.png)

*Insert screenshot showing successful borrowing despite reaching limit.*

#### Recommendation

Validate current borrowed book count before creating a new borrow record.

---

### BUG-004: Wrong Error Message for Suspended Member

| Field       | Value   |
| ----------- | ------- |
| Bug ID      | BUG-004 |
| Related TC  | TC-18   |
| Related REQ | REQ-04  |
| Severity    | Medium  |
| Status      | Open    |

#### Title

Suspended member receives incorrect error message when borrowing a book.

#### Precondition

Member MEM004 has status "Suspended".

#### Steps to Reproduce

1. Login as MEM004.
2. Select an available book.
3. Click Borrow.

#### Expected Result

Display message:
"Account is suspended"

#### Actual Result

Display message:
"Member expired. Cannot borrow book"

#### Impact

Users receive misleading information about account status.

#### Evidence

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/80a6d6fc-26be-42cc-9ab3-12da214d0738" />


#### Recommendation

Return correct validation message according to member status.

---

## 3. Return Book (REQ-05)

### BUG-005: Overdue Return Warning Is Missing

| Field       | Value   |
| ----------- | ------- |
| Bug ID      | BUG-005 |
| Related TC  | TC-20   |
| Related REQ | REQ-05  |
| Severity    | Medium  |
| Status      | Open    |

#### Title

System does not display overdue warning when returning overdue books.

#### Precondition

Borrow record BR001 is overdue.

#### Steps to Reproduce

1. Open Borrow/Return page.
2. Select BR001.
3. Click Return.

#### Expected Result

* Return succeeds.
* Warning message "Book return is overdue" is displayed.

#### Actual Result

* Return succeeds.
* No overdue warning is displayed.

#### Impact

Librarians may not recognize overdue returns.

#### Evidence

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/baf433f1-3a6f-4b0e-929d-7a1be653f0c7" />


#### Recommendation

Display overdue warning before completing return process.

---

## 4. Overdue Handling (REQ-06)

### BUG-006: Repeated Overdue Check Produces Incorrect Result

| Field       | Value   |
| ----------- | ------- |
| Bug ID      | BUG-006 |
| Related TC  | TC-23   |
| Related REQ | REQ-06  |
| Severity    | Medium  |
| Status      | Open    |

#### Title

Repeated overdue checking reports incorrect overdue count.

#### Precondition

Overdue records have already been marked.

#### Steps to Reproduce

1. Click "Check overdue books".
2. Click "Check overdue books" again.

#### Expected Result

System maintains correct overdue status.

#### Actual Result

System reports 0 overdue records.

#### Impact

Overdue information becomes inconsistent.

#### Evidence

![BUG-006](images/BUG-006.png)

*Insert screenshot showing incorrect overdue count.*

#### Recommendation

Make overdue checking idempotent and preserve existing overdue status.

---

## 5. Member Management (REQ-07)

### BUG-007: Valid Email Is Rejected During Member Creation

| Field       | Value   |
| ----------- | ------- |
| Bug ID      | BUG-007 |
| Related TC  | TC-24   |
| Related REQ | REQ-07  |
| Severity    | High    |
| Status      | Open    |

#### Title

System rejects valid email address when creating a member.

#### Expected Result

Member is created successfully.

#### Actual Result

System displays "Invalid email".

#### Impact

Valid members cannot be added to the system.

#### Evidence

![BUG-007](images/BUG-007.png)

*Insert screenshot showing valid email rejected.*

#### Recommendation

Review email validation logic.

---

### BUG-008: Invalid Email Is Accepted

| Field       | Value   |
| ----------- | ------- |
| Bug ID      | BUG-008 |
| Related TC  | TC-26   |
| Related REQ | REQ-07  |
| Severity    | High    |
| Status      | Open    |

#### Title

System accepts invalid email format and creates member account.

#### Expected Result

Display "Invalid email" and reject member creation.

#### Actual Result

Member account is created successfully.

#### Impact

Invalid data is stored in the system.

#### Evidence

![BUG-008](images/BUG-008.png)

*Insert screenshot showing invalid email accepted.*

#### Recommendation

Validate email format before saving data.

---

### BUG-009: Email Validation Not Triggered When Multiple Fields Are Invalid

| Field       | Value   |
| ----------- | ------- |
| Bug ID      | BUG-009 |
| Related TC  | TC-27   |
| Related REQ | REQ-07  |
| Severity    | Medium  |
| Status      | Open    |

#### Title

System only validates phone number when both email and phone are invalid.

#### Expected Result

Display email error or both validation errors.

#### Actual Result

Only phone validation error is displayed.

#### Impact

Users cannot identify all invalid input fields.

#### Evidence

![BUG-009](images/BUG-009.png)

*Insert screenshot showing only phone validation error.*

#### Recommendation

Validate all fields before returning error messages.

---

## 6. Borrow Record Lookup (REQ-08)

### BUG-010: Member Can View Other Members' Borrow Records

| Field       | Value    |
| ----------- | -------- |
| Bug ID      | BUG-010  |
| Related TC  | TC-29    |
| Related REQ | REQ-08   |
| Severity    | Critical |
| Status      | Open     |

#### Title

Member account can access borrow records belonging to another member.

#### Expected Result

Member can only view their own borrow records.

#### Actual Result

Member can view records belonging to another member (MEM003).

#### Impact

Violates access control and user privacy requirements.

#### Evidence

![BUG-010](images/BUG-010.png)

*Insert screenshot showing another member's records visible.*

#### Recommendation

Apply authorization checks based on logged-in member ID.
