#  Security

Probe implements security measures across authentication, password storage, authorization, cross-origin access, and application configuration. These controls protect user accounts, API endpoints, and sensitive system configuration.

---

##  Security Architecture Diagram

The diagram below illustrates Probe's security architecture, organized into distinct zones that separate users, perimeter defenses, trusted application logic, and data storage.

![Probe Security Architecture](/images/SAD-Service.jpg)

### Users / External Zone

This zone represents the people and devices interacting with Probe from outside the system: Recyclers, Inventory Managers (UPS Company), System Admins, and connected Battery Sensors. All communication from this zone to the platform is encrypted using **HTTPS + TLS 1.3**.

### Edge / Perimeter Security Zone

This zone forms the first line of defense between external users and the trusted application layer. It includes:

* **Firewall / WAF** – Provides IP allowlisting, SQL injection prevention, rate limiting (per IP/per router), DDoS protection, and exploit protection.
* **API Gateway** – Handles rate limiting, TLS termination, request routing, request size limits, and authentication enforcement before requests reach the application.
* **HiveMQ Broker Endpoint** – Enforces payload restrictions (maximum message sizes for incoming telemetry) and connection throttling (caps the maximum connection rate per sensor node) to prevent buffer overruns and abuse from IoT devices.

### Trusted Application Zone

This zone contains Probe's core authenticated logic, split into several responsibilities:

* **Authentication & Session** – Uses short-lived session tokens (5–15 minutes) to mitigate session hijacking, bcrypt password hashing, strong password entropy requirements, and multi-factor authentication (MFA).
* **Authorization** – Enforces role-based access control (separating Recycler vs. Inventory Manager permissions) and object-level authorization, ensuring an Inventory Manager can only modify resources within their designated scope.
* **Audit and Event Logging** – Centrally tracks who triggered an action, what parameters were passed, when it occurred, and where (module target). Logs are written directly to decoupled console/file streams to isolate logs from runtime code interference.
* **JWT Management** – Tokens are encrypted with asymmetric algorithms (RS256) for tamper-proofing, never expose internal metadata or database IDs in the payload, and use cryptographically signed JSON Web Tokens.
* **Core Application Modules** – The Battery Registration Module (strict input validation and sanitization of scanned barcodes), State of Health (SoH) Module (secure compilation of the SoH formula to prevent unauthorized modification of execution parameters), and Battery Booking Module (mutex-locking mechanisms during allocation processing to eliminate race conditions and double-booking exploits).
* **Data Retention** – Defines how long different data types are kept: PWA local storage clears immediately upon logout or session expiration; telemetry and sensor data are retained 30–90 days; State of Health and availability data persist permanently until physical battery dispatch; system logs and audit trails are kept for 90 days; and operational/transactional data (scanned barcodes, profile configurations, UPS booking records) are retained for 1 to 5 years.

### Data Zones

This zone contains Probe's persistent storage and backup systems, communicating with the Trusted Application Zone over an encrypted (TLS 1.3) channel.

* **PostgreSQL Database** – Encrypted at rest, enforces least-privilege access, applies role-based access between modules, and has audit logging enabled.
* **Data Classification** – Data is categorized by sensitivity:
  * **Identity Data (Restricted):** Recycler name, email and password, device serial, password hashes, MFA codes.
  * **Operational Data (Internal use):** Booking status, timestamps, allocation assignments, diagnostic/SoH logs.
  * **Telemetry Data (Lower sensitivity):** Temperature, voltage, current readings, raw MQTT payloads, short retention.
* **Cache / Session Storage** – Uses Redis for session tokens, synced device data (10-day retention), and platform config cache (6-month retention).
* **Backup Storage** – Data is backed up to cloud storage (Supabase), providing redundancy beyond the primary database.

---

##  Password Security

User passwords are never stored as plain text.

Before a password is stored in the database, it is hashed using **bcrypt** through the Passlib library. During authentication, the provided password is compared against the stored hash rather than being stored or retrieved as the original password.

This reduces the risk of exposing user passwords if the database is compromised.

---

##  JWT Authentication

Probe uses **JSON Web Tokens (JWT)** to authenticate requests to protected API endpoints.

When a user successfully logs in through:

```http
POST /users/login
```

the backend generates a signed JWT containing information such as the user's ID and user type.

The token is configured using:

```text
JWT_SECRET_KEY
JWT_ALGORITHM=HS256
JWT_EXPIRE_DAYS=30
```

Protected requests include the token in the HTTP authorization header:

```http
Authorization: Bearer <access_token>
```

The backend validates the token before allowing access to protected resources.

### Authentication Flow

```text
User
  ↓
POST /users/login
  ↓
FastAPI Authentication
  ↓
Credentials Verified
  ↓
JWT Generated
  ↓
Frontend Stores Token
  ↓
Protected API Request
  ↓
JWT Validated
  ↓
Resource Access Granted
```

If the token is missing, invalid, or expired, the API returns an authentication error.

---

##  Role-Based Access Control

Probe uses the `user_type` field to control access to protected functionality.

The main user roles include:

* **ADMIN** – manages administrative resources and platform-level operations.
* **RECYCLER** – registers devices, manages batteries, and works with testing data.
* **UPS_COMPANY** – views available battery inventory and creates battery bookings.

Administrative endpoints use a dedicated authentication dependency that verifies:

```python
user_type == "ADMIN"
```

This prevents users with other roles from accessing admin-only resources.

### Role Access Flow

```text
Authenticated User
        ↓
   JWT Validated
        ↓
    user_type
        ↓
 ┌──────┼─────────────┐
 ↓      ↓             ↓
ADMIN  RECYCLER   UPS_COMPANY
 ↓      ↓             ↓
Admin  Battery/    Inventory/
Access Device      Booking
       Operations  Operations
```

---

##  CORS Protection

Probe uses **Cross-Origin Resource Sharing (CORS)** to control which frontend applications can communicate with the FastAPI backend.

Allowed origins are configured through the `CORS_ORIGINS` environment variable.

Development environments may include:

```text
http://localhost:3000
http://127.0.0.1:3000
https://probe-herckers-3325e295df63.herokuapp.com
```

The deployed frontend can also be added to the allowed origins.

Using an explicit list of origins prevents unrelated websites from making requests to the API.

Example configuration:

```text
CORS_ORIGINS=http://localhost:3000,https://your-production-frontend.com
```

Production deployments should use the actual frontend domain rather than allowing all origins with `*`.

---

##  Environment Variables and Secrets

Sensitive configuration values are kept outside the source code using environment variables.

Probe uses environment variables for values such as:

```text
DATABASE_URL
JWT_SECRET_KEY
JWT_ALGORITHM
JWT_EXPIRE_DAYS
CORS_ORIGINS
```

This prevents database credentials, authentication secrets, and deployment-specific configuration from being hardcoded in the application.

The `.env` file should not be committed to GitHub.

A `.gitignore` entry should therefore include:

```text
.env
.env.local
```

For deployed environments, these values are configured through the hosting platform's environment-variable settings.

---

##  API Protection

Protected API resources require authentication before sensitive operations can be performed.

Examples include:

```text
/users/
/devices/
/batteries/
/bookings/
/v1/sensor-readings/
```

The backend uses authentication dependencies to identify the current user before processing protected requests.

Authorization is then applied where specific roles are required.

This creates two levels of protection:

```text
Authentication
"Who are you?"
       ↓
JWT Validation
       ↓
Authorization
"What are you allowed to access?"
       ↓
Resource
```

---

##  Security Responsibilities

Security is applied across the different layers of the Probe system:

| Layer              | Security Measure                  |
| ------------------ | --------------------------------- |
| User Accounts      | bcrypt password hashing           |
| API Authentication | JWT bearer tokens                 |
| Authorization      | Role-based access control         |
| Frontend ↔ Backend | CORS origin restrictions          |
| Configuration      | Environment variables             |
| Database Access    | Authenticated backend access      |
| API Resources      | Protected routes and dependencies |

Together, these measures provide the baseline security required for Probe's authentication, battery management, telemetry, and booking workflows.