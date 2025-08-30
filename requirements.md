# Backend Requirement Specifications

This document outlines the technical and functional requirements for four core backend features of the Airbnb Clone project: **User Authentication & Authorization**, **Property Management**, **Booking System**, and **Reviews & Ratings**.

---

## 1. User Authentication & Authorization

### Functional Requirements
- Users (Guests, Hosts, Admins) must be able to register, log in, and manage their profiles.
- The system must support **role-based access control (RBAC)**:
  - Guests: Can search, book, and review properties.
  - Hosts: Can list and manage properties, and respond to reviews.
  - Admins: Can manage users, listings, and bookings.
- Authentication should be implemented using **JWT tokens**.
- Passwords must be stored securely (hashed + salted).

### API Endpoints

| Method | Endpoint                 | Description                         | Auth Required | Role  |
|--------|---------------------------|-------------------------------------|---------------|-------|
| POST   | /api/v1/auth/register     | Register a new user                 | No            | Any   |
| POST   | /api/v1/auth/login        | Log in and get a JWT token          | No            | Any   |
| GET    | /api/v1/auth/profile      | Retrieve authenticated user profile | Yes           | Any   |

### Validation Rules
- Email must be **unique** and in a valid format.
- Password must be at least **8 characters**, include a **number** and a **special character**.
- Username must be **unique**.
- Role must be one of: `guest`, `host`, `admin`.

### Security & Performance
- Passwords hashed with **bcrypt** (configurable work factor).
- JWT signed with strong secret or asymmetric keys; expiration: **24h**; support refresh tokens.
- Authentication endpoints should respond within **200ms** under typical load.

---

## 2. Property Management

### Functional Requirements
- Hosts can add, update, and delete property listings.
- Guests can search, filter, and view property details.
- Admins can remove inappropriate listings.

### API Endpoints

| Method | Endpoint                   | Description                          | Auth Required | Role         |
|--------|-----------------------------|--------------------------------------|---------------|--------------|
| POST   | /api/v1/properties          | Add a new property listing           | Yes           | Host         |
| PUT    | /api/v1/properties/{id}     | Update an existing property listing  | Yes           | Host         |
| DELETE | /api/v1/properties/{id}     | Delete a property                    | Yes           | Host/Admin   |
| GET    | /api/v1/properties          | Retrieve all available properties    | No            | Any          |
| GET    | /api/v1/properties/{id}     | Retrieve details of a property       | No            | Any          |

### Validation Rules
- Title required (max 100 chars).
- Description required.
- Price must be a positive number.
- Location required.
- At least one image required.

### Performance Criteria
- Search and filter queries should return results within **500ms**.
- Support pagination for property listings.

---

## 3. Booking System

### Functional Requirements
- Guests can book and cancel bookings.
- Hosts can view bookings for their properties.
- Admins can manage all bookings.

### API Endpoints

| Method | Endpoint                          | Description                         | Auth Required | Role       |
|--------|------------------------------------|-------------------------------------|---------------|------------|
| POST   | /api/v1/bookings                   | Create a new booking                | Yes           | Guest      |
| GET    | /api/v1/bookings/{id}              | Retrieve booking details            | Yes           | Guest/Host |
| DELETE | /api/v1/bookings/{id}              | Cancel a booking                    | Yes           | Guest/Admin|
| GET    | /api/v1/bookings/user/{userId}     | Retrieve bookings for a user        | Yes           | Guest      |

### Validation Rules
- `propertyId` must exist.
- `checkIn` < `checkOut`.
- Dates must be in valid format.
- Guests must be a positive integer.
- Payment method must be supported.

### Performance Criteria
- Booking creation and cancellation must complete within **500ms**.
- Concurrent booking conflicts must be prevented (atomic transactions).

---

## 4. Reviews & Ratings

### Functional Requirements
- Guests can leave **reviews and ratings** for properties they have booked.
- Hosts can respond to reviews for their properties.
- Admins can moderate or delete inappropriate reviews.

### API Endpoints

| Method | Endpoint                       | Description                       | Auth Required | Role       |
|--------|---------------------------------|-----------------------------------|---------------|------------|
| POST   | /api/v1/reviews                 | Create a review for a property    | Yes           | Guest      |
| GET    | /api/v1/reviews/property/{id}   | Get all reviews for a property    | No            | Any        |
| PUT    | /api/v1/reviews/{id}/response   | Respond to a review               | Yes           | Host       |
| DELETE | /api/v1/reviews/{id}            | Delete a review                   | Yes           | Admin      |

### Validation Rules
- Review must be linked to an existing booking.
- Rating must be an integer between 1 and 5.
- Review text required, max 500 characters.
- Guests can only review properties they have booked.

### Performance Criteria
- Creating a review should complete within **200ms**.
- Listing reviews should be paginated and return within **300ms**.

---

## Notes
- All APIs use standard HTTP status codes: `200`, `201`, `400`, `401`, `404`, `500`.
- Input/output formats: **JSON**.
- JWT required for protected endpoints.
- RBAC ensures:
  - Guests: Can book and review.
  - Hosts: Can manage properties and respond to reviews.
  - Admins: Can manage all resources.
