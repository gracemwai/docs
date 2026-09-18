#  Backend

The Probe backend provides the core API and business logic that connects the platform's frontend applications, battery inventory, booking workflows, and IoT testing system.

It is built with **FastAPI** and uses **PostgreSQL** for persistent data storage. SQLAlchemy is used as the Object-Relational Mapping (ORM) layer, while Pydantic is used for request validation and response schemas.

The backend is responsible for authentication, user management, battery and device management, sensor data, inventory, and battery booking workflows.

## Backend Technology Stack

| Component             | Technology        | Purpose                          |
| --------------------- | ----------------- | -------------------------------- |
| **Framework**         | FastAPI           | REST API and backend application |
| **Language**          | Python            | Backend development              |
| **Database**          | PostgreSQL        | Persistent data storage          |
| **ORM**               | SQLAlchemy        | Database models and queries      |
| **Validation**        | Pydantic          | Request and response validation  |
| **Authentication**    | JWT               | Secure user authentication       |
| **Password Hashing**  | bcrypt            | Secure password storage          |
| **Migration**         | Alembic           | Database schema migrations       |
| **IoT Communication** | MQTT              | Sensor telemetry communication   |
| **API Documentation** | Swagger / OpenAPI | Interactive API documentation    |

## Backend Responsibilities

The backend provides the following major capabilities:

* **User authentication and authorization**
* **Role-based access control**
* **User management**
* **Battery registration and inventory management**
* **Testing device management**
* **Sensor-reading management**
* **Battery State of Health data processing**
* **Battery booking management**
* **Database persistence**
* **API access for the web and mobile applications**

## Backend Architecture

Probe follows a layered architecture in which responsibilities are separated between database models, repositories, business services, schemas, and API routers.

```text
                    Client Applications
                           │
                           ▼
                      API Routers
                           │
                           ▼
                       Services
                           │
                           ▼
                     Repositories
                           │
                           ▼
                    SQLAlchemy ORM
                           │
                           ▼
                      PostgreSQL
```

This separation allows individual parts of the backend to be developed, tested, and maintained independently.

## Main API Resources

The backend exposes five major resource groups:

| Resource            | Prefix                | Purpose                                |
| ------------------- | --------------------- | -------------------------------------- |
| **Users**           | `/users`              | Authentication and user management     |
| **Devices**         | `/devices`            | Testing device management              |
| **Batteries**       | `/batteries`          | Battery asset and inventory management |
| **Sensor Readings** | `/v1/sensor-readings` | Battery telemetry and test results     |
| **Bookings**        | `/bookings`           | Battery reservation workflow           |

The complete endpoint reference is documented in the **API Reference** section.
