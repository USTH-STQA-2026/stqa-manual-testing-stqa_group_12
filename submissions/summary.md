# Test Summary — Báo cáo tổng hợp kiểm thử

> **Hướng dẫn**: Đây là hoạt động **Quality Assurance** — bạn đánh giá chất lượng tổng thể của phần mềm, không chỉ liệt kê lỗi.

---

# Summary Report — Quality Assurance

## 1. Team Information

| Field | Information |
|-------|-------------|
| **Team** | Group 12 |
| **Class** | 52ICT2012.L1 |
| **Report Date** | 28/05/2026 |
| **Tested System** | https://stqa.rbc.vn — v1.0 |

---

## 2. Test Execution Summary

| Metric | Value |
|--------|-------|
| Total Test Cases | 34 |
| Pass | 19 |
| Fail | 15 |
| Blocked | 0 |
| Not Run | 0 |
| **Pass Rate** | **55.9%** |
| **Total Bugs Detected** | **12** |

### Distribution by Function Group

| Function Group | TC | Pass | Fail | Bug IDs | Assessment |
|----------------|-----|------|------|---------|-------------|
| Login (REQ-01) | 5 | 5 | 0 | - | ✅ Good |
| View Book List (REQ-02) | 3 | 3 | 0 | - | ✅ Good |
| Search & Filter Books (REQ-03) | 11 | 7 | 4 | BUG-001, BUG-010, BUG-011 | ⚠️ Fair |
| Borrow Book (REQ-04) | 5 | 2 | 3 | BUG-002, BUG-003 | ❌ Poor |
| Return Book (REQ-05) | 3 | 1 | 2 | BUG-004 | ⚠️ Fair |
| Overdue Handling (REQ-06) | 2 | 1 | 1 | BUG-005 | ⚠️ Fair |
| Member Management (REQ-07) | 8 | 2 | 6 | BUG-006, BUG-007, BUG-008, BUG-009 | ❌ Very Poor |
| Borrow Record Lookup (REQ-08) | 2 | 1 | 1 | BUG-012 | ⚠️ Fair |

### Bug Distribution by Severity

| Severity | Count | Bug IDs |
|----------|-------|---------|
| 🔴 High | 8 | BUG-001, BUG-002, BUG-003, BUG-004, BUG-006, BUG-007, BUG-010, BUG-012 |
| 🟡 Medium | 4 | BUG-005, BUG-008, BUG-009, BUG-011 |
| 🟢 Low | 0 | - |
| **Total** | **12** | |

---

## 3. Test Design Techniques Used

| Technique | Applied to REQ | Number of TCs | Explanation |
|-----------|---------------|---------------|-------------|
| **EP (Equivalence Partitioning)** | REQ-01, REQ-02, REQ-03, REQ-04, REQ-05, REQ-06, REQ-07, REQ-08 | 28 | Divide input domains into partitions where each partition produces equivalent behavior. Only one representative needed per partition |
| **BVA (Boundary Value Analysis)** | REQ-04, REQ-05, REQ-07 | 4 | Test boundaries: 3-book limit (borrow 4th book), overdue boundary (return 1 day late), email format (missing dot) |
| **Decision Table** | REQ-04 | 1 | Combine multiple conditions: book status (available/borrowed), number of books (< 3 or =3), account status (active/suspended/expired) |

---

## 4. Software Quality Analysis

### 4.1. Strengths

| Area | Assessment |
|------|------------|
| **Login Function (REQ-01)** | Works correctly. All validation messages are accurate. Pass rate: 100% |
| **View Book List (REQ-02)** | Displays all 20 books correctly. Pass rate: 100% |
| **Search Function (REQ-03)** | Basic search works (case-insensitive). Pass rate: ~64% |
| **Role-Based Access** | Librarian sees Members tab, Members do not |

### 4.2. Weaknesses

| Area | Issue | Severity |
|------|-------|----------|
| **Member Management (REQ-07)** | Email validation completely broken: valid emails rejected, invalid emails accepted. Phone validation also has issues | 🔴 High |
| **Borrow Book (REQ-04)** | 3-book limit not enforced. Cannot distinguish Suspended vs Expired error messages | 🔴 High |
| **Borrow Record Lookup (REQ-08)** | Members can view other members' borrow records (security breach) | 🔴 High |
| **Search & Filter (REQ-03)** | Search and filter do not combine (work independently) | 🔴 High |
| **Return Book (REQ-05)** | Overdue books do not trigger warning message | 🔴 High |
| **Bilingual UI** | Category dropdown not translated to English; English filter does not work | 🟡 Medium |
| **Overdue Check (REQ-06)** | Only works correctly once; second click reports 0 records | 🟡 Medium |

---

## 5. Bug Fix Priority Recommendations

> **Priority Criteria:** Based on **severity** (technical impact) and **business impact** (user experience, security, compliance)

| Priority | Order | Bug ID | Severity | Reason for Priority |
|----------|-------|--------|----------|---------------------|
| **P0 (Highest)** | 1 | BUG-012 | 🔴 High | **Security breach** - Members can view other members' private borrow records. Must fix immediately |
| **P0 (Highest)** | 2 | BUG-002 | 🔴 High | **Core business rule violation** - 3-book limit not enforced. Allows borrowing beyond system limit |
| **P1 (High)** | 3 | BUG-006 | 🔴 High | **Email validation broken** - Valid emails rejected, users cannot be added with correct email format |
| **P1 (High)** | 4 | BUG-007 | 🔴 High | **Email validation bypass** - Invalid emails accepted, data integrity issue |
| **P1 (High)** | 5 | BUG-003 | 🔴 High | **Wrong error messages** - Users cannot distinguish between Suspended and Expired accounts |
| **P1 (High)** | 6 | BUG-001 | 🔴 High | **Search/filter broken** - Users cannot search within filtered categories |
| **P1 (High)** | 7 | BUG-004 | 🔴 High | **Missing overdue warning** - Librarians not warned about overdue returns |
| **P1 (High)** | 8 | BUG-010 | 🔴 High | **English filter broken** - International users cannot use category filter |
| **P2 (Medium)** | 9 | BUG-005 | 🟡 Medium | **Check overdue inconsistent** - Second click reports wrong data |
| **P2 (Medium)** | 10 | BUG-011 | 🟡 Medium | **UI not fully translated** - Category dropdown still in Vietnamese |
| **P2 (Medium)** | 11 | BUG-008 | 🟡 Medium | **Poor error messages** - Only shows phone error when both email and phone invalid |
| **P2 (Medium)** | 12 | BUG-009 | 🟡 Medium | **Wrong error message** - Shows email error when phone is actually invalid |

---

## 6. Conclusion

| Question | Answer |
|----------|--------|
| **Is the system ready for release?** | ❌ **NO** |
| **Why?** | The system has **8 High severity bugs** including security vulnerabilities, broken core business rules (3-book limit), and completely broken email validation. The system cannot be released in current state |

### Required Actions Before Release:

| Priority | Action Required | Bug ID | Severity | Detailed Description |
|----------|-----------------|--------|----------|---------------------|
| **Must fix** | Security issue: members can view other members' borrow records | BUG-012 | 🔴 High | Members can search and view other members' private borrow records. This is a security breach. Fix: When member logs in, only fetch and display borrow records of that specific member ID from session. If search function exists, check permissions before returning results |
| **Must fix** | 3-book limit not enforced (can borrow 4th book) | BUG-002 | 🔴 High | Members can borrow a 4th book when already borrowing 3 books. Violates core business rule. Fix: Add validation: if (current_borrowed_books >= 3) then reject borrow, display error message "Reached limit of 3 books" |
| **Must fix** | Email validation error: valid email rejected as invalid | BUG-006 | 🔴 High | Valid email format (e.g., `vana@email.com`) is rejected with error "Invalid email". Fix: Fix email validation logic according to SRS: only accept email with @ and dot after @ (e.g., user@domain.com) |
| **Must fix** | Email validation error: invalid email accepted (missing .com) | BUG-007 | 🔴 High | Invalid email (missing .com, e.g., `test@email`) is accepted and creates member successfully. Fix: Validate email first according to SRS. If email is invalid, display error and do NOT create member, regardless of phone number |
| **Must fix** | Cannot distinguish Suspended vs Expired error messages | BUG-003 | 🔴 High | Both Suspended and Expired accounts show same message: "Member expired. Cannot borrow book". SRS requires different messages. Fix: Distinguish between the two cases based on account status from SRS section 3.1. Suspended: "Account is suspended". Expired: "Library card has expired" |
| **Should fix** | Search and filter do not combine (apply independently) | BUG-001 | 🔴 High | When filtering by "Literature" and searching "Flutter", system still displays Technology books. Filter and search work independently. Fix: Apply both conditions with AND operator: (Category = X) AND (Title contains Y OR Author contains Y) |
| **Should fix** | Return overdue book shows no warning message | BUG-004 | 🔴 High | When returning an overdue book (BR001, due 15/09/2024), system does not display warning "Book return is overdue". Fix: When return_date > due_date, display warning "Book return is overdue" before or after confirming return as specified in SRS |
| **Should fix** | Category filter does not work after switching to English | BUG-010 | 🔴 High | After switching UI language to English, selecting "Technology" category returns no books. Fix: Map English category names ("Technology") to Vietnamese category data ("Công nghệ") when filtering |
| **Nice to fix** | Overdue check only works correctly once (second click reports 0) | BUG-005 | 🟡 Medium | First click of "Check overdue" detects overdue records. Second click reports "0 overdue records" even though records still exist. Fix: Reset status or re-scan based on due_date vs current date each time the button is clicked |
| **Nice to fix** | Both email and phone invalid only shows phone error (no email error) | BUG-008 | 🟡 Medium | When both email and phone are invalid, system only shows "Invalid phone number". No email error displayed. Fix: Validate all fields according to SRS and display all errors, or prioritize displaying email error first |
| **Nice to fix** | Valid email + invalid phone shows wrong error (shows email error) | BUG-009 | 🟡 Medium | Valid email + invalid phone shows "Invalid email" error instead of "Invalid phone number". Fix: Check each field individually and display the correct error corresponding to the invalid field according to SRS |
| **Nice to fix** | Category dropdown not translated to English after switching language | BUG-011 | 🟡 Medium | After switching UI language to English, category filter dropdown still displays in Vietnamese. Fix: Translate category labels when switching language according to SRS bilingual UI requirement |

---
---

## 7. Lessons Learned

| Lesson | Description |
|--------|-------------|
| **Always verify against SRS** | Many bugs found because actual behavior did not match SRS specifications |
| **Test both positive and negative** | Negative test cases (invalid email, expired account) revealed critical bugs |
| **Boundary testing is important** | BVA revealed the 3-book limit issue (tested exactly at the boundary) |
| **Bilingual UI needs separate testing** | English mode revealed missing translations and broken filters |
| **Security testing is essential** | BUG-012 (member viewing other's records) would have been missed without permission testing |

---

## 8. AI Usage Declaration

> If your team used AI tools (ChatGPT, Copilot, Gemini, DeepSeek, etc.), please declare them below. Honest declaration **does not affect your grade** — it is a professional transparency skill.

| AI Tool | Used for which part | How you reviewed / edited |
|---------|---------------------|---------------------------|
| **ChatGPT** | - Generating test case templates and structure<br>- Creating IDM (Input Domain Modeling) framework<br>- Suggesting negative test scenarios<br> | - Reviewed every test case against SRS requirements<br>- Changed Expected Results from "low oracle" (e.g., "Borrow successful") to "strong oracle" (e.g., "Borrow successful, number of borrowed books increases by 1, book status changes to Borrowed")<br>- Added specific details from SRS and seed data (actual book titles, book IDs, member emails, error messages)<br>- Verified that all steps match actual system UI<br>- Ensured each test case maps back to IDM |
| **DeepSeek** | - Classifying test design techniques (EP, BVA, Decision Table)<br>- Identifying characteristics and partitions for IDM<br>- Suggesting boundary values (BVA) for limits (3 books, overdue dates)<br>- Building Decision Table for REQ-04 (Borrow Book)<br>- Restructuring and reorganizing techniques for each test case | - Verified each test case has correct technique classification (which TC is EP, which is BVA, which is DT)<br>- Adjusted partitions in IDM to accurately reflect SRS<br>- Added missing boundary cases (e.g., return book 1 day overdue)<br>- Separated Decision Table into its own section with clear Rule explanations (Rule 1-5)<br>- Created color-coded bug severity system (Red/High, Yellow/Medium, Green/Low) |
