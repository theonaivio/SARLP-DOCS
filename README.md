Here’s the **updated, fully professional, MVP-ready README-style documentation** for SARLP, including **“Contact Agent” functionality** along with all prior user flows, API endpoints, and data models.

---

# 📘 **SARLP – Data Flow & API Documentation (MVP)**

**Version:** 1.1
**Maintained by:** SARLP Engineering Team
**Document Format:** README.md

---

# **📑 Table of Contents**

1. Overview
2. User Roles & Permissions
3. Data Flow Overview
4. Core Data Models
5. API Endpoints

   * Admin
   * Agent / Property Manager
   * Tenant / User
   * Landlord / Property Owner
6. Business Processes & Workflows
7. Contact Agent Flow
8. Notifications & Triggers
9. Security
10. Future Enhancements

---

# **1. Overview**

This document defines the **MVP-ready data flows and API endpoints** for SARLP.
It covers all major user roles:

* **Admin** – Full system control, KYC approval, revenue management.
* **Agent / Property Manager** – Manage multiple properties, tours, bookings, and tenant communication.
* **Tenant / User** – Browse, book, pay, submit maintenance requests, and contact agents.
* **Landlord / Property Owner** – List properties, receive payments, approve tenants, manage portfolios.

Designed for **backend engineers, mobile/web developers, QA, and product teams**, this document ensures clarity on system flows, endpoints, and responsibilities.

---

# **2. User Roles & Permissions**

| Role                         | Key Permissions                                                                                              |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------ |
| **Admin**                    | Full CRUD on users, properties, bookings; KYC verification; payment oversight; system reports                |
| **Agent / Property Manager** | Manage assigned properties; confirm tours; manage shortlet bookings; communicate with tenants & landlords    |
| **Tenant/User**              | Browse properties; request tours; book shortlets; make payments; submit maintenance requests; contact agents |
| **Landlord/Property Owner**  | List properties; approve tenants; view rental income; receive payments; manage disputes                      |

---

# **3. Data Flow Overview**

**General Flow:**

```
[Tenant / User]                      [Landlord / Owner]
       │                                     │
       ▼                                     ▼
Browse / Search Properties → Property Data
       │                                     │
       ▼                                     ▼
Request Tour / Booking / Payment → Booking Service
       │                                     │
       ▼                                     ▼
  Contact Agent / Messages → Agent Service → Notifications
       │
       ▼
   Payment Service (Paystack) → Admin / Agent Commission
       │
       ▼
Notifications Service → Email / SMS / Push
       │
       ▼
Dashboards (Role-Specific)
```

**Notes:**

* Admin monitors and approves KYC, listings, and payments.
* Agent facilitates tours, shortlet bookings, and tenant-landlord communication.
* Tenant initiates discovery, bookings, and contact requests.
* Property Owner manages listings, tenants, and receives payouts.

---

# **4. Core Data Models**

## **4.1 User Model**

```json
{
  "id": "UUID",
  "role": "tenant | landlord | agent | admin",
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
    "scuml_certificate": ""
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

## **4.2 Property Model**

```json
{
  "id": "UUID",
  "title": "",
  "description": "",
  "listing_type": "sale | rent | shortlet | land",
  "property_type": "duplex | apartment | bungalow | land | office",
  "price": 0,
  "shortlet_price_daily": 0,
  "currency": "NGN",
  "bedrooms": 0,
  "bathrooms": 0,
  "parking_space": 0,
  "location": {
    "address": "",
    "city": "",
    "state": "",
    "country": "",
    "latitude": 0,
    "longitude": 0,
    "google_place_id": ""
  },
  "media": {
    "images": [],
    "videos": [],
    "floor_plan": []
  },
  "amenities": [],
  "agent_id": "UUID",
  "owner_id": "UUID",
  "status": "pending | active | sold | rented | unavailable",
  "reviews": [],
  "created_at": "",
  "updated_at": ""
}
```

---

## **4.3 Booking / Shortlet Model**

```json
{
  "id": "UUID",
  "property_id": "UUID",
  "user_id": "UUID",
  "check_in": "YYYY-MM-DD",
  "check_out": "YYYY-MM-DD",
  "guest_count": 1,
  "amount": 0,
  "payment_reference": "",
  "payment_status": "pending | paid | failed",
  "booking_status": "awaiting_payment | booked | cancelled | completed",
  "created_at": "",
  "updated_at": ""
}
```

---

## **4.4 Tour Request Model**

```json
{
  "id": "UUID",
  "property_id": "UUID",
  "user_id": "UUID",
  "selected_times": [],
  "confirmed_time": null,
  "type": "physical | virtual",
  "status": "pending | confirmed | completed | cancelled",
  "message": "",
  "created_at": "",
  "updated_at": ""
}
```

---

## **4.5 Payment Model**

```json
{
  "id": "UUID",
  "booking_id": "UUID",
  "user_id": "UUID",
  "amount": 0,
  "currency": "NGN",
  "payment_gateway": "paystack",
  "reference": "",
  "status": "pending | success | failed",
  "callback_data": {},
  "created_at": "",
  "updated_at": ""
}
```

---

## **4.6 Contact Agent Model**

```json
{
  "id": "UUID",
  "property_id": "UUID",
  "tenant_name": "",
  "tenant_email": "",
  "tenant_phone": "",
  "message": "",
  "agent_id": "UUID",
  "status": "pending | responded",
  "created_at": "",
  "updated_at": ""
}
```

---

# **5. API Endpoints**

---

## **5.1 Admin Endpoints**

| Action           | Method | Endpoint                      | Description                |
| ---------------- | ------ | ----------------------------- | -------------------------- |
| Get All Users    | GET    | /admin/users                  | List all users by role     |
| Approve KYC      | PUT    | /admin/users/:id/approve      | Approve landlord/agent KYC |
| Reject KYC       | PUT    | /admin/users/:id/reject       | Reject KYC                 |
| Get Properties   | GET    | /admin/properties             | View all properties        |
| Approve Property | PUT    | /admin/properties/:id/approve | Approve listing            |
| Reject Property  | PUT    | /admin/properties/:id/reject  | Reject listing             |
| View Bookings    | GET    | /admin/bookings               | All bookings and status    |
| Manage Payments  | GET    | /admin/payments               | Transaction overview       |
| Notifications    | POST   | /admin/notifications          | Broadcast alerts           |

---

## **5.2 Agent / Property Manager Endpoints**

| Action             | Method | Endpoint                    | Description                 |
| ------------------ | ------ | --------------------------- | --------------------------- |
| List My Properties | GET    | /agent/properties           | Properties managed by agent |
| Add Property       | POST   | /agent/properties           | Add new property            |
| Update Property    | PUT    | /agent/properties/:id       | Update property info        |
| Confirm Tour       | PUT    | /agent/tours/:id/confirm    | Confirm tenant tour         |
| Manage Shortlet    | PUT    | /agent/shortlet/:id/confirm | Confirm booking             |
| View Bookings      | GET    | /agent/bookings             | Property bookings           |
| Contact Tenant     | POST   | /agent/messages             | Respond to tenant inquiries |

---

## **5.3 Tenant / User Endpoints**

| Action               | Method | Endpoint             | Description                  |
| -------------------- | ------ | -------------------- | ---------------------------- |
| Browse Properties    | GET    | /properties          | Public search                |
| Property Details     | GET    | /properties/:id      | View details                 |
| Favorite Property    | POST   | /user/favorites      | Save property                |
| Request Tour         | POST   | /tours/request       | Request property tour        |
| Book Shortlet        | POST   | /shortlet/book       | Initiate booking             |
| Make Payment         | POST   | /payments/initialize | Pay via Paystack             |
| Track Booking        | GET    | /user/bookings       | User-specific bookings       |
| Maintenance Requests | POST   | /user/maintenance    | Submit issue                 |
| Contact Agent        | POST   | /user/contact-agent  | Message agent about property |

---

## **5.4 Landlord / Property Owner Endpoints**

| Action             | Method   | Endpoint                   | Description                |
| ------------------ | -------- | -------------------------- | -------------------------- |
| Add Property       | POST     | /owner/properties          | Create new listing         |
| Update Property    | PUT      | /owner/properties/:id      | Edit listing               |
| View Bookings      | GET      | /owner/bookings            | View tenant bookings       |
| Track Income       | GET      | /owner/payments            | Rental revenue             |
| Approve Tenant     | PUT      | /owner/tenants/:id/approve | Approve rental application |
| Manage Maintenance | GET/POST | /owner/maintenance         | Track and respond          |

---

# **6. Business Processes & Workflows**

### **6.1 Admin Workflow**

1. User signs up → KYC pending (landlord/agent)
2. Admin reviews → approves/rejects
3. Admin monitors payments, bookings, disputes
4. Admin approves property listings

### **6.2 Agent Workflow**

1. Agent assigned properties → manages listings
2. Receives tour requests → confirms
3. Handles shortlet bookings → confirms payments
4. Responds to contact-agent messages
5. Coordinates tenants and landlords

### **6.3 Tenant/User Workflow**

1. Browse/search properties
2. Request tour → confirmed by agent
3. Book shortlet → payment via Paystack
4. Track booking and maintenance
5. Contact agent for property inquiries

### **6.4 Property Owner Workflow**

1. Add property → pending approval
2. View tenant applications → approve/reject
3. Receive payouts
4. Track maintenance & disputes

---

# **7. Contact Agent Flow**

1. Tenant clicks **“Contact Agent”** on property page
2. Fills out:

   * Name
   * Email
   * Phone
   * Message (e.g., *“I am interested in 1104 Chesterton Dr, Richardson, TX 75080.”*)
3. Form submits → creates `contact_agent` record
4. Agent receives notification
5. Agent responds via dashboard → updates status `pending | responded`
6. Tenant notified via email/push

---

# **8. Notifications & Triggers**

| Event             | Trigger            | Channels         |
| ----------------- | ------------------ | ---------------- |
| Booking confirmed | User/Agent action  | Email, SMS, Push |
| Payment success   | Paystack webhook   | Email, SMS       |
| Tour approved     | Agent/Owner action | Email, Push      |
| KYC approved      | Admin action       | Email            |
| Contact agent     | Tenant message     | Email, Push      |

---

# **9. Security**

* JWT authentication
* Role-based access control (RBAC)
* Rate limiting
* HTTPS-only communication
* Payment webhook verification
* File validation (images/docs only)

---

# **10. Future Enhancements**

* Wallet for agents/owners
* Subscription packages for agents
* In-app chat system
* AI-powered property recommendations
* Multi-city & multi-country support
