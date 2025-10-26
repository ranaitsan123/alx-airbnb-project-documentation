# Airbnb Clone Backend — Requirement Specifications

This document specifies the technical and functional requirements for the Airbnb Clone backend.  
It covers **User Authentication**, **Property Management**, and **Booking System** features.

---

## 1. User Authentication

### **Overview**
Users (Guests and Hosts) can register, log in, and manage their profiles securely.

### **API Endpoints**

| Endpoint           | Method | Description                    | Input                                         | Output                        |
|-------------------|--------|--------------------------------|-----------------------------------------------|-------------------------------|
| /api/auth/register | POST   | Register new user              | `{name, email, password, role}`              | `{id, name, email, role, token}` |
| /api/auth/login    | POST   | Login user                     | `{email, password}`                           | `{token, user}`               |
| /api/auth/logout   | POST   | Logout user                    | `Authorization: Bearer <token>`              | `{message: "Logged out"}`    |
| /api/users/:id     | GET    | Get user profile               | `Authorization: Bearer <token>`              | `{id, name, email, role, profilePic}` |
| /api/users/:id     | PUT    | Update user profile            | `Authorization: Bearer <token>` + `{name, profilePic}` | `{id, name, email, role, profilePic}` |

### **Validation Rules**
- Email must be unique and valid.
- Password: minimum 8 characters.
- Role must be either `"guest"` or `"host"`.

### **Security**
- Passwords hashed using bcrypt.
- JWT for authentication.
- Role-based access control (RBAC).

---

## 2. Property Management

### **Overview**
Hosts can create, update, view, and delete property listings.

### **API Endpoints**

| Endpoint             | Method | Description                     | Input                                                   | Output                                   |
|---------------------|--------|---------------------------------|---------------------------------------------------------|-----------------------------------------|
| /api/properties      | POST   | Create a new property           | `{title, description, location, price, amenities, availability}` | `{id, title, location, price, status}` |
| /api/properties      | GET    | Get all properties              | Optional query params: `location, price, guests, amenities` | `[ {id, title, location, price, status} ]` |
| /api/properties/:id  | GET    | Get property details            | Property ID                                             | `{id, title, description, location, price, amenities, availability, hostId}` |
| /api/properties/:id  | PUT    | Update property                | Property ID + `{title, description, price, availability}` | Updated property object                  |
| /api/properties/:id  | DELETE | Delete property                | Property ID                                             | `{message: "Property deleted"}`         |

### **Validation Rules**
- Price: must be a positive number.
- Availability: must be a valid date range.
- Title and description cannot be empty.

### **Notes**
- Only the host who created the property can update/delete it.
- Images stored in cloud storage (e.g., AWS S3).

---

## 3. Booking System

### **Overview**
Guests can book properties for specific dates. Hosts and guests can track booking statuses.

### **API Endpoints**

| Endpoint             | Method | Description                     | Input                                                   | Output                                   |
|---------------------|--------|---------------------------------|---------------------------------------------------------|-----------------------------------------|
| /api/bookings        | POST   | Create a new booking            | `{propertyId, guestId, startDate, endDate, paymentInfo}` | `{id, propertyId, guestId, startDate, endDate, status}` |
| /api/bookings/:id    | GET    | Get booking details             | Booking ID                                             | `{id, propertyId, guestId, startDate, endDate, status}` |
| /api/bookings/:id    | PUT    | Update booking (cancel/modify) | Booking ID + `{status, startDate, endDate}`           | Updated booking object                   |
| /api/bookings        | GET    | Get all bookings (for host/guest)| `userId` (query param)                                 | `[ {id, propertyId, guestId, startDate, endDate, status} ]` |

### **Validation Rules**
- Dates must be valid and not overlap with existing bookings.
- Payment must be confirmed before booking is marked "Confirmed".
- Status can be: Pending, Confirmed, Canceled, Completed.

### **Notifications**
- Send email/in-app notifications for booking creation, confirmation, or cancellation.

---

## 4. General Requirements

- **Database:** PostgreSQL (tables: Users, Properties, Bookings, Reviews, Payments)
- **Authentication:** JWT
- **API:** RESTful endpoints with proper HTTP status codes
- **Security:** Encrypt sensitive info (passwords, payment data)
- **Performance:** Pagination for lists, caching for frequent queries
- **Testing:** Unit and integration tests for all endpoints

---

**End of Document**
