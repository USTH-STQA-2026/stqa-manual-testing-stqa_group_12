# Test Summary — Báo cáo tổng hợp kiểm thử

> **Hướng dẫn**: Đây là hoạt động **Quality Assurance** — bạn đánh giá chất lượng tổng thể của phần mềm, không chỉ liệt kê lỗi.

---

# Summary Report — Quality Assurance

## 1. Team Information

| Field             | Information                |
| ----------------- | -------------------------- |
| **Team**          | Group 12                   |
| **Class**         | 52ICT2012.L1               |
| **Report Date**   | 28/05/2026                 |
| **Tested System** | https://stqa.rbc.vn — v1.0 |
| **Link Github** | https://github.com/USTH-STQA-2026/stqa-manual-testing-stqa_group_12 |

---

## 2. Test Execution Summary

| Metric                  | Value     |
| ----------------------- | --------- |
| Total Test Cases        | 29        |
| Pass                    | 19        |
| Fail                    | 10        |
| Blocked                 | 0         |
| Not Run                 | 0         |
| **Pass Rate**           | **65.5%** |
| **Total Bugs Detected** | **10**    |

### Distribution by Function Group

| Function Group                 | TC | Pass | Fail | Bug IDs                   | Assessment |
| ------------------------------ | -- | ---- | ---- | ------------------------- | ---------- |
| Login (REQ-01)                 | 4  | 4    | 0    | -                         | ✅ Good     |
| View Book List (REQ-02)        | 1  | 1    | 0    | -                         | ✅ Good     |
| Search & Filter Books (REQ-03) | 9  | 7    | 2    | BUG-001, BUG-002          | ⚠️ Fair    |
| Borrow Book (REQ-04)           | 4  | 2    | 2    | BUG-003, BUG-004          | ❌ Poor     |
| Return Book (REQ-05)           | 3  | 2    | 1    | BUG-005                   | ⚠️ Fair    |
| Overdue Handling (REQ-06)      | 2  | 1    | 1    | BUG-006                   | ⚠️ Fair    |
| Member Management (REQ-07)     | 4  | 1    | 3    | BUG-007, BUG-008, BUG-009 | ❌ Poor     |
| Borrow Record Lookup (REQ-08)  | 2  | 1    | 1    | BUG-010                   | ❌ Poor     |

### Bug Distribution by Severity

| Severity  | Count  | Bug IDs                                                       |
| --------- | ------ | ------------------------------------------------------------- |
| 🔴 High   | 7      | BUG-001, BUG-003, BUG-004, BUG-005, BUG-007, BUG-008, BUG-010 |
| 🟡 Medium | 3      | BUG-002, BUG-006, BUG-009                                     |
| 🟢 Low    | 0      | -                                                             |
| **Total** | **10** |                                                                |

---

## 3. Test Design Techniques Used

| Technique                         | Applied to REQ                                         | Number of TCs | Explanation                                                                                      |
| --------------------------------- | ------------------------------------------------------ | ------------- | ------------------------------------------------------------------------------------------------ |
| **EP (Equivalence Partitioning)** | REQ-01, REQ-02, REQ-03, REQ-05, REQ-06, REQ-07, REQ-08 | 24            | Representative values were selected from valid and invalid equivalence classes.                  |
| **BVA (Boundary Value Analysis)** | REQ-04, REQ-05, REQ-07                                 | 3             | Tested important boundaries such as borrowing limit and invalid email format.                    |
| **Decision Table**                | REQ-04                                                 | 2             | Used to verify borrowing outcomes under different combinations of book status and member status. |

---

## 4. Software Quality Analysis

### 4.1. Strengths

| Area                               | Assessment                                                                     |
| ---------------------------------- | ------------------------------------------------------------------------------ |
| **Login Function (REQ-01)**        | Works correctly for valid and invalid credentials. Pass rate: 100%.            |
| **View Book List (REQ-02)**        | Displays all books and book information correctly. Pass rate: 100%.            |
| **Basic Search Function (REQ-03)** | Keyword search and category filtering work correctly in most normal scenarios. |
| **Role Separation**                | Librarian and Member interfaces are displayed differently.                     |

### 4.2. Weaknesses

| Area                              | Issue                                                                                  | Severity  |
| --------------------------------- | -------------------------------------------------------------------------------------- | --------- |
| **Search & Filter (REQ-03)**      | Search and category filter are not combined correctly.                                 | 🔴 High   |
| **Borrow Book (REQ-04)**          | Borrowing limit is not enforced; account status validation is incorrect.               | 🔴 High   |
| **Return Book (REQ-05)**          | Overdue returns do not display warning messages.                                       | 🔴 High   |
| **Overdue Handling (REQ-06)**     | Rechecking overdue records produces inconsistent results.                              | 🟡 Medium |
| **Member Management (REQ-07)**    | Email validation contains multiple defects causing incorrect member creation behavior. | 🔴 High   |
| **Borrow Record Lookup (REQ-08)** | Members can access records belonging to other members.                                 | 🔴 High   |

---

## 5. Bug Fix Priority Recommendations

> Priority is determined based on business impact and system risk.

| Priority | Order | Bug ID  | Severity  | Reason                                                            |
| -------- | ----- | ------- | --------- | ----------------------------------------------------------------- |
| **P0**   | 1     | BUG-010 | 🔴 High   | Security issue: members can access other members’ borrow records. |
| **P0**   | 2     | BUG-003 | 🔴 High   | Borrow limit rule is not enforced.                                |
| **P1**   | 3     | BUG-007 | 🔴 High   | Valid email addresses are rejected.                               |
| **P1**   | 4     | BUG-008 | 🔴 High   | Invalid email addresses are accepted.                             |
| **P1**   | 5     | BUG-004 | 🔴 High   | Incorrect validation for member status.                           |
| **P1**   | 6     | BUG-001 | 🔴 High   | Search and filter do not work together.                           |
| **P1**   | 7     | BUG-005 | 🔴 High   | Overdue return warning is missing.                                |
| **P2**   | 8     | BUG-006 | 🟡 Medium | Overdue checking is inconsistent.                                 |
| **P2**   | 9     | BUG-002 | 🟡 Medium | English category filtering does not work.                         |
| **P2**   | 10    | BUG-009 | 🟡 Medium | Incorrect validation message when multiple fields are invalid.    |

---

## 6. Conclusion

| Question                             | Answer                                                                                                                                          |
| ------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| **Is the system ready for release?** | ❌ **NO**                                                                                                                                        |
| **Why?**                             | The system still contains 10 confirmed defects, including several High severity issues affecting security, business rules, and data validation. |

### Required Actions Before Release

| Priority    | Action Required                                       | Bug ID                    |
| ----------- | ----------------------------------------------------- | ------------------------- |
| Must Fix    | Restrict members from viewing other members’ records. | BUG-010                   |
| Must Fix    | Enforce maximum borrowing limit of 3 books.           | BUG-003                   |
| Must Fix    | Correct member status validation logic.               | BUG-004                   |
| Must Fix    | Fix email validation defects.                         | BUG-007, BUG-008, BUG-009 |
| Should Fix  | Combine search and filter conditions correctly.       | BUG-001                   |
| Should Fix  | Display overdue return warnings.                      | BUG-005                   |
| Nice to Fix | Improve overdue rechecking behavior.                  | BUG-006                   |
| Nice to Fix | Support English category filtering correctly.         | BUG-002                   |

---

## 7. Lessons Learned

| Lesson                                         | Description                                                                                   |
| ---------------------------------------------- | --------------------------------------------------------------------------------------------- |
| **Always verify against SRS**                  | Several defects were identified because actual behavior differed from specified requirements. |
| **Negative testing is important**              | Most critical bugs were found through invalid-input scenarios.                                |
| **Boundary testing is effective**              | Borrowing limit defect was discovered using Boundary Value Analysis.                          |
| **Security testing should not be ignored**     | Permission-related defects may have serious consequences.                                     |
| **Requirement traceability improves coverage** | Mapping each test case to REQ IDs ensured all requirements were tested.                       |

---

## 8. AI Usage Declaration

| AI Tool      | Used For                                                                                                                      | Review Process                                                                                                                                        |
| ------------ | ----------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| **ChatGPT**  | Generating test case structures, bug report templates, test summary report formatting, and supporting test design activities. | All generated content was reviewed and adjusted according to the SRS, seed data, and actual testing results before inclusion in the final submission. |
| **DeepSeek** | Supporting IDM analysis, EP/BVA classification, Decision Table design, and brainstorming test scenarios.                      | Outputs were validated manually and aligned with project requirements, SRS specifications, and actual system behavior.                                |

---
