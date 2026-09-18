#  Mobile App

The Probe mobile application is a cross-platform application built with **Flutter and Dart**. It supports field operations for recyclers and UPS companies by providing access to device registration, battery information, live data, reports, bookings, and user profiles.

The mobile application communicates with the same FastAPI backend used by the web dashboard, allowing both platforms to work with the same users, batteries, devices, sensor readings, and booking data.

<div class="probe-features probe-features-3col">
  <div class="probe-card">
    <img src="/images/frontend-phone.jpg" alt="Mobile app screen 1" class="probe-screenshot">
    <h3>Home</h3>
  </div>
  <div class="probe-card">
    <img src="/images/frontend-mobile.jpg" alt="Mobile app screen 2" class="probe-screenshot">
    <h3>Devices</h3>
  </div>
  <div class="probe-card">
    <img src="/images/frontend-mobile2.jpg" alt="Mobile app screen 3" class="probe-screenshot">
    <h3>Live Data</h3>
  </div>
</div>
---

##  Mobile Technology Stack

| Technology | Purpose                                      |
| ---------- | -------------------------------------------- |
| Flutter    | Cross-platform mobile application framework  |
| Dart       | Application programming language             |
| Android    | Android platform target                      |
| iOS        | iOS platform target                          |
| FastAPI    | Backend API integration                      |
| REST API   | Communication between mobile app and backend |

---

##  Project Structure

The mobile application is organized into platform configuration, screens, reusable widgets, themes, and testing files.

```text
HERCKERS_MOBILE/
└── probe/
    ├── android/
    │   ├── app/
    │   ├── gradle/
    │   └── build.gradle.kts
    │
    ├── ios/
    │   ├── Flutter/
    │   └── Runner.xcodeproj
    │
    ├── assets/
    │   └── images/
    │       ├── recycler.png
    │       └── ups.png
    │
    ├── lib/
    │   ├── screens/
    │   │   ├── device_registration_page.dart
    │   │   ├── profile_page.dart
    │   │   ├── recycler_landing_page.dart
    │   │   └── ups_landing_page.dart
    │   │
    │   ├── theme/
    │   │   └── app_colors.dart
    │   │
    │   ├── widgets/
    │   │   ├── app_bottom_nav_bar.dart
    │   │   ├── device_registered_sheet.dart
    │   │   ├── labeled_text_field.dart
    │   │   └── metric_group_card.dart
    │   │
    │   └── main.dart
    │
    ├── test/
    │   └── widget_test.dart
    │
    ├── analysis_options.yaml
    └── pubspec.yaml
```

### Directory Responsibilities

**`lib/screens/`**
Contains the main application screens and user-facing workflows.

**`lib/widgets/`**
Contains reusable interface components shared across screens.

**`lib/theme/`**
Contains application-wide design tokens such as colors.

**`assets/`**
Contains images and other static application resources.

**`android/` and `ios/`**
Contain the native platform configurations required to build and run the application.

**`test/`**
Contains Flutter widget and application tests.

---

##  User Roles and Navigation

The mobile application supports two primary user roles:

* **Recycler**
* **UPS Company**

The available navigation options change according to the authenticated user's role.

### Recycler Navigation

Recyclers can access:

* Home
* Live Data
* Reports
* Profile

### UPS Company Navigation

UPS companies can access:

* Home
* Bookings
* Reports
* Profile

This role-based navigation ensures that each user sees functionality relevant to their responsibilities.

---

##  Core Mobile Features

### Device Registration

The device registration screen allows recyclers to configure and register testing devices.

The workflow supports:

* Device serial number selection
* Channel assignment
* Device status
* Battery association
* Device verification

During development, a local set of mock devices is used to validate the registration interface before connecting to physical hardware.

Example device records include:

```text
PR-12345 → CH1 → Active → BAT-001
PR-12346 → CH2 → Inactive → BAT-002
PR-12347 → CH1 → Active → BAT-003
```

---

### Live Battery Data

The mobile application provides recyclers with access to battery testing information, including:

* Temperature
* Voltage
* Current
* State of Health (SoH)
* Battery classification

These values originate from the hardware testing pipeline and are made available through the backend API.

---

### Booking Management

UPS companies can use the mobile application to view and manage battery bookings.

A typical workflow is:

```text
UPS Company
     ↓
View available batteries
     ↓
Select battery
     ↓
Create booking
     ↓
POST /bookings/
     ↓
Backend creates booking
     ↓
Booking status: PENDING
```

---

### Reports

The mobile application provides access to reports generated from battery testing and booking activities.

Reports allow users to review previously recorded operational information without needing to access the desktop dashboard.

---

### Profile Management

The profile screen provides access to authenticated user information and role-specific account functionality.

The application uses the authenticated user's role to determine available navigation and operational features.

---

##  Reusable UI Components

Reusable components are stored inside `lib/widgets/` to avoid duplicating interface code across different screens.

### Metric Group Card

`metric_group_card.dart`

Displays battery telemetry values such as:

* Temperature
* Voltage
* Current

This provides a consistent format for displaying testing measurements.

### Bottom Navigation Bar

`app_bottom_nav_bar.dart`

Provides navigation between the main sections of the mobile application.

The navigation items are dynamically determined by the user's role.

### Labeled Text Field

`labeled_text_field.dart`

Provides a reusable text-input component for forms such as device registration and profile information.

### Device Registered Sheet

`device_registered_sheet.dart`

Displays confirmation feedback after a device has been successfully registered.

---

##  Application Theme

The mobile application uses shared design tokens defined in:

```text
lib/theme/app_colors.dart
```

The main colors include:

| Token               | Purpose                                      |
| ------------------- | -------------------------------------------- |
| `primaryBlue`       | Primary buttons and major interface elements |
| `outlineBlue`       | Input and component borders                  |
| `navBackground`     | Navigation background                        |
| `navBorder`         | Navigation borders                           |
| `inactiveGray`      | Inactive interface elements                  |
| `hintGray`          | Placeholder and secondary text               |
| `statusActiveGreen` | Active device/status indicators              |

Using centralized color definitions keeps the mobile interface visually consistent with the Probe web dashboard.

---

##  Backend API Integration

The mobile application communicates with the Probe FastAPI backend using REST APIs.

Authenticated requests include the user's bearer token:

```text
Authorization: Bearer <token>
```

The mobile application uses the same backend resources as the web dashboard, including:

```text
/users/
/devices/
/batteries/
/v1/sensor-readings/
/bookings/
```

This allows the mobile and web applications to operate on the same source of data.

### Data Flow

```text
Flutter Mobile App
        ↓
REST API Request
        ↓
FastAPI Backend
        ↓
Service Layer
        ↓
PostgreSQL Database
        ↓
API Response
        ↓
Flutter Mobile App
```

For battery telemetry, the broader system follows:

```text
Battery Sensors
       ↓
ESP32
       ↓
HiveMQ
       ↓
State of Health Module
       ↓
FastAPI Backend
       ↓
PostgreSQL
       ↓
Mobile / Web Dashboard
```

---

##  Local Development

To run the mobile application locally:

```bash
cd HERCKERS_MOBILE/probe

flutter pub get

flutter run
```

Before running the application, ensure that:

* Flutter is installed.
* Dart is available through the Flutter installation.
* An Android emulator, iOS simulator, or physical device is available.
* The required backend services are running or accessible.

### Verify the Installation

Run:

```bash
flutter doctor
```

This checks the local Flutter development environment and identifies missing dependencies.

---

##  Testing

Flutter's testing framework is used for validating mobile interface components and application behaviour.

Tests are located in:

```text
test/
└── widget_test.dart
```

Tests can be executed using:

```bash
flutter test
```

The project also uses `analysis_options.yaml` to enforce Dart code analysis and identify potential issues during development.

---

##  Mobile-to-System Architecture

The mobile application forms part of the larger Probe ecosystem:

```text
                    PROBE PLATFORM

       ┌──────────────────────────────┐
       │        Flutter Mobile        │
       │                              │
       │ Recycler   |   UPS Company   │
       └──────────────┬───────────────┘
                      │
                      │ REST API
                      ▼
             ┌─────────────────┐
             │  FastAPI Backend │
             └────────┬────────┘
                      │
                      ▼
               ┌─────────────┐
               │ PostgreSQL  │
               └─────────────┘
                      ▲
                      │
              Battery Data
                      │
               ┌──────┴──────┐
               │     ESP32   │
               └──────┬──────┘
                      ▲
                      │
               Battery Sensors
```

The mobile application therefore acts as a field-facing interface while the FastAPI backend provides centralized authentication, business logic, data storage, and API access.


### Mobile — Flutter / Dart

The Flutter application follows Dart conventions.

| Element   | Convention        | Example                         |
| --------- | ----------------- | ------------------------------- |
| Variables | `camelCase`       | `userRole`                      |
| Functions | `camelCase`       | `handleLogout()`                |
| Classes   | `PascalCase`      | `MetricGroupCard`               |
| Files     | `snake_case.dart` | `device_registration_page.dart` |
| Constants | `camelCase`       | `primaryBlue`                   |
