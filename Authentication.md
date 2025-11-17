# 📘 **SARLP – Onboarding & Authentication Flow (README Documentation)**

**Version:** 1.0
**Module:** User Authentication & Role Onboarding
**Audience:** Backend, Mobile, Web, Product Teams

---

# **📑 Table of Contents**

1. Overview
2. Public Access (Discovery Mode)
3. Authentication Trigger Logic
4. Signup & Login Flow
5. Role Selection
6. Role-Based Required Fields
7. Verification Flow (OTP + Email + KYC)
8. Onboarding Emails
9. Role Activation Rules
10. Post-Login Dashboards
11. Session Management
12. Logout & Re-entry
13. Data Models
14. API Endpoints

---

# **1. Overview**

SARLP provides a modern, flexible onboarding experience designed for three primary roles:

* Tenants
* Landlords / Property Owners
* Agents / Property Managers

The onboarding supports:

✔ Email & Password Sign Up
✔ Google Sign-In
✔ OTP Phone Verification
✔ Role-Based Document Upload
✔ Admin Verification
✔ Role-Specific Dashboard Access

---

# **2. Public Access (Discovery Mode)**

Before login, the user can:

* Browse all public properties
* View property details, images, videos
* Explore reviews, amenities, pricing
* View landlord/agent ratings
* Read FAQs, support articles
* Explore company information

**No authentication is required.**
This is intentional to encourage exploration and reduce entry barriers.

---

# **3. Authentication Trigger Logic**

The system requires login when a user attempts:

| Action                    | Login Required |
| ------------------------- | -------------- |
| Save/Favorite Property    | ✔              |
| Start a booking           | ✔              |
| Make payments             | ✔              |
| Contact agent/landlord    | ✔              |
| Submit application        | ✔              |
| Raise maintenance request | ✔              |
| Access dashboards         | ✔              |
| Generate receipts/reports | ✔              |

When triggered → **Auth Modal opens (Login/Signup).**

---

# **4. Signup & Login Flow**

### **Signup Options**

1. **Email + Password**
2. **Google Sign-In (OAuth 2.0)**

### **Signup Steps**

1. User selects **Continue with Email** or **Continue with Google**
2. User completes **Basic Info + Role Selection**
3. System sends **OTP to phone**
4. User enters OTP → verification success
5. System creates user profile
6. System sends:

   * **Welcome Email**
   * **KYC Document Upload Reminder Email**

User is logged in immediately after OTP verification.

---

# **5. Role Selection**

During signup, user selects **one role**:

* **Tenant**
* **Landlord / Property Owner**
* **Agent / Property Manager**

This determines:

* Dashboard layout
* Required onboarding documents
* Permissions
* Required fields

---

# **6. Role-Based Required Fields**

## **6.1 Tenant (Minimum friction)**

| Field              | Type     | Required |
| ------------------ | -------- | -------- |
| Full Name          | Text     | ✔        |
| Email              | Text     | ✔        |
| Phone Number       | Text     | ✔        |
| Password           | Password | ✔        |
| Preferred Location | Dropdown | Optional |

---

## **6.2 Landlord / Property Owner**

| Field            | Type     | Required |
| ---------------- | -------- | -------- |
| Full Name        | Text     | ✔        |
| Email            | Text     | ✔        |
| Phone Number     | Text     | ✔        |
| Password         | Password | ✔        |
| ID Upload        | File     | ✔        |
| Proof of Address | File     | ✔        |
| Bank Details     | Text     | ✔        |
| Company Name     | Text     | Optional |

---

## **6.3 Agent / Property Manager**

| Field             | Type     | Required |
| ----------------- | -------- | -------- |
| Full Name         | Text     | ✔        |
| Email             | Text     | ✔        |
| Phone Number      | Text     | ✔        |
| Password          | Password | ✔        |
| Valid ID          | File     | ✔        |
| Proof of Address  | File     | ✔        |
| CAC Documents     | File     | Optional |
| SCUML Certificate | File     | Optional |

---

# **7. Verification Flow**

### **Step 1: OTP Verification**

Sent to user’s phone:

* 6-digit OTP
* Valid for 10 minutes

### **Step 2: Email Verification**

Welcome email AND "Upload your documents" email are sent.

### **Step 3: Admin KYC Approval**

* Tenant → auto-approved
* Landlord → requires admin approval
* Agent → requires admin approval

After approval → dashboard is unlocked.

---

# **8. Onboarding Emails**

### **1. Welcome Email**

Sent immediately after account creation.

### **2. Document Upload Reminder Email**

Sent when role = landlord or agent.
Includes a secure link to upload documents.

### **3. Verification Complete Email**

Sent after admin approves KYC.

---

# **9. Role Activation Rules**

| Role     | Activation                  |
| -------- | --------------------------- |
| Tenant   | Immediate (no KYC review)   |
| Landlord | Requires admin KYC approval |
| Agent    | Requires admin KYC approval |

Users can still browse the platform while awaiting approval but **cannot:**

* Post listings
* Receive booking payments
* Manage tenants
* Create contracts
* Access financial dashboards

---

# **10. Post-Login Dashboards**

### **Tenant Dashboard**

* Saved properties
* Rental applications
* Payment & receipts
* Maintenance requests
* Agreements

### **Landlord Dashboard**

* Add/Edit properties
* Rental income tracking
* Tenant list
* Tax/expense reports
* Dispute/maintenance management

### **Agent Dashboard**

* Manage multiple properties
* Occupancy analytics
* Maintenance coordination
* Communication panel

---

# **11. Session Management**

* JWT Authentication
* Refresh token strategy
* Session persists until:

  * logout
  * token expiry
  * security event (password change)

---

# **12. Logout & Re-entry**

After logout:

* User returns to **Public Access Mode**
* Can browse properties freely
* Any restricted action → triggers Auth Modal

---

# **13. Data Models**

## **User Model**

```json
{
  "id": "UUID",
  "role": "tenant | landlord | agent",
  "full_name": "",
  "email": "",
  "phone": "",
  "password_hash": "",
  "google_auth": false,
  "email_verified": false,
  "phone_verified": false,
  "kyc_status": "not_uploaded | pending | approved | rejected",
  "documents": {
    "id_upload": "",
    "proof_of_address": "",
    "cac_document": "",
    "scuml": ""
  },
  "bank_details": {
    "bank_name": "",
    "account_number": "",
    "account_name": ""
  },
  "created_at": "",
  "updated_at": ""
}
```

---

## **OTP Model**

```json
{
  "id": "UUID",
  "user_id": "UUID",
  "otp": "123456",
  "expires_at": "timestamp",
  "verified": false
}
```

---

# **14. API Endpoints**

---

## **Signup / Login**

### **POST /auth/signup**

Create new user.

### **POST /auth/google**

Google OAuth signup.

### **POST /auth/login**

Login with email + password.

---

## **OTP Verification**

### **POST /auth/otp/send**

Send OTP.

### **POST /auth/otp/verify**

Verify OTP and activate account.

---

## **Documents Upload**

### **POST /auth/documents/upload**

Landlord/Agent uploads KYC documents.

---

## **Email Verification**

### **GET /auth/email/verify/:token**

---

## **KYC Admin Review**

### **GET /admin/users/pending-kyc**

Landlords/agents pending approval.

### **PUT /admin/user/:id/approve-kyc**

Approve.

### **PUT /admin/user/:id/reject-kyc**

Reject.

---

## **User Dashboard**

### **GET /dashboard**

Returns role-based dashboard.

---

# ✅ **DONE — FULL ONBOARDING FLOW COMPLETE**
