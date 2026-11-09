# Travel BD — QA Test Reports

Target Application: `https://studio.unfaa.com`  
Platform: Travel Studio (Travel Agency ERP by UNFAA)  
Audit Date: 2026-11-09  

---

## 📁 Repository Files

* **[Travel_Bd_login.xlsx](./Travel_Bd_login.xlsx):** Authentication & Sign-in feature test report (19 Test Cases, 2 Defects).
* **[Travel_Bd_register.xlsx](./Travel_Bd_register.xlsx):** Organization Registration & Request Access feature test report (16 Test Cases, 4 Defects).

---

## 1. Feature: Organization Registration / Request Access (`Travel_Bd_register.xlsx`)

### 📊 Summary KPIs
| Total Tests | Passed | Failed | Pass Rate |
| :---: | :---: | :---: | :---: |
| **16** | **12** | **4** (TC-07, TC-08, TC-15, TC-16) | **75.0%** |

### 📋 Test Cases Execution Table
| Test ID | Category | Test Scenario / Objective | Test Input / Payload | Expected Result | Actual Result | Severity | Status |
| :---: | :--- | :--- | :--- | :--- | :--- | :---: | :---: |
| **TC-01** | System Config | Check initial registration config fetch | `GET /bff/tenant-requests/config` | Returns registration availability config and loads form | Returns HTTP 200 with registrationRequestEnabled: true. Skeleton loader resolves to registration form. | High | **PASS** |
| **TC-02** | Input Validation | Check submission with empty form fields | `Payload: {}` | Stops form submission and lists required field errors | HTTP 400 Bad Request with VALIDATION_ERROR for all required fields. | Medium | **PASS** |
| **TC-03** | Input Validation | Check Business Name minimum length (<2 chars) | `businessName: 'A'` | Rejects business name shorter than 2 characters | HTTP 400 Bad Request: 'Too small: expected string to have >=2 characters'. | Medium | **PASS** |
| **TC-04** | Input Validation | Check Business Name maximum length (>100 chars) | `businessName: 'A' * 105` | Rejects business name exceeding 100 characters | HTTP 400 Bad Request: 'Too big: expected string to have <=100 characters'. | Low | **PASS** |
| **TC-05** | Input Validation | Check Owner Name boundary length (2-100 chars) | `ownerName: 'B'` | Rejects owner name shorter than 2 characters | HTTP 400 Bad Request: 'Too small: expected string to have >=2 characters'. | Medium | **PASS** |
| **TC-06** | Input Validation | Check invalid email format (missing '@') | `email: 'invalid_email'` | Schema validator rejects invalid email format | HTTP 400 Bad Request: 'Invalid email address'. Submission prevented. | Medium | **PASS** |
| **TC-07** | Input Validation | Check Phone Number minimum length boundary | `phone: '123'` (< 5 digits) | Displays a user-friendly inline validation message explaining required length | Form fails to provide clear inline user guidance; server returns generic length error without formatted feedback. | Medium | **FAIL** |
| **TC-08** | Business Logic | Check Date of Birth with future date input | `dateOfBirth: '2035-12-31'` | Date picker should disable future dates and prevent selecting future birth dates on UI | not change date field year always select 2026. | High | **FAIL** |
| **TC-09** | Business Logic | Check Date of Birth with malformed date string | `dateOfBirth: 'not-a-date'` | Rejects malformed date format cleanly | HTTP 400 Bad Request. Handled cleanly without server crash. | Medium | **PASS** |
| **TC-10** | Input Validation | Check Address field length limits (5-300 chars) | `address: 'Hi'` (< 5 chars) | Rejects address shorter than 5 characters | HTTP 400 Bad Request: 'Too small: expected string to have >=5 characters'. | Medium | **PASS** |
| **TC-11** | Anti-Spam | Check duplicate registration request for same email | Re-submitting an already requested email address | Blocks duplicate request and alerts user that request is already open | HTTP 409 Conflict: 'A registration request for this email is already open'. Prevents duplicate spam. | High | **PASS** |
| **TC-12** | Security / CSRF | Check Cross-Origin POST request submission | `Origin: https://attacker.com` | Server rejects cross-site request from untrusted origin | HTTP 403 Forbidden: 'Cross-origin request rejected'. CSRF attack blocked. | Critical | **PASS** |
| **TC-13** | Rate Limiting | Check brute-force & spamming rate limit protection | 5+ rapid submissions within 60 seconds | Throttles excessive submissions and temporarily blocks requests | HTTP 429 Too Many Requests: Throttler triggers safely after 3-4 attempts. | High | **PASS** |
| **TC-14** | Security / XSS | Check script injection in URL 'plan' parameter | `Query: ?plan=<script>alert(1)</script>` | Parameter sanitized without script execution | Script tags blocked; safely parsed into plain JSON text. Page loads safely. | High | **PASS** |
| **TC-15** | Input Validation | Check Phone Number numeric validation (API bypass) | `phone: 'abcdefghij'` (non-numeric string) | Server should validate numeric/E.164 phone format and reject alphabet strings | Server accepts alphabetic phone string and returns HTTP 201 Created because backend schema lacks numeric regex validation. | Medium | **FAIL** |
| **TC-16** | HTTP Standards | Check standard Retry-After header on rate limit | HTTP 429 response headers inspection | HTTP 429 response should include Retry-After header indicating wait time in seconds | HTTP 429 status code is returned, but Retry-After header is missing from response headers. | Low | **FAIL** |

### 🐛 Defects & Bug Reports
* **BUG-01 (TC-07):** Phone number field lacks clear client-side minimum length guidance / validation feedback.
* **BUG-02 (TC-08):** Date of birth picker year does not change and always defaults to 2026.
* **BUG-03 (TC-15):** Missing server-side numeric validation on phone field (API accepts alphabetic strings and returns HTTP 201 Created).
* **BUG-04 (TC-16):** Rate limit response (HTTP 429) lacks standard Retry-After header.

---

## 2. Feature: User Sign-In / Authentication (`Travel_Bd_login.xlsx`)

### 📊 Summary KPIs
| Total Tests | Passed | Failed | Pass Rate |
| :---: | :---: | :---: | :---: |
| **19** | **17** | **2** (TC-09, TC-11) | **89.5%** |

### 🐛 Key Defects
* **BUG-01 (TC-09):** Sign-in button freezes in infinite loading spinner on oversized password (>1,000 chars).
* **BUG-02 (TC-11):** Rate limiter returns HTTP 200 OK status instead of standard HTTP 429 Too Many Requests.
