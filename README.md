# Travel BD — QA Test Reports

**Target Application:** `https://studio.unfaa.com`  
**Platform:** Travel Studio (All-in-one Travel Agency ERP by UNFAA)  
**Audit Date:** 2026-11-09  

---

## 📁 Test Report Workbooks (.xlsx)

* 📊 **[Travel_Bd_login.xlsx](./Travel_Bd_login.xlsx)** — Authentication & Sign-in feature test report
* 📊 **[Travel_Bd_register.xlsx](./Travel_Bd_register.xlsx)** — Registration & Request access feature test report
* 📊 **[Travel_Bd_forgot_password.xlsx](./Travel_Bd_forgot_password.xlsx)** — Password reset & OTP verification feature test report

---

## 1. Password Reset & OTP Verification (`Travel_Bd_forgot_password.xlsx`)

### 📊 Summary KPIs
| Total Tests | Passed | Failed | Pass Rate |
| :---: | :---: | :---: | :---: |
| **17** | **15** | **2** (TC-16, TC-17) | **88.2%** |

### 🐛 Identified Defects
* **BUG-01 (TC-16 | High):** Missing rate limiting and account lockout mechanism on password reset OTP verification (allows continuous OTP guessing attempts without any "Too Many Requests" lockout).
* **BUG-02 (TC-17 | Critical):** Password reset OTP verification email is never delivered to valid registered mailboxes (Broken DNS SPF record `v=spf1` / unconfigured backend SMTP transport on `tss.unfaa.com`).

---

## 2. Registration Feature Summary (`Travel_Bd_register.xlsx`)

### 📊 Summary KPIs
| Total Tests | Passed | Failed | Pass Rate |
| :---: | :---: | :---: | :---: |
| **16** | **12** | **4** (TC-07, TC-08, TC-15, TC-16) | **75.0%** |

### 🐛 Identified Defects
* **BUG-01 (TC-07):** Phone number field lacks clear client-side minimum length guidance / validation feedback.
* **BUG-02 (TC-08):** Date of birth picker year cannot be changed and always defaults to 2026.
* **BUG-03 (TC-15):** Missing server-side numeric validation on phone field (API accepts alphabetic strings and returns HTTP 201 Created).
* **BUG-04 (TC-16):** Rate limit response (HTTP 429) lacks standard `Retry-After` header.

---

## 3. Login Feature Summary (`Travel_Bd_login.xlsx`)

### 📊 Summary KPIs
| Total Tests | Passed | Failed | Pass Rate |
| :---: | :---: | :---: | :---: |
| **19** | **17** | **2** (TC-09, TC-11) | **89.5%** |

### 🐛 Identified Defects
* **BUG-01 (TC-09):** Sign-in button freezes in infinite loading spinner on oversized password (>1,000 chars).
* **BUG-02 (TC-11):** Rate limiter returns HTTP 200 OK status instead of standard HTTP 429 Too Many Requests.
