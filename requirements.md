# 📘 Airbnb Clone - Backend Requirement Specifications

This document outlines the technical and functional requirements for three key backend features of the Airbnb Clone project.

---

## 🔐 1. User Authentication

### ✅ Description
Allows users to register, log in, and access system features based on their roles (guest, host, admin).

### 📌 Endpoints

- `POST /api/register`  
  Registers a new user.

- `POST /api/login`  
  Authenticates a user and returns a token/session.

### 🧾 Input Specifications

#### `POST /api/register`
```json
{
  "first_name": "Alice",
  "last_name": "Smith",
  "email": "alice@example.com",
  "password": "securepass123",
  "role": "guest"
}
```
## 🔐 User Authentication

### 📌 POST `/api/login`

#### 📥 Input (JSON)
```json
{
  "email": "alice@example.com",
  "password": "securepass123"
}
```
#### 📥 Output (JSON)
-200 OK on successful login:
```json
{
  "token": "jwt-token-string",
  "user": {
    "user_id": "uuid",
    "role": "guest"
  }
}
```
- 400 / 401 for invalid credentials or duplicate registration.

### 🔎 Validation Rules
- Valid email format.
- Password must be at least 8 characters.
- Role must be one of: guest, host, admin.

### ⚙️ Performance Criteria
- Response time < 300ms for authentication actions.
- Passwords stored securely using bcrypt or similar hashing algorithms.

## 🏠 2. Property Management
### ✅ Description
Hosts can create, view, update, and delete their property listings.

### 📌 Endpoints
- `POST /api/properties`
- `GET /api/properties/:id`
- `PUT /api/properties/:id`
- `DELETE /api/properties/:id`
- `GET /api/properties?location=NYC`

### 🧾 Input Specifications
#### `POST /api/properties`
```json
{
  "name": "Beachfront Villa",
  "description": "Beautiful villa by the sea.",
  "location": "Miami, FL",
  "pricepernight": 250.00
}
```
### 📥 Output (JSON)
- 201 Created with property ID.
- 403 Forbidden if unauthorized (e.g., guest trying to create property).

 ### 🔎 Validation Rules
- `pricepernight` must be positive.
- `name`, `description`, and `location` must be non-empty.
- Only users with host role can create/edit/delete properties.

### ⚙️ Performance Criteria
- Indexed queries for location-based property searches.
- Support for paginated listing using `limit` and `offset`.

## 📅 Booking System

### ✅ Description
Guests can book available properties for specific dates and pay for the booking.

---

### 📌 Endpoints

- `POST /api/bookings`
- `GET /api/bookings/:id`
- `GET /api/users/:id/bookings`

---

### 🧾 Input Specifications

#### POST `/api/bookings`

```json
{
  "property_id": "uuid",
  "start_date": "2025-07-10",
  "end_date": "2025-07-12"
}
```
### 📤 Output

**201 Created**
```json
{
  "booking_id": "uuid",
  "total_price": 500.00,
  "status": "pending"
}
```
### ❗ 409 Conflict

Returned if the booking dates overlap with an existing reservation.

---

### 🔎 Validation Rules

- Dates must be in the future.
- `end_date` must be after `start_date`.
- Total price is calculated as:  
  `(end_date - start_date) * pricepernight`

---

### ⚙️ Performance Criteria

- Date conflict checks optimized via indexing.
- API response time < **500ms** for booking creation.

---

### ✅ Summary

| Feature              | Endpoint Example              | Role Required | Conflict Handling      |
|----------------------|-------------------------------|----------------|------------------------|
| User Authentication  | `/api/login`, `/api/register` | None (public)  | Duplicate email        |
| Property Management  | `/api/properties`             | Host           | Authorization required |
| Booking System       | `/api/bookings`               | Guest          | Date overlap           |
