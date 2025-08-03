---
title: Order Page
parent: Frontend
layout: default

nav_order: 3
---

#  Order Screen

The `Order` screen in the VH app is responsible for collecting user details and confirming meal bookings by placing an order via the backend. It includes data validation, local data caching, order summary rendering, and async transaction saving.

---

##  Logic Overview

###  Data Input & State

- Accepts `total`, `bookings`, and optional `items` from route params.
- Derives a list of selected meals (`items`) using fallback logic if not directly provided.
- Uses `useState` to manage user form fields (`name`, `email`, `contact`).

###  AsyncStorage

- On mount, reads saved form data (`user_form_data`) and pre-fills the form.
- Upon form submission, form data is validated and then saved locally.
- After a successful order, new `transactions` are prepended to the saved list in AsyncStorage.

---

##  Order & Payment Flow

###  Validation

Before placing an order:
- Name, email, and phone number are validated.
- Email and contact number formats are checked with regex.

###  Order Creation

- Meal bookings are flattened into a list of `{ meal_date, meal_type }` selections.
- These are sent to the `/api/coupons/initiate-order` endpoint.
- On success, the `order_id` is received and used to label the transaction.

###  Transaction Saving

Each transaction stores:
- Order ID
- Booking Date (`booked`)
- Target Day & Date
- Meal Type, Quantity, and Cost
- User Name

Transactions are saved to `AsyncStorage` under the `transactions` key.

---

##  UI Rendering

###  Form Inputs

- Name
- Email
- Contact

Each field is styled dynamically based on the theme.

###  Order Summary

- Lists selected meals in the format:
- Displays total amount payable.

### 🟦 Confirm Button

- Clicking the button triggers the `pay()` function.
- While the request is in progress, an `ActivityIndicator` spinner is shown.

---

##  Utilities

###  getDateFromWeekday

```ts
function getDateFromWeekday(weekday: string, referenceDate = new Date()): string {
const dayNames = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];
const targetDay = dayNames.indexOf(weekday);
const today = referenceDate.getDay();
const daysUntilTarget = (targetDay - today + 7) % 7;
const resultDate = new Date(referenceDate);
resultDate.setDate(referenceDate.getDate() + daysUntilTarget);
return resultDate.toISOString().split('T')[0];
}
```
##  Navigation on Success

On successful booking:

- Navigates to `/success/[orderID]`
- Passes the following as route parameters:
  - `orderID`
  - `items`
  - `total`

---


##  Stored Async Keys

- `user_form_data`:  
  Stores the user details in the format:  
  ```json
  {
    "name": "User Name",
    "email": "user@example.com",
    "contact": "9876543210"
  }
```
### Notes
- Uses `useMemo` for efficient recalculations of total and summary.
- Persists data using `AsyncStorage` to maintain user identity and history.
- Automatically adapts to system dark or light mode.
