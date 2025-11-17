# **📘 SARLP – Property Platform (MVP)**

### **Property Process • API Documentation • Payments • User Flows**

**Version:** 1.0
**Maintained by:** SARLP Engineering Team
**Document Format:** README.md

---

# **📑 Table of Contents**

1. [Overview](#overview)
2. [Core Features](#core-features)
3. [System Architecture](#system-architecture)
4. [Data Models](#data-models)

   * Property
   * Tour Request
   * Shortlet Booking
   * Payment
5. [API Endpoints](#api-endpoints)

   * Property
   * Media
   * Search
   * Tours
   * Shortlet
   * Payments (Paystack)
6. [Business Processes & End-to-End Workflows](#business-processes)
7. [Payment Model Options](#payment-model-options)
8. [Admin Dashboard & Role-Based Access](#admin-dashboard)
9. [Google Maps & Calendar Integrations](#google-maps--calendar-sync)
10. [Notifications](#notifications)
11. [Security](#security)
12. [Future Enhancements](#future-enhancements)

---

# **1. Overview**

SARLP is a modern real estate platform enabling property listing, shortlet booking, payments, and automated tours for agents, admins, and customers.

This README provides:

* Core business flows
* API endpoint structures
* Data models
* Payment processes
* Role-Based Access definitions
* Google Maps & Calendar synchronization
* Media support (Images + Video URLs)

This is an MVP-ready document suitable for:

* Backend engineers
* Mobile & Web developers
* DevOps
* Product Managers
* QA testing teams

---

# **2. Core Features**

### **Property Listing**

* Sales, Rent, Lands, Shortlet
* Photos, Video URLs (YouTube / Vimeo / Google Drive)
* Amenities, Location (Google Maps)

### **Search & Discovery**

* Google-integrated location search
* Radius-based filtering
* Keyword + structured filters

### **Tour Scheduling**

* Users select 1–3 preferred time slots
* Agents confirm
* Auto-sync to Google/Apple Calendar

### **Shortlet Booking**

* Real-time calendar availability
* Booking lifecycle
* Payment (Paystack)

### **Payments**

* Paystack inline & redirect
* Webhooks
* Commission model for SARLP

### **Admin Dashboard**

* Approvals
* Agent verification
* Booking oversight
* Payment/revenue reporting

---

# **3. System Architecture**

```
Frontend (Web & Mobile)
     │
API Gateway / Load Balancer
     │
Backend Services (Node.js / Laravel / NestJS)
     ├── Authentication Service
     ├── Property Service
     ├── Booking Service
     ├── Media Service
     ├── Payment Service (Paystack)
     ├── Notification Service
     └── Admin Service
     │
Database (PostgreSQL/MySQL)
     │
External Integrations
     ├── Paystack Payments
     ├── Google Maps / Places
     ├── Google/Apple Calendar
     └── Cloud Storage (AWS S3 / GCP / DigitalOcean)
```

---

# **4. Data Models**

## **4.1. Property**

```json
{
  "id": "UUID",
  "title": "Luxury 4-Bed Duplex",
  "description": "4-bed detached duplex in a serene environment",
  "listing_type": "sale | rent | shortlet | land",
  "property_type": "duplex | apartment | bungalow | land | warehouse | office",
  "price": 2500000,
  "shortlet_price_daily": 70000,
  "currency": "NGN",
  "bedrooms": 4,
  "bathrooms": 3,
  "toilets": 4,
  "parking_space": 2,
  "square_feet": 2929,
  "location": {
    "address": "1104 Chesterton Dr, Richardson, TX",
    "city": "Richardson",
    "state": "Texas",
    "country": "USA",
    "latitude": 32.9690,
    "longitude": -96.7286,
    "google_place_id": "ChIJxxxx"
  },
  "media": {
    "images": [],
    "videos": [
      {
        "type": "youtube | vimeo | drive | raw",
        "url": "https://youtube.com/xxx"
      }
    ],
    "floor_plan": []
  },
  "amenities": [],
  "agent_id": "UUID",
  "status": "pending | active | sold | rented | unavailable",
  "reviews": [],
  "created_at": "",
  "updated_at": ""
}
```

---

## **4.2 Tour Request**

```json
{
  "id": "UUID",
  "property_id": "UUID",
  "user_id": "UUID",
  "selected_times": ["2025-04-11T11:00:00Z"],
  "preferred_time": "2025-04-11T11:00:00Z",
  "type": "physical | virtual",
  "status": "pending | confirmed | completed | cancelled",
  "message": "",
  "created_at": ""
}
```

---

## **4.3 Shortlet Booking**

```json
{
  "id": "UUID",
  "property_id": "UUID",
  "user_id": "UUID",
  "check_in": "2025-06-20",
  "check_out": "2025-06-24",
  "guest_count": 2,
  "amount": 280000,
  "payment_reference": "",
  "payment_status": "pending | paid | failed",
  "booking_status": "awaiting_payment | booked | cancelled | completed",
  "created_at": ""
}
```

---

## **4.4 Payment (Paystack)**

```json
{
  "id": "UUID",
  "booking_id": "UUID",
  "user_id": "UUID",
  "amount": 280000,
  "currency": "NGN",
  "payment_gateway": "paystack",
  "reference": "psk_ref_892934",
  "status": "pending | success | failed",
  "callback_data": {},
  "created_at": "",
  "updated_at": ""
}
```

---

# **5. API Endpoints**

---

## **5.1 Property Endpoints**

### **Create Property**

`POST /api/properties`

### **Update Property**

`PUT /api/properties/:id`

### **Get All Properties**

`GET /api/properties`

### **Get Property by ID**

`GET /api/properties/:id`

---

## **5.2 Media Upload**

### **Upload Images**

`POST /api/properties/:id/media/images`

### **Add Video URL**

`POST /api/properties/:id/media/video`

Accepted:

* YouTube
* Vimeo
* Google Drive
* Direct file URLs

---

## **5.3 Search Endpoints**

`GET /api/properties/search`

### **Supported Query Params**

| Key             | Description              |
| --------------- | ------------------------ |
| q               | Full text search         |
| city            | Filter                   |
| state           | Filter                   |
| price_min       | Numeric                  |
| price_max       | Numeric                  |
| lat/lng         | Map search               |
| radius          | Distance (meters)        |
| google_place_id | If user searches via map |

---

## **5.4 Tour Endpoints**

### **Submit Tour Request**

`POST /api/tours/request`

### **Confirm Tour (Agent/Admin)**

`PUT /api/tours/:id/confirm`

---

## **5.5 Shortlet Booking Endpoints**

### **Check Availability**

`GET /api/shortlet/availability`

### **Create Booking**

`POST /api/shortlet/book`

### **Confirm Booking**

`PUT /api/shortlet/:id/confirm`

---

## **5.6 Payment (Paystack)**

### **Initialize Payment**

`POST /api/payments/initialize`

### **Verify Payment**

`GET /api/payments/verify/:reference`

### **Webhook**

`POST /api/payments/callback`

---

# **6. Business Processes**

---

## **6.1 Property Listing Flow**

1. Agent submits property
2. Uploads images & video URLs
3. Admin verifies
4. Property goes live
5. Users search → view → book tour or shortlet

---

## **6.2 Tour Request Flow**

1. User selects 1–3 preferred times
2. Agent/Admin reviews request
3. Confirms a slot
4. Notification sent
5. Tour completed → status updated
6. Calendar sync (optional)

---

## **6.3 Shortlet Booking Flow**

1. User checks availability
2. Creates booking
3. System initializes Paystack payment
4. User pays → redirect
5. Paystack webhook confirms
6. Booking status → **BOOKED**
7. Admin/Agent prepares for check-in

---

# **7. Payment Model Options**

### ✔ **Option A – Pay Per Booking (Recommended)**

* SARLP charges: **5–10% service fee**
* Agent gets payout from booking amount

### ✔ **Option B – Escrow Holding**

* SARLP holds funds until guest checkout

### ✔ **Option C – Automated Payouts to Agents**

* Paystack Transfers API
* Auto-settlement to bank account

---

# **8. Admin Dashboard & Role-Based Access**

| Role      | Permissions                                         |
| --------- | --------------------------------------------------- |
| **Admin** | Full system access, approvals, revenue reports      |
| **Agent** | Manage own listings, confirm tours, handle bookings |
| **User**  | Search, tour requests, bookings, payments           |

---

# **9. Google Maps & Calendar Sync**

### **Maps Integration**

* Autocomplete
* Places search
* Reverse geocoding
* Radius filtering for search

### **Calendar Sync**

Triggered when:

* Tour confirmed
* Shortlet booked

Supported:

* Google Calendar
* Apple iCalendar (.ics)

---

# **10. Notifications**

| Event             | Email | SMS      | Push |
| ----------------- | ----- | -------- | ---- |
| Tour Request      | ✔     | Optional | ✔    |
| Tour Confirmed    | ✔     | ✔        | ✔    |
| Payment Success   | ✔     | ✔        | ✔    |
| Booking Confirmed | ✔     | ✔        | ✔    |

---

# **11. Security**

* JWT authentication
* Role-based access
* Request rate limiting
* Paystack webhook signature verification
* Media URL validation (YouTube/Vimeo/GDrive only)
* HTTPS-only communication

---

# **12. Future Enhancements**

* Wallet system
* Agent subscription packages
* In-app chat between agents & buyers
* AI-powered property recommendations
* Property inspection automation
* Multi-city expansion
