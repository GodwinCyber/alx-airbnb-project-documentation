# Backend Requirement Specifications
__1. User Authentication & Management__
Functional Requirements

__Register new users.__
- Retrieve, update, and delete user profiles.
- Authenticate users via DRF’s built-in authentication (TokenAuth / SessionAuth).

__API Endpoints__
- POST /users/ → Register user
- GET /users/{user_id}/ → Retrieve user profile
- PUT /users/{user_id}/ → Update user profile
- DELETE /users/{user_id}/ → Delete user
- POST /users/login/ → Authenticate and return JWT
- POST /users/logout/ → Invalidate JWT

__Input/Output Specs__
Register Request:
```json
  {
    "email": "user@example.com",
    "password": "SecurePass123",
    "name": "Jane Doe"
  }
```
Register Response:
```json
  {
    "id": 1,
    "username": "jane_doe",
    "email": "jane@example.com"
  }
```
Login Request:
```json
  {
    "username": "jane_doe",
    "password": "SecurePass123"
  }
```
Login Response (Token Auth):
```json
  {
    "token": "d34f0a4b12e..."
  }
```
__Validation Rules__
- Username unique (min 3 chars, max 30).
- Email unique, valid format.
- Password ≥ 8 chars.

__Performance Criteria__
- Auth response ≤ 300ms.
- Token/session must expire after inactivity (configurable).


__2. Property Management__
Functional Requirements
- Create, update, retrieve, and delete properties.
- Only authenticated users (e.g., hosts/admins) can manage properties.

__API Endpoints__
- POST /properties/ → Create property
- GET /properties/ → Retrieve all properties (search/filter supported)
- GET /properties/{property_id}/ → Retrieve property by ID
- PUT /properties/{property_id}/ → Update property
- DELETE /properties/{property_id}/ → Delete property

__Input/Output Specs__
Create Property Request:
```json
  {
    "title": "Luxury Villa with Pool",
    "description": "5-bedroom villa in Valletta",
    "location": "Valletta, Malta",
    "price": 500,
    "availability": true
  }
```
Create Property Response:
```json
  {
    "id": 10,
    "title": "Seaside Apartment",
    "owner": "jane_doe"
  }
```

__Validation Rules__
- Title required (3–100 chars).
- Price numeric, > 0.
- Location required.
- Only property owner or admin can update/delete.

__Performance Criteria__
- Property list response ≤ 500ms.
- Scale to 10,000+ properties.

__3. Booking System__
Functional Requirements
- Users can book available properties.
- Prevent overlapping bookings.
- Allow cancel/update.

__API Endpoints__
- POST /bookings/ → Create booking
- GET /bookings/ → List all bookings (user sees own; host sees bookings on their properties).
- GET /bookings/{booking_id}/ → Retrieve booking by ID
- PUT /bookings/{booking_id}/ → Update booking (e.g., change dates)
- DELETE /bookings/{booking_id}/ → Cancel booking


__Input/Output Specs__
Create Booking Request:
```json
  {
    "property_id": 10,
    "check_in": "2025-10-01",
    "check_out": "2025-10-05"
  }
```
Create Booking Response:
```json
  {
    "id": 100,
    "property": 10,
    "user": "jane_doe",
    "status": "confirmed"
  }
```

__Validation Rules__
- Property must exist and be available.
- Check-in < Check-out.
- No overlapping bookings allowed.
- Only booking owner or admin can cancel/update.

__Performance Criteria__
- Booking confirmation ≤ 400ms.
- Transaction safety (atomic DB transactions).
