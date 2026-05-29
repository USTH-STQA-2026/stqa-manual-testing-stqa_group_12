# Bug Reports — Báo cáo lỗi

> **Hướng dẫn**: Tạo 1 mục bug cho mỗi TC có kết quả **Fail**.
> Xem [examples/sample-bug-report.md](../examples/sample-bug-report.md) để hiểu cách viết bug report tốt.
> Mỗi bug cần: tiêu đề mô tả hành vi lỗi, bước tái hiện, expected vs actual, severity + giải thích.

| Thông tin | |
|---|---|
| **Nhóm** | Group 12 |
| **Ngày báo cáo** | 27/05/2026 |

---

## BUG-001: Search and filter do not combine

| Field | Value |
|-------|-------|
| **Bug ID** | BUG-001 |
| **Related TC** | TC-11 |
| **Related REQ** | REQ-03 |
| **Severity** | High |
| **Detected by** | Nguyen An Khánh |
| **Detection Date** | 25/05/2026 |
| **Status** | Open |

**Title:**
When filtering by "Literature" category and searching for keyword "Flutter", system still displays Flutter book (belongs to Technology category)

**Environment:**
- Browser: Chrome
- OS: Windows
- UI Language: Vietnamese

**Precondition:**
Logged in, on Books tab

**Steps to Reproduce:**
1. Select category "Literature" from filter dropdown
2. Enter keyword "Flutter" in search box
3. Press Enter

**Expected Result (SRS REQ-03):**
- Search and filter must work together
- System should display message "No books found" because no Literature book contains "Flutter"

**Actual Result:**
- Still displays book "Lập trình Flutter cơ bản" (belongs to Technology category)
- Search and filter are applied independently, not together

**Impact:**
User cannot search accurately within a filtered category. Filter and search functions work independently instead of combining as specified in SRS.

**Evidence:**
(Screenshot showing Literature filter + Flutter search still returns Technology book)

**Recommendation:**
Apply both conditions with AND operator: (Category = X) AND (Title contains Y OR Author contains Y)

---

## BUG-002: 3-book limit not enforced

| Field | Value |
|-------|-------|
| **Bug ID** | BUG-002 |
| **Related TC** | TC-15 |
| **Related REQ** | REQ-04 |
| **Severity** | High |
| **Detected by** | Nguyen An Khánh |
| **Detection Date** | 25/05/2026 |
| **Status** | Open |

**Title:**
Can still borrow 4th book when already borrowed 3 books (limit not enforced)

**Environment:**
- Browser: Chrome
- OS: Windows
- UI Language: Vietnamese

**Precondition:**
Logged in as member ba.nguyen@email.com (MEM002), currently has exactly 3 borrowed books

**Steps to Reproduce:**
1. Go to Books tab
2. Find an "Available" book as the 4th book
3. Click "+" button
4. Click "Borrow"

**Expected Result (SRS REQ-04):**
- Maximum limit is 3 books per member
- System must reject borrowing, display error message "Reached limit of 3 books"
- Number of borrowed books remains 3

**Actual Result:**
- Can still borrow the 4th book
- Number of borrowed books increases to 4
- No error message displayed

**Impact:**
Violates core business rule specified in SRS, allows borrowing beyond the limit of 3 books per member

**Evidence:**
(Screenshot showing successful borrowing of 4th book)

**Recommendation:**
Add validation: if (current_borrowed_books >= 3) then reject borrow, display error message

---

## BUG-003: Cannot distinguish Suspended vs Expired error messages

| Field | Value |
|-------|-------|
| **Bug ID** | BUG-003 |
| **Related TC** | TC-16, TC-17 |
| **Related REQ** | REQ-04 |
| **Severity** | High |
| **Detected by** | Nguyen An Khanh |
| **Detection Date** | 25/05/2026 |
| **Status** | Open |

**Title:**
System does not distinguish error messages between Suspended account and Expired library card as required by SRS

**Environment:**
- Browser: Chrome
- OS: Windows
- UI Language: Vietnamese

**Precondition:**
Have Suspended account (cu.le@email.com - MEM004) and Expired account (binh.pham@email.com - MEM005) as per SRS section 3.1

**Steps to Reproduce:**
1. Login with Suspended account cu.le@email.com
2. Borrow any "Available" book
3. Logout
4. Login with Expired account binh.pham@email.com
5. Borrow any "Available" book

**Expected Result (SRS REQ-04):**
- Suspended account: "Account is suspended"
- Expired account: "Library card has expired"
- System must display different error messages for each case

**Actual Result:**
- Both display: "Member expired. Cannot borrow book"
- Same message for both suspended and expired accounts

**Impact:**
Users cannot know the exact reason why they cannot borrow (suspended or expired), causing confusion. Violates SRS requirement that error messages must distinguish between suspended and expired.

**Evidence:**
(Screenshot of error messages for both accounts showing same message)

**Recommendation:**
Distinguish between the two cases based on account status from SRS section 3.1 and display different error messages as specified

---

## BUG-004: Return overdue book shows no warning

| Field | Value |
|-------|-------|
| **Bug ID** | BUG-004 |
| **Related TC** | TC-19, TC-21 |
| **Related REQ** | REQ-05 |
| **Severity** | High |
| **Detected by** | Nguyen An Khanh |
| **Detection Date** | 25/05/2026 |
| **Status** | Open |

**Title:**
When returning an overdue book, system does not display overdue warning as required by SRS

**Environment:**
- Browser: Chrome
- OS: Windows
- UI Language: Vietnamese

**Precondition:**
Logged in as Librarian, have overdue borrow record (BR001 - due date 15/09/2024) as per SRS section 3.3

**Steps to Reproduce:**
1. Go to Borrow/Return tab
2. Find borrow record BR001 (due date 15/09/2024)
3. Click "Return" button

**Expected Result (SRS REQ-05):**
- Return request succeeds
- System MUST display overdue warning when return date > due date

**Actual Result:**
- Return successful
- NO overdue warning displayed

**Impact:**
Librarian is not warned when returning overdue books. Violates SRS requirement for overdue handling.

**Evidence:**
(Screenshot after returning book showing no warning)

**Recommendation:**
When return_date > due_date, display warning "Book return is overdue" before or after confirming return as specified in SRS

---

## BUG-005: Overdue check only works correctly once

| Field | Value |
|-------|-------|
| **Bug ID** | BUG-005 |
| **Related TC** | TC-23 |
| **Related REQ** | REQ-06 |
| **Severity** | Medium |
| **Detected by** | Nguyen An Khanh |
| **Detection Date** | 25/05/2026 |
| **Status** | Open |

**Title:**
"Check overdue" function only works correctly the first time, second time reports 0 overdue records

**Environment:**
- Browser: Chrome
- OS: Windows
- UI Language: Vietnamese

**Precondition:**
Logged in as Librarian, have overdue borrow record (BR001) as per SRS section 3.3

**Steps to Reproduce:**
1. Go to Borrow/Return tab
2. Click "Check overdue" first time → detects 2 overdue-related records
3. Click "Check overdue" second time

**Expected Result (SRS REQ-06):**
- Each time Librarian clicks the button, system must scan all borrow records
- Any record with dueDate ≤ current date must be marked as "Overdue"

**Actual Result:**
- First time: correctly detects overdue records
- Second time: reports "Update: 0 overdue records"

**Impact:**
After first click, Librarian cannot re-check overdue status if new overdue records appear. Violates SRS requirement that function must work each time it is activated.

**Evidence:**
(Screenshot of second click showing 0 overdue records)

**Recommendation:**
Reset status or re-scan based on due_date vs current date each time the button is clicked as specified in SRS

---

## BUG-006: Email validation error (valid rejected, invalid accepted)

| Field | Value |
|-------|-------|
| **Bug ID** | BUG-006 |
| **Related TC** | TC-23 |
| **Related REQ** | REQ-07 |
| **Severity** | High |
| **Detected by** | Nguyen An Khanh |
| **Detection Date** | 25/05/2026 |
| **Status** | Open |

**Title:**
Email validation does not comply with SRS: valid email rejected, invalid email accepted

**Environment:**
- Browser: Chrome
- OS: Windows
- UI Language: Vietnamese

**Precondition:**
Logged in as Librarian, on Members tab

**Steps to Reproduce for valid email rejected:**
1. Click "Add member"
2. Enter Email: `vana@email.com` (valid format: has @ and .com)
3. Enter other valid information
4. Click "Save"

**Expected Result (SRS REQ-07):**
- Email must be valid (has @ AND dot in domain, e.g., user@domain.com)
- Email `vana@email.com` is valid → add successful

**Actual Result:**
- Error message: "Invalid email"

**Steps to Reproduce for invalid email accepted:**
1. Click "Add member"
2. Enter Email: `vana@email` (invalid - missing dot after @)
3. Click "Save"

**Expected Result (SRS REQ-07):**
- Email `user@domain` is NOT valid → error message "Invalid email"

**Actual Result:**
- Add successful

**Impact:**
Users cannot add members with valid email format, and can add members with invalid email format. Violates SRS validation rules.

**Evidence:**
(Screenshots of both cases)

**Recommendation:**
Fix email validation logic according to SRS: only accept email with @ and dot after @

---

## BUG-007: Invalid email + valid phone still creates member

| Field | Value |
|-------|-------|
| **Bug ID** | BUG-007 |
| **Related TC** | TC-27 |
| **Related REQ** | REQ-07 |
| **Severity** | High |
| **Detected by** | Nguyen An Khanh |
| **Detection Date** | 25/05/2026 |
| **Status** | Open |

**Title:**
Invalid email (missing .com) + valid phone number still creates member, bypassing email validation

**Environment:**
- Browser: Chrome
- OS: Windows
- UI Language: Vietnamese

**Precondition:**
Logged in as Librarian, on Members tab

**Steps to Reproduce:**
1. Click "Add member"
2. Enter Email: `test@email` (invalid - missing .com)
3. Enter Phone: `0123456789` (valid)
4. Enter valid name
5. Click "Save"

**Expected Result (SRS REQ-07):**
- Email must be valid (has @ AND dot in domain)
- Invalid email → error message "Invalid email", member NOT created

**Actual Result:**
- Add successful, new member appears in member list
- Email validation is skipped when phone number is valid

**Impact:**
System bypasses email validation. Invalid emails are accepted. Violates SRS requirement for email validation.

**Evidence:**
(Screenshot of new member created with invalid email)

**Recommendation:**
Validate email first according to SRS. If email is invalid, display error and do NOT create member, regardless of phone number

---

## BUG-008: Both email and phone invalid only shows phone error

| Field | Value |
|-------|-------|
| **Bug ID** | BUG-008 |
| **Related TC** | TC-28 |
| **Related REQ** | REQ-07 |
| **Severity** | Medium |
| **Detected by** | Nguyen An Khanh |
| **Detection Date** | 25/05/2026 |
| **Status** | Open |

**Title:**
When both email and phone are invalid, system only shows phone error, does not show email error

**Environment:**
- Browser: Chrome
- OS: Windows
- UI Language: Vietnamese

**Precondition:**
Logged in as Librarian, on Members tab

**Steps to Reproduce:**
1. Click "Add member"
2. Enter Email: `test@email` (invalid - missing .com)
3. Enter Phone: `abc` (invalid)
4. Click "Save"

**Expected Result (SRS REQ-07):**
- Invalid email must be reported
- Show both errors or prioritize email error

**Actual Result:**
- Only shows "Invalid phone number"
- No email error displayed

**Impact:**
Users do not know email is also invalid, must fix multiple times. Violates SRS email validation requirement.

**Evidence:**
(Screenshot showing only phone error message)

**Recommendation:**
Validate all fields according to SRS and display all errors, or prioritize displaying email error first

---

## BUG-009: Valid email + invalid phone shows wrong error

| Field | Value |
|-------|-------|
| **Bug ID** | BUG-009 |
| **Related TC** | TC-29 |
| **Related REQ** | REQ-07 |
| **Severity** | Medium |
| **Detected by** | Nguyen An Khanh |
| **Detection Date** | 25/05/2026 |
| **Status** | Open |

**Title:**
Valid email + invalid phone shows "Invalid email" error instead of "Invalid phone number"

**Environment:**
- Browser: Chrome
- OS: Windows
- UI Language: Vietnamese

**Precondition:**
Logged in as Librarian, on Members tab

**Steps to Reproduce:**
1. Click "Add member"
2. Enter Email: `test@email.com` (valid - has @ and .com)
3. Enter Phone: `abc` (invalid)
4. Click "Save"

**Expected Result (SRS REQ-07):**
- Email is valid, only phone is invalid
- Error message: "Invalid phone number"

**Actual Result:**
- Error message: "Invalid email"

**Impact:**
Users get confused, fix email when the actual error is in phone number. Incorrect error message violates SRS clarity.

**Evidence:**
(Screenshot showing wrong error message)

**Recommendation:**
Check each field individually and display the correct error corresponding to the invalid field according to SRS

---

## BUG-010: Category filter does not work after switching to English

| Field | Value |
|-------|-------|
| **Bug ID** | BUG-010 |
| **Related TC** | TC-32 |
| **Related REQ** | REQ-03 |
| **Severity** | High |
| **Detected by** | Nguyen An Khanh |
| **Detection Date** | 27/05/2026 |
| **Status** | Open |

**Title:**
After switching language to English, filtering by "Technology" category does not display Technology books

**Environment:**
- Browser: Chrome
- OS: Windows
- UI Language: English (after switching)

**Precondition:**
Logged in, switched language to English using UI language toggle (SRS section 5: Bilingual UI)

**Steps to Reproduce:**
1. Click "EN" button to switch to English
2. Go to Books tab
3. Select category "Technology" from filter dropdown
4. Observe results

**Expected Result (SRS REQ-03):**
- Filter must work regardless of UI language
- Should display Technology category books (BOOK001, BOOK002, BOOK003, BOOK005, BOOK008, BOOK009, BOOK010, BOOK011)

**Actual Result:**
- No books found (empty list)

**Impact:**
International users cannot use category filter functionality in English. Violates bilingual UI requirement.

**Evidence:**
(Screenshot showing "Technology" dropdown with no results)

**Recommendation:**
Map English category names ("Technology") to Vietnamese category data ("Công nghệ") when filtering

---

## BUG-011: Category dropdown not translated to English

| Field | Value |
|-------|-------|
| **Bug ID** | BUG-011 |
| **Related TC** | TC-33 |
| **Related REQ** | REQ-03 |
| **Severity** | Medium |
| **Detected by** | Nguyen An Khanh |
| **Detection Date** | 27/05/2026 |
| **Status** | Open |

**Title:**
After switching language to English, category filter dropdown still displays in Vietnamese

**Environment:**
- Browser: Chrome
- OS: Windows
- UI Language: English (after switching)

**Precondition:**
Logged in, switched language to English using UI language toggle (SRS section 5: Bilingual UI)

**Steps to Reproduce:**
1. Click "EN" button to switch to English
2. Go to Books tab
3. Click on category filter dropdown

**Expected Result (SRS section 5 - Bilingual UI):**
- When UI language is English, all interface elements must display in English
- Dropdown should display: "Technology", "Education", "Economics", "Soft Skills", "Management", "Literature"

**Actual Result:**
- Dropdown still displays in Vietnamese: "Công nghệ", "Giáo dục", "Kinh tế", "Kỹ năng mềm", "Quản trị", "Văn học"

**Impact:**
UI is not fully translated as required by SRS bilingual specification, causing difficulty for international users

**Evidence:**
(Screenshot showing dropdown displays Vietnamese when in English mode)

**Recommendation:**
Translate category labels when switching language according to SRS bilingual UI requirement

---

## BUG-012: Member can view other member's borrow records

| Field | Value |
|-------|-------|
| **Bug ID** | BUG-012 |
| **Related TC** | TC-26 |
| **Related REQ** | REQ-08 |
| **Severity** | High |
| **Detected by** | Nguyen An Khanh |
| **Detection Date** | 26/05/2026 |
| **Status** | Open |

**Title:**
Member can search and view other member's borrow records, violating SRS access control

**Environment:**
- Browser: Chrome
- OS: Windows
- UI Language: Vietnamese

**Precondition:**
Logged in as member ba.nguyen@email.com (MEM002) as per SRS section 3.1

**Steps to Reproduce:**
1. Go to Borrow/Return tab
2. Search or try to view other member's borrow records (e.g., enter MEM003 code)

**Expected Result (SRS REQ-08):**
- Members can ONLY view their own borrow records
- Members CANNOT view borrow records of other members
- Error message: "You do not have permission to view other member's borrow records"

**Actual Result:**
- Can still view MEM003's borrow records (BR002, BR005)
- No permission error displayed
- Security breach

**Impact:**
Security vulnerability. Members can view any other member's borrowing history. Violates SRS access control requirement.

**Evidence:**
(Screenshot showing member viewing another member's records)

**Recommendation:**
When member logs in, only fetch and display borrow records of that specific member ID from session. If search function exists, check permissions before returning results as specified in SRS REQ-08

<!-- Copy template BUG trên để thêm BUG-03, BUG-04, ... cho mỗi TC Fail -->
