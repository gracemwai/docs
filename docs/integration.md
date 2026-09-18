#  Integration

Probe integrates several services to connect the hardware testing system, backend API, database, and web dashboard.

The main integrations are **Heroku, Vercel, PostgreSQL, and HiveMQ**. Together, these services support application hosting, data storage, API communication, and real-time sensor data transmission.

##  Integration Overview

```text
                         ┌──────────────────┐
                         │    ESP32 Device  │
                         │  Temperature     │
                         │  Voltage         │
                         │  Current         │
                         └────────┬─────────┘
                                  │
                           MQTT Sensor Data
                                  │
                                  ▼
                         ┌──────────────────┐
                         │      HiveMQ      │
                         │   MQTT Broker    │
                         └────────┬─────────┘
                                  │
                         Sensor Data Stream
                                  │
                                  ▼
                         ┌──────────────────┐
                         │   FastAPI        │
                         │    Backend       │
                         └───────┬───┬──────┘
                                 │   │
                    ┌────────────┘   └─────────────┐
                    ▼                              ▼
             ┌──────────────┐              ┌──────────────┐
             │  PostgreSQL  │              │   Dashboard  │
             │   Database   │              │    Vercel    │
             └──────────────┘              └──────────────┘
```

The integration flow allows Probe to move data from the physical testing hardware through the MQTT broker and backend before it is stored and presented to users.

---

##  External Services

| Service        | Purpose                                                         | Integration                                                                    |
| -------------- | --------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| **Heroku**     | Hosts the FastAPI backend and PostgreSQL database               | Backend deployment and production configuration                                |
| **Vercel**     | Hosts the Next.js web dashboard                                 | Communicates with the FastAPI API through `NEXT_PUBLIC_API_URL`                |
| **PostgreSQL** | Stores users, devices, batteries, bookings, and sensor readings | Connected to FastAPI through SQLAlchemy                                        |
| **HiveMQ**     | MQTT broker for hardware communication                          | Receives sensor data from ESP32 and distributes messages to subscribed clients |

---

##  Heroku Integration

Heroku hosts the Probe backend application and its PostgreSQL database.

The backend uses environment variables for production configuration, including:

```text
DATABASE_URL
JWT_SECRET_KEY
JWT_ALGORITHM
JWT_EXPIRE_DAYS
CORS_ORIGINS
```

The backend can be deployed through Git or a connected GitHub repository.

The deployment process follows:

```text
Code Changes
     ↓
Git Push
     ↓
Heroku Build
     ↓
Dependencies Installed
     ↓
FastAPI Application Started
     ↓
Production API Available
```

Heroku also provides the production environment in which the FastAPI application communicates with the PostgreSQL database.

---

##  Vercel Integration

Vercel hosts the Probe web dashboard built with **Next.js**.

The frontend requires the backend API URL through:

```text
NEXT_PUBLIC_API_URL
```

For local development:

```text
NEXT_PUBLIC_API_URL=http://localhost:8000
```

For production, the variable points to the deployed FastAPI backend.

The communication flow is:

```text
UPS Company / Recycler
          ↓
     Next.js Dashboard
          ↓
   NEXT_PUBLIC_API_URL
          ↓
      FastAPI API
          ↓
       PostgreSQL
```

When the production API URL is changed, the Vercel deployment must be redeployed so that the updated environment configuration is applied.

---

##  PostgreSQL Integration

PostgreSQL is Probe's primary relational database.

The FastAPI backend connects to PostgreSQL using the `DATABASE_URL` configuration value.

SQLAlchemy is used as the ORM layer, while **Alembic** manages database schema migrations.

The main application data stored in PostgreSQL includes:

* Users
* Devices
* Batteries
* Sensor readings
* Bookings

The database integration follows:

```text
FastAPI
   ↓
SQLAlchemy
   ↓
PostgreSQL
   ↓
Probe Application Data
```

Database schema changes are applied through Alembic migrations:

```bash
alembic upgrade head
```

---

##  HiveMQ / MQTT Integration

HiveMQ provides the MQTT communication layer between the physical testing hardware and the software system.

The **ESP32** publishes sensor measurements through MQTT. These measurements include:

* Temperature
* Voltage
* Current

The MQTT broker receives the messages and makes them available to subscribed clients.

```text
ESP32
  │
  │ MQTT Publish
  ▼
HiveMQ Broker
  │
  │ MQTT Subscribe
  ▼
SOH / Backend Processing
  │
  ├──► Battery Health Results
  │
  └──► Sensor Data Storage
```

The MQTT communication layer allows sensor data to be transmitted without requiring the ESP32 to communicate directly with the web dashboard.

---

##  Integration Data Flow

A typical battery testing workflow moves through the following services:

```text
1. Battery connected to testing hardware
              ↓
2. ESP32 reads sensors
              ↓
3. ESP32 publishes readings through MQTT
              ↓
4. HiveMQ receives the sensor message
              ↓
5. SOH processing calculates battery health
              ↓
6. Tested battery data is sent to the backend
              ↓
7. FastAPI validates and processes the data
              ↓
8. PostgreSQL stores the results
              ↓
9. Next.js dashboard requests the data
              ↓
10. Recycler views battery testing results
```

For the UPS company workflow:

```text
UPS Company
     ↓
Vercel-hosted Dashboard
     ↓
GET /batteries/
     ↓
FastAPI Backend
     ↓
PostgreSQL
     ↓
Available Battery Inventory
     ↓
UPS Company Selects Battery
     ↓
POST /bookings/
     ↓
FastAPI Backend
     ↓
Booking Created
     ↓
Status: PENDING
```

---

##  Integration Configuration Summary

| Integration          | Configuration                                                 |
| -------------------- | ------------------------------------------------------------- |
| Heroku → FastAPI     | Production environment variables and deployment configuration |
| FastAPI → PostgreSQL | `DATABASE_URL`                                                |
| Vercel → FastAPI     | `NEXT_PUBLIC_API_URL`                                         |
| ESP32 → HiveMQ       | MQTT broker connection and topic configuration                |
| FastAPI → HiveMQ     | MQTT client configuration and subscriptions                   |
| FastAPI → PostgreSQL | SQLAlchemy connection and Alembic migrations                  |

These integrations allow Probe to operate as a connected system rather than as separate hardware, backend, database, and frontend components.
