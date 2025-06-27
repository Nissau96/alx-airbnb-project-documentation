# 📝 Backend Requirements Specification – Airbnb Clone

## 📌 Objective

This document outlines the functional and technical requirements for key backend features of the Airbnb Clone system. The goal is to ensure clear and structured implementation aligned with API best practices, scalability, and performance.

---

## 1. 🔐 User Management

### Functional Requirements

- Users must be able to register as guests or hosts.
- Users must be able to log in using email/password or via OAuth providers (Google, Facebook).
- Users can update profile details such as name, email, phone number, and profile photo.
- Admins can manage users, deactivate accounts, or change roles.

### API Endpoints

- `POST /api/auth/register` – Register a new user
- `POST /api/auth/login` – Authenticate user login
- `POST /api/auth/oauth` – OAuth login via Google/Facebook
- `GET /api/users/me` – Get logged-in user's profile
- `PUT /api/users/me` – Update logged-in user's profile
- `GET /api/admin/users` – Admin fetches all users
- `PUT /api/admin/users/:id/role` – Admin updates user role

### Input/Output Specs

- **Registration Input:** `{ name, email, password, role }`
- **Login Input:** `{ email, password }`
- **Profile Update Input:** `{ name?, email?, phone?, profilePhoto? }`
- **Output (Common):** `{ id, name, email, role, token }`

### Validation Rules

- Email must be valid format and unique.
- Password must be at least 8 characters.
- Role must be one of: `guest`, `host`, `admin`.

### Performance Criteria

- Login response time < 300ms under typical load.
- Profile fetch/update operations must complete in < 500ms.
- Rate limiting in place: max 5 login attempts per IP per 10 minutes.

---

## 2. 🏠 Property Listings Management

### Functional Requirements

- Hosts can create, edit, and delete listings.
- Each listing includes title, location, price, amenities, photos, and availability.
- Guests can search and filter listings.

### API Endpoints

- `POST /api/properties` – Create new listing
- `GET /api/properties` – Get all listings with optional filters
- `GET /api/properties/:id` – Get listing details
- `PUT /api/properties/:id` – Update listing
- `DELETE /api/properties/:id` – Delete listing

### Input/Output Specs

- **Create/Update Input:** `{ title, description, price, location, amenities[], photos[], availability }`
- **Output:** `{ id, hostId, title, price, location, rating, createdAt }`

### Validation Rules

- Title and location must not be empty.
- Price must be a positive number.
- Photos must be image URLs or base64-encoded data.
- Only hosts can create/edit/delete listings.

### Performance Criteria

- Listings fetch with filters (pagination) must respond < 800ms.
- Property creation/editing should not exceed 1s latency.
- Search queries should support indexing for scalability.

---

## 3. 📅 Booking Management

### Functional Requirements

- Guests can book available properties.
- System checks for date conflicts before confirming a booking.
- Hosts can view bookings related to their properties.
- Users can cancel bookings within policy window.
- Each booking has statuses: `pending`, `confirmed`, `canceled`, `completed`.

### API Endpoints

- `POST /api/bookings` – Create new booking
- `GET /api/bookings/me` – Get user’s bookings
- `GET /api/bookings/property/:id` – Get bookings for a property (host only)
- `PUT /api/bookings/:id/cancel` – Cancel a booking
- `PUT /api/bookings/:id/status` – Admin/host updates booking status

### Input/Output Specs

- **Create Booking Input:** `{ propertyId, startDate, endDate, guests }`
- **Output:** `{ id, guestId, propertyId, status, totalPrice, startDate, endDate }`

### Validation Rules

- Booking dates must not overlap existing bookings.
- Start date must be in the future.
- Guests must be a positive integer.
- Only the guest who made the booking or an admin can cancel it.

### Performance Criteria

- Booking creation must validate and respond in < 700ms.
- Conflict checks must scale linearly with booking volume.
- API must handle 100 concurrent bookings per second without errors.
