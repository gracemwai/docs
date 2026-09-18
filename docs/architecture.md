#  Architecture

Probe is a hardware-integrated battery testing and reuse platform. The system combines physical battery testing hardware, IoT communication, a FastAPI backend, PostgreSQL database, and web/mobile interfaces.

Battery condition is derived from real sensor readings collected during testing. The State of Health (SoH) is calculated using a predefined formula rather than a trained AI model.

---

## System Diagram

The Probe architecture connects the physical battery testing process with the digital platform.

<!-- Add your system architecture image here -->

![Probe System Architecture](/images/images/System-Architecture.jpeg)

The architecture consists of the following major actors and components:

### Admin

The Admin uses the Probe application to manage users and oversee platform operations. Admin access is protected through role-based authorization.

### Recycler

The Recycler uses Probe's testing hardware and dashboard to register batteries, monitor testing results, view battery data, and manage available battery inventory.

### UPS Company

The UPS Company uses the platform to browse available batteries, view suitable battery stock, and submit booking requests for batteries intended for reuse.

### Probe Testing Hardware

The physical testing system holds the battery under test and collects electrical and environmental measurements such as:

* Temperature
* Resting voltage
* Load voltage
* Current

The hardware is designed to support independent battery testing and transmit measurements to the software platform.

### ESP32

The ESP32 acts as the communication controller for the testing hardware. It receives readings from connected sensors and publishes the telemetry data to the MQTT broker.

### Sensors

The connected sensors measure the physical characteristics of the battery during testing. These measurements provide the raw data used to assess battery condition.

### MQTT Broker

The MQTT broker provides communication between the connected testing hardware and the backend services.

The ESP32 publishes sensor readings through MQTT, while subscribed services consume the telemetry for processing and monitoring.

### State of Health (SoH) Calculation

The SoH calculation processes the measured battery data and applies Probe's predefined formula to estimate the battery's State of Health.

The calculation is **formula-based and does not use a trained AI or machine-learning model**.

### FastAPI Backend

The FastAPI backend is the central software layer of Probe. It:

* Authenticates users
* Authorizes protected requests
* Manages battery records
* Processes booking requests
* Receives and validates battery testing data
* Communicates with the PostgreSQL database

### PostgreSQL Database

PostgreSQL stores the platform's persistent data, including:

* User records
* Battery information
* Sensor/test results
* Inventory information
* Booking records
* Booking statuses

### Web Dashboard

The web dashboard provides the interface through which users interact with the Probe platform. It allows users to access inventory, battery information, bookings, and relevant testing data based on their role.

### Mobile / PWA Application

The mobile interface provides access to Probe functionality from mobile devices and supports platform workflows where applicable.

---

##  Modules

Probe's backend is organized into distinct modules, each responsible for a specific part of the system's functionality. These modules are coordinated through the FastAPI backend and API layer.

### User Management Module

Handles admin-facing user operations. It processes requests to view and edit user records, verifies changes, and returns confirmation once user details have been successfully updated.

**Responsibilities:**
* Retrieve the user list for the admin interface
* Process user edit requests
* Verify and persist updated user records to the database

### Battery Registration Module

Handles the registration of batteries into the system when a recycler scans or selects a battery profile.

**Responsibilities:**
* Receive scanned barcode or selected battery profile data
* Verify battery details
* Register the battery for testing and tracking

### State of Health (SoH) Module

Processes raw battery telemetry received from the hardware pipeline and calculates each battery's State of Health using a predefined formula.

**Responsibilities:**
* Subscribe to tested battery data published through the MQTT broker
* Apply the SoH calculation formula to incoming readings
* Store the resulting battery health data for use across the platform

### Battery Booking Module

Manages the booking workflow for UPS companies and inventory managers, from viewing available batteries to validating and allocating reserved stock.

**Responsibilities:**
* Retrieve available battery data for browsing
* Process booking and allocation requests
* Validate and reserve batteries against inventory status

---

## Data Flow

Probe has several connected data flows. The most important flows are authentication, battery testing, and battery booking.

### User Authentication

The authentication flow allows users to securely access features based on their assigned role.

```text
User
  ↓
Web / Mobile Application
  ↓
POST /users/login
  ↓
FastAPI Backend
  ↓
JWT Token
  ↓
Authenticated Requests
```

The user submits their credentials through the application.

The backend validates the credentials and returns a JWT access token when authentication succeeds.

The client uses the token when making protected API requests:

```text
Authorization: Bearer <token>
```

The backend validates the token before allowing access to protected resources.

---

###  Battery Testing Flow

The battery testing process connects the physical hardware to the digital platform.

```text
Battery
   ↓
Sensors
   ↓
ESP32
   ↓
MQTT Broker
   ↓
SoH Calculation
   ↓
FastAPI Backend
   ↓
PostgreSQL
   ↓
Recycler Dashboard
```

The process works as follows:

1. A recycler places a battery into the Probe testing hardware.
2. The sensors collect measurements such as temperature, voltage, and current.
3. The ESP32 receives the sensor readings.
4. The ESP32 publishes the readings through MQTT.
5. The SoH calculation processes the readings using Probe's predefined formula.
6. The resulting battery test data is sent to the FastAPI backend.
7. The backend validates and stores the data in PostgreSQL.
8. The recycler can view the resulting battery information through the dashboard.

---

###  Battery Booking Flow

The booking process connects available battery inventory with UPS companies looking for batteries for reuse.

```text
UPS Company
    ↓
Web Dashboard
    ↓
GET /batteries/
    ↓
FastAPI Backend
    ↓
PostgreSQL
    ↓
Available Battery Inventory
    ↓
UPS Company selects battery
    ↓
POST /bookings/
    ↓
FastAPI Backend
    ↓
Booking created
    ↓
Status: PENDING
```

The process works as follows:

1. The UPS Company logs into the platform.
2. The dashboard requests available battery inventory from the backend.
3. The backend retrieves available battery records from PostgreSQL.
4. The UPS Company selects a suitable battery and quantity.
5. The dashboard sends a booking request to the backend.
6. The backend validates the authenticated user and booking information.
7. A booking record is created with an initial status of `PENDING`.
8. The booking can subsequently move through the defined booking lifecycle.

---

##  Component Interaction

The major components work together as follows:

| Component           | Responsibility                                                            |
| ------------------- | ------------------------------------------------------------------------- |
| **Sensors**         | Measure battery temperature, voltage, and current.                        |
| **ESP32**           | Collect sensor readings and publish telemetry through MQTT.               |
| **MQTT Broker**     | Transport telemetry between connected devices and subscribing services.   |
| **SoH Calculation** | Calculate battery State of Health using a predefined formula.             |
| **FastAPI Backend** | Handle authentication, battery management, bookings, and data processing. |
| **PostgreSQL**      | Persist users, batteries, testing data, inventory, and bookings.          |
| **Web Dashboard**   | Provide the user interface for platform workflows.                        |
| **Mobile / PWA**    | Provide mobile access to supported Probe functionality.                   |

---

##  Architecture Principles

Probe's architecture is designed around several principles:

* **Hardware-to-cloud integration:** Physical battery measurements are connected directly to the digital platform.
* **Real sensor data:** Battery assessment is based on measurements collected from the testing hardware.
* **Formula-based assessment:** State of Health is calculated using a predefined formula rather than a trained AI model.
* **Role-based access:** Platform capabilities are controlled according to user roles.
* **API-driven communication:** Frontend applications communicate with the backend through REST APIs.
* **Persistent data management:** Battery, user, testing, inventory, and booking data are stored in PostgreSQL.
* **Asynchronous telemetry:** MQTT provides lightweight communication for IoT sensor data.


---

##  Design Guidelines

Probe follows a consistent set of visual design principles and brand guidelines to maintain a cohesive look and feel across the web dashboard, mobile application, and documentation.

![Probe Style Guide](/images/Style-Guide.jpg)

### Product Logo

The PROBE logo combines the wordmark with a signal/wireless icon, reflecting the platform's IoT-connected battery testing capability.

The logo is available for both light and dark backgrounds:

* **Dark background:** white wordmark on a dark navy/black background
* **Light/brand background:** white wordmark on Probe Blue background

The logo should be displayed with adequate spacing and should not be stretched, recolored, or altered.

### Color Palette

| Color | Hex | Usage |
| :---- | :-- | :---- |
| **Probe Blue** | `#005BEF` | Primary brand color — buttons, links, active states, and key interface accents |
| **Probe Light Blue** | *(secondary blue, teal-leaning)* | Secondary buttons, highlighted states |
| **Probe Light** | `#DFF0F7` | Backgrounds, cards, and subtle accents |
| **Probe Black** | `#000000` | Primary text and dark UI elements |

### Typography

Probe uses **Inter** as its primary typeface, applied across the interface in a range of weights to establish clear visual hierarchy — from light body text through bold headings and uppercase emphasis.

### Buttons

Buttons follow four consistent states across all primary actions (Signup, Login, Logout, Request Booking, Download):

* **Primary (filled brand blue):** default active button state
* **Secondary (filled light blue):** alternate emphasis or hover state
* **Tertiary (light gray background):** lower-emphasis actions
* **Disabled (gray, muted text):** unavailable or inactive actions

This consistent button styling is applied across both the web dashboard and mobile application to maintain a unified interaction pattern.

### Icons

Probe uses a consistent icon set across the product for common actions and navigation:

* Home
* Devices / Battery
* Live Data / Export
* Settings
* Bookings / Inventory

Icons follow the same visual states as buttons — default (outlined), active/selected (filled blue), and inactive — to maintain consistency between navigation elements and interactive components.

### Visual Design Principles

* **Consistency:** UI components (buttons, icons, cards, navigation) follow the same styling patterns across web and mobile.
* **Clarity:** Interfaces prioritize readability and clear calls to action, using Probe Blue to draw attention to primary actions.
* **Accessibility:** Sufficient contrast is maintained between text and background colors to support readability.
* **Simplicity:** Layouts avoid unnecessary visual clutter, keeping the focus on battery data, testing results, and platform workflows.