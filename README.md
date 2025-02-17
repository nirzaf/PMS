Below is a simple System Requirement Specification (SRS) document for a Property Booking Management System. This document outlines the key objectives, functionalities (both for public users and admins), and a proposed database design with table structures and column details. The design has been structured to store property details, pricing (with history), calendar/booking data, property availability, user information, and payment status. In addition, every activity (whether by a public user or an admin) will be recorded in an activity log.

---

# System Requirement Specification (SRS)

## 1. Introduction

**Purpose:**  
This document specifies the requirements for a Property Booking Management System. The system is designed to manage property listings, pricing (including price change history), booking calendars, property availability, user registrations, and payment statuses. It provides distinct functionalities for public users (booking, canceling, registering) and administrators (managing properties, bookings, and users) while logging all activities.

**Scope:**  
The system will be used by property owners, potential renters, and administrators. Public users can browse and book properties, cancel bookings, and register accounts. Administrators can add, update, and remove properties, manage booking details, and control user data. The system’s backend includes a relational database designed to efficiently store and retrieve all relevant information.

---

## 2. Overall System Description

The Property Booking Management System is a web-based application that supports:
- **Public User Interface:** Allows users to register, view property listings, book properties, and cancel bookings.
- **Admin Interface:** Enables administrators to manage property data, booking records, and user accounts.
- **Activity Logging:** Every operation—whether initiated by a user or an administrator—is recorded for audit and tracking purposes.

---

## 3. Functional Requirements

### 3.1 Public User Functionalities
- **User Registration:**  
  - Users can create accounts by providing essential details (name, email, password, etc.).
- **Property Booking:**  
  - Users can search for available properties, view pricing, and make bookings.
  - Booking details include start and end dates.
- **Booking Cancellation:**  
  - Users can cancel existing bookings.
- **Payment Processing:**  
  - Users can view payment status and complete transactions (integrated with the payment module).

### 3.2 Admin Functionalities
- **Property Management:**  
  - Add, update, and delete property records.
- **Booking Management:**  
  - View, update, and cancel booking details.
- **User Management:**  
  - Manage user accounts, including updating or deleting user details.
- **Activity Monitoring:**  
  - Review activity logs to track all operations performed within the system.

---

## 4. Database Design

The backend database uses a relational model. Below are the proposed tables with key columns:

### 4.1 Properties Table
- **PropertyID** (PK, INT, Auto-Increment)
- **Name** (VARCHAR(255), Required)
- **Description** (TEXT)
- **Address** (VARCHAR(255))
- **Other Attributes** (e.g., PropertyType, Capacity as needed)

### 4.2 Property Pricing & Price Change History

#### Option A: Combined History Approach  
- **PropertyPrice Table**
  - **PriceID** (PK, INT, Auto-Increment)
  - **PropertyID** (FK, INT)
  - **Price** (DECIMAL(10,2))
  - **EffectiveDate** (DATE)
  - **EndDate** (DATE, Nullable)
  - *(Each row represents a pricing period; new rows create the history.)*

#### Option B: Separate Price Change History Table  
- **PriceChangeHistory Table**
  - **HistoryID** (PK, INT, Auto-Increment)
  - **PropertyID** (FK, INT)
  - **OldPrice** (DECIMAL(10,2))
  - **NewPrice** (DECIMAL(10,2))
  - **ChangeDate** (DATE)
  - **ChangedBy** (UserID, INT – reference to the admin making the change)

### 4.3 Booking Table (Calendar Details)
- **BookingID** (PK, INT, Auto-Increment)
- **PropertyID** (FK, INT)
- **UserID** (FK, INT)
- **StartDate** (DATE)
- **EndDate** (DATE)
- **BookingStatus** (VARCHAR(50), e.g., "Booked", "Cancelled")
- **CreatedAt** (TIMESTAMP)
- **UpdatedAt** (TIMESTAMP)

### 4.4 Property Availability
*(Availability can be inferred from booking data; if a separate table is needed:)*
- **AvailabilityID** (PK, INT, Auto-Increment)
- **PropertyID** (FK, INT)
- **AvailableFrom** (DATE)
- **AvailableTo** (DATE)
- **IsAvailable** (BOOLEAN)

### 4.5 Users Table
- **UserID** (PK, INT, Auto-Increment)
- **Username** (VARCHAR(100), Unique)
- **PasswordHash** (VARCHAR(255))
- **Email** (VARCHAR(255), Unique)
- **FullName** (VARCHAR(255))
- **Phone** (VARCHAR(20))
- **UserType** (VARCHAR(50), e.g., "Public", "Admin")
- **CreatedAt** (TIMESTAMP)

### 4.6 Payment Status Table
- **PaymentID** (PK, INT, Auto-Increment)
- **BookingID** (FK, INT)
- **PaymentDate** (DATE)
- **Amount** (DECIMAL(10,2))
- **PaymentStatus** (VARCHAR(50), e.g., "Paid", "Pending", "Refunded")
- **PaymentMethod** (VARCHAR(50), e.g., "Credit Card", "PayPal")

### 4.7 Activity Log Table
- **LogID** (PK, INT, Auto-Increment)
- **UserID** (FK, INT, Nullable if system-generated)
- **Action** (VARCHAR(255)) – e.g., "User Registered", "Booking Created", "Property Updated"
- **Timestamp** (TIMESTAMP)
- **Details** (TEXT or JSON for additional context)

---

## 5. Activity Recording

Every action—whether initiated by a public user (e.g., booking or cancellation) or an administrator (e.g., property or user management)—must generate an entry in the **Activity Log Table**. This ensures full traceability and supports auditing.

---

## 6. Non-Functional Requirements

- **Performance:** The system must support concurrent access by multiple users and provide fast query responses (e.g., booking searches, availability checks).
- **Security:** User data (especially passwords and payment information) must be stored securely (e.g., hashed passwords, encrypted sensitive data). Role-based access control will restrict admin functionalities.
- **Scalability:** The system should be able to scale to support an increasing number of properties, bookings, and users.
- **Usability:** The user interface should be intuitive and mobile-friendly for both public users and administrators.
- **Auditability:** All system activities must be logged, and logs should be queryable for audit purposes.

---

## 7. Assumptions and Constraints

- The system assumes a relational database management system (e.g., MySQL, PostgreSQL, SQL Server).
- All dates and timestamps will follow a consistent format (e.g., ISO 8601).
- Payment integrations (e.g., third-party payment gateways) are handled externally and interfaced via APIs.
- The availability of a property is dynamically determined by comparing the booking table with any specified availability schedule.

---

## 8. Appendix

### Sample Activity Log Entry (JSON Representation)
```json
{
  "LogID": 101,
  "UserID": 5,
  "Action": "Booking Created",
  "Timestamp": "2025-03-10T14:23:00Z",
  "Details": {
    "BookingID": 201,
    "PropertyID": 35,
    "StartDate": "2025-04-01",
    "EndDate": "2025-04-07"
  }
}
```

