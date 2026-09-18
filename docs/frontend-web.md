#  Frontend Web

The Probe web application is built with **Next.js** and provides the main interface for interacting with the battery testing, inventory, device, and booking systems.

<div class="probe-features" style="grid-template-columns: 1fr; max-width: 700px; margin: 24px auto;">
  <div class="probe-card">
    <img src="/images/frontend-web.jpg" alt="Frontend Web overview" class="probe-screenshot">
    <h3>Frontend Web Dashboard</h3>
  </div>
</div>

---

##  Frontend Setup

Navigate to the dashboard project:

```bash
cd HERckers_Dashboard
```

Install the required dependencies:

```bash
npm install
```

### Environment Variables

Create a `.env.local` file in the project root:

```env
NEXT_PUBLIC_API_URL=https://probe-herckers-3325e295df63.herokuapp.com
```

For the deployed application, `NEXT_PUBLIC_API_URL` should point to the hosted backend API.

### Run the Dashboard

Start the development server:

```bash
npm run dev
```

The dashboard will be available at:

```text
https://herckersdashboard-seven.vercel.app/
```

---

##  Architecture Pattern

### Web — Next.js

The Probe web application is built using **Next.js** with the **App Router**.

The application is organized into routes, reusable components, API communication utilities, and MQTT functionality to support maintainability and simple onboarding.

### Application Structure

```text
app/
├── api/
│   ├── mqtt/
│   │   └── route.ts
│   ├── booking-dashboard/
│   │   └── booking.types.ts
│   ├── dashboard/
│   ├── device-registry/
│   ├── inventory/
│   │   └── lib/
│   │       └── api.ts
│   └── mqtt.ts
│
├── booking-dashboard/
│   ├── LocalNav.tsx
│   └── page.tsx
│
├── bookingreport/
│   └── page.tsx
│
├── dashboard/
│   └── page.tsx
│
├── device-registry/
│   └── page.tsx
│
├── inventory/
│   └── page.tsx
│
├── livedata/
│   └── page.tsx
│
├── login/
│   └── page.tsx
│
├── profile/
│   └── page.tsx
│
├── report/
│   └── page.tsx
│
├── signup/
│   └── page.tsx
│
├── components/
│   ├── auth-form.module.css
│   ├── battery-dashboard.tsx
│   ├── booking-nav.tsx
│   ├── BookingForm.tsx
│   ├── device-registered-modal.tsx
│   ├── device-registry.tsx
│   ├── inventory-dashboard.tsx
│   ├── landing-page.module.css
│   ├── landing-page.tsx
│   ├── login-form.tsx
│   ├── main-content.tsx
│   ├── profile-page.module.css
│   ├── profile-page.tsx
│   ├── RecyclerCard.tsx
│   ├── register-device-form.tsx
│   ├── sidebar.tsx
│   └── signup-form.tsx
│
├── lib/
│   ├── api.ts
│   └── mqtt.ts
│
├── favicon.ico
├── globals.css
└── layout.tsx
```

The structure separates application routes from reusable UI components and communication utilities.

---

##  Component Structure

Reusable UI components live in `app/components/` and are shared across the application's routes. Each component handles a focused piece of the interface.

| Component | Responsibility |
| :-------- | :-------------- |
| **`battery-dashboard.tsx`** | Displays battery testing data and metrics on the dashboard. |
| **`booking-nav.tsx`** | Provides navigation specific to booking-related pages. |
| **`BookingForm.tsx`** | Handles the form and submission flow for creating a battery booking. |
| **`device-registered-modal.tsx`** | Displays confirmation feedback after a device has been successfully registered. |
| **`device-registry.tsx`** | Renders the device registry interface for viewing and managing registered devices. |
| **`inventory-dashboard.tsx`** | Displays available battery inventory and stock status. |
| **`landing-page.tsx`** | Renders the application's landing page content. |
| **`login-form.tsx`** | Handles user login input and authentication submission. |
| **`main-content.tsx`** | Provides the main content wrapper/layout used across dashboard pages. |
| **`profile-page.tsx`** | Displays authenticated user profile information. |
| **`RecyclerCard.tsx`** | Displays summary information for a recycler entity. |
| **`register-device-form.tsx`** | Handles the form and submission flow for registering a new device. |
| **`sidebar.tsx`** | Provides the main sidebar navigation for the dashboard. |
| **`signup-form.tsx`** | Handles new user signup input and submission. |

Component-specific styling is co-located using CSS Modules, such as `auth-form.module.css`, `landing-page.module.css`, and `profile-page.module.css`, keeping styles scoped to their corresponding component.

---

##  State Propagation, Token Management and Route Security

### Network Communication Core

The project routes external communication requests through unified client abstractions in:

```text
app/lib/api.ts
```

This allows authentication information to be added automatically to protected API requests.

The authentication header is generated using the stored JWT:

```typescript
const getAuthHeaders = (): Record<string, string> => {
  const token =
    typeof window !== "undefined"
      ? localStorage.getItem("probe_token")
      : null;

  return {
    "Content-Type": "application/json",
    ...(token && {
      "Authorization": `Bearer ${token}`,
    }),
  };
};
```

### Booking Request

Booking creation is also handled through the centralized API communication layer:

```typescript
export async function submitBookingCreation(
  payload: BookingPayload
): Promise<Response> {
  const targetUrl =
    `${process.env.NEXT_PUBLIC_API_URL}/bookings/`;

  const response = await fetch(targetUrl, {
    method: "POST",
    headers: getAuthHeaders(),
    body: JSON.stringify(payload),
  });

  if (!response.ok) {
    const errorDetails = await response.json();

    throw new Error(
      errorDetails.detail ||
      "Fails to establish reservation request transaction."
    );
  }

  return response.json();
}
```

The resulting flow is:

```text
User
 ↓
Frontend Component
 ↓
app/lib/api.ts
 ↓
JWT Authorization Header
 ↓
FastAPI Backend
 ↓
Booking Endpoint
```

---

##  API Integration & API Service

### API Service Layer

All backend communication is routed through a centralized API service defined in:

```text
app/lib/api.ts
```

Rather than individual components implementing their own `fetch` logic, each component calls a shared function from this service layer. This keeps request construction, authentication, and error handling consistent across the application.

### Integration Pattern

A typical API integration follows this pattern:

1. A component calls a function exported from `app/lib/api.ts` (e.g., `submitBookingCreation`).
2. The service function builds the target URL using `process.env.NEXT_PUBLIC_API_URL`.
3. `getAuthHeaders()` attaches the JWT token, if available, to the request headers.
4. The request is sent to the FastAPI backend using `fetch`.
5. The response is checked for errors; failed requests throw an `Error` with the backend's returned detail message.
6. On success, the parsed JSON response is returned to the calling component.

### Endpoints Used

The frontend integrates with the following backend resources:

```text
/users/
/devices/
/batteries/
/v1/sensor-readings/
/bookings/
```

### Example: Authenticated Request Headers

```typescript
const getAuthHeaders = (): Record<string, string> => {
  const token =
    typeof window !== "undefined"
      ? localStorage.getItem("probe_token")
      : null;

  return {
    "Content-Type": "application/json",
    ...(token && {
      "Authorization": `Bearer ${token}`,
    }),
  };
};
```

---

##  Navigational Rails & Route Security

### Context-Aware Sub-Navigation

The booking dashboard uses:

```text
app/booking-dashboard/LocalNav.tsx
```

The component uses client-side rendering where required:

```typescript
"use client";
```

It provides local navigation within the booking dashboard while maintaining the dashboard's overall layout.

The navigation styling uses Tailwind CSS configurations, including:

```text
bg-[#EAF4FC]
```

and responsive layout dimensions such as:

```text
w-full
md:w-[220px]
```

This allows the navigation rail to adapt to different screen sizes.

### State & Token Lifecycles

Authentication tokens are stored in the browser's local storage.

The application retrieves the stored token when making authenticated requests:

```typescript
localStorage.getItem("token")
```

The token is then included in the request:

```text
Authorization: Bearer <token>
```

This allows the frontend to maintain the user's authenticated session while communicating with protected backend endpoints.

> **Implementation note:** The codebase contains references to both `probe_token` and `token`. The documentation should ultimately use the key implemented by the current authentication flow consistently.

---

##  Unified Network Client Abstraction

Frontend components should not implement independent raw `fetch` pipelines for backend communication.

Instead, requests are routed through the shared API abstraction located in:

```text
app/lib/api.ts
```

The API client is responsible for:

* Constructing backend request URLs.
* Attaching authentication tokens.
* Sending request payloads.
* Handling API responses.
* Standardizing error handling.

This creates a consistent communication layer between the Next.js frontend and the FastAPI backend.

```text
┌─────────────────────────┐
│     Frontend Component  │
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│      app/lib/api.ts     │
│                         │
│  • Authentication       │
│  • API Requests         │
│  • Error Handling       │
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│      FastAPI Backend    │
└─────────────────────────┘
```

Centralizing network communication reduces duplicated request logic and provides a consistent approach to authentication and API error handling throughout the application.

---

##  Code Standards

Frontend code follows the project-wide naming conventions, folder structure, and error handling patterns outlined below.

### Naming Conventions & Structure
* Components use `PascalCase.tsx` (e.g., `BookingForm.tsx`)
* Utility files use `camelCase.ts` (e.g., `api.ts`)
* Style files use CSS Modules with `kebab-case.module.css` (e.g., `landing-page.module.css`)
### Error Handling & API Patterns
* API requests are wrapped in `try/catch` blocks.
* User-facing error messages must be human-readable rather than surfacing raw backend errors to the UI.
* Fallback UI components (like Error Boundaries) should handle unexpected runtime crashes.

