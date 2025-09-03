---
title: Routes Guide
parent: Backend
layout: default
nav_order: 2
---

# Routes Guide

This document provides an overview of the **API routes** for both **Admin** and **User** modules.

## Authentication
All protected routes require a **JWT token** in the `Authorization` header.


##  Admin Routes

Base path: `/api/admin`

### 1. **Login**
- **POST** `/login`
- Request body:
    ```json
    {
        "username": "admin",
        "password": "******"
    }
    ```
- Response: JWT token

### 2. **Verify Token**
- **GET** `/verify-token`
- Protected
- Response: Confirms token validity

### 3. **Upload Menu**
- **POST** `/menu/upload`
- Protected
- Upload Excel file (menuFile) containing menu & meal timings
- Response: Success message with count of items uploaded

### 4. **Get Current Menu**
- **GET** `/menu/current`
- Protected
- Returns all currently active menu items

### 5. **Get All Coupons**
- **GET** `/coupons/all`
- Protected
- Query parameters:
    - `search`: search by orderId, name, email, phone
    - `meal_type`: Breakfast | Lunch | Dinner
    - `status`: Active | Used
    - `date`: YYYY-MM-DD
- Response: List of coupons (max 200 records)

### 6. **Get Today's Summary**
- **GET** `/coupons/summary`
- Protected
- Returns:
    ```json
    {
        "Breakfast": { "Active": 10, "Used": 5 },
        "Lunch": { "Active": 20, "Used": 15 },
        "Dinner": { "Active": 30, "Used": 10 },
        "upcomingMeal": "Lunch"
    }
    ```

### 7. **Mark Coupon as Used**
- **POST** `/coupons/mark-used/:id`
- Protected
- Path parameter: `id` - Coupon ID
- Marks coupon status as Used


##  User Routes

Base path: `/api/user`

### 1. **Signup**
- **POST** `/signup`
- Request body:
    ```json
    {
        "name": "John",
        "email": "john@example.com",
        "password": "123456"
    }
    ```
- Sends verification email
- Response: Signup success message with token

### 2. **Signin**
- **POST** `/signin`
- Request body:
    ```json
    {
        "email": "john@example.com",
        "password": "123456"
    }
    ```
- Response: JWT token on success

### 3. **Verify Email**
- **GET** `/verify?token=<verification_token>`
- Triggered by email link
- Marks user as verified and redirects to frontend VERIFIED page

---
## 🍽️ Menu Routes

**Base path:** `/api/menu`

### 1. Get Current Menu
`GET /current`

Returns the currently active menu items along with last updated time.

#### Success Response (200)
```json
{
    "success": true,
    "lastUpdated": "2025-09-02T11:22:45.000Z",
    "menu": [
        {
            "day_of_week": "Monday",
            "meal_type": "Breakfast",
            "description": "Idli with Sambar",
            "price": 30,
            "coupons": 50,
            "available_coupons": 47
        },
        {
            "day_of_week": "Monday",
            "meal_type": "Lunch",
            "description": "Veg Thali",
            "price": 60,
            "coupons": 100,
            "available_coupons": 93
        }
    ]
}
```

#### Error Response (404)
```json
{
    "success": false,
    "message": "No active menu found. Please ask the admin to upload one."
}
```

### 2. Get Last Updated Time
`GET /last-updated`

Returns the timestamp when the active menu was last updated.

#### Success Response (200)
```json
{
    "success": true,
    "lastUpdated": "2025-09-02T11:22:45.000Z"
}
```

#### Error Response (404)
```json
{
    "success": false,
    "message": "No active menu found."
}
```

### 3. Get Cutoff Meal Timings
`GET /booking-Closetimings`

Returns the cutoff booking time for each meal type.

#### Success Response (200)
```json
{
    "success": true,
    "data": {
        "Breakfast": { "hour": 9, "minute": 0 },
        "Lunch": { "hour": 14, "minute": 0 },
        "Dinner": { "hour": 21, "minute": 0 }
    }
}
```

#### Error Response (500)
```json
{
    "success": false,
    "message": "Failed to fetch meal timings"
}
```

## Notes
- Day of week ordering is handled server-side
- Cutoff timings are stored in the MealTiming table
- If no menu exists, endpoints return 404