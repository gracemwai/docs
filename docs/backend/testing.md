#  Testing

Testing is used to verify that Probe's backend endpoints, business logic, authentication, and database interactions behave as expected.

## API Testing

API endpoints are tested using **Postman** and FastAPI's interactive Swagger documentation.

Testing covers:

* Successful requests
* Invalid request data
* Authentication failures
* Authorization failures
* Missing resources
* Duplicate resources
* Boundary and edge cases
* Response validation
* Booking workflow validation

## Automated Tests

Backend tests can be organized around the main application layers:

| Test Area          | Purpose                                         |
| ------------------ | ----------------------------------------------- |
| **Models**         | Verify database models and relationships        |
| **Schemas**        | Verify request and response validation          |
| **Services**       | Verify business rules                           |
| **Repositories**   | Verify database operations                      |
| **Routers**        | Verify API behavior and status codes            |
| **Authentication** | Verify JWT authentication and role-based access |

## API Validation

Each endpoint should be tested against expected HTTP status codes.

Common responses include:

| Status                      | Meaning                                           |
| --------------------------- | ------------------------------------------------- |
| `200 OK`                    | Request completed successfully                    |
| `201 Created`               | Resource created successfully                     |
| `400 Bad Request`           | Invalid request                                   |
| `401 Unauthorized`          | Authentication missing or invalid                 |
| `403 Forbidden`             | User does not have permission                     |
| `404 Not Found`             | Requested resource does not exist                 |
| `409 Conflict`              | Request conflicts with the current resource state |
| `500 Internal Server Error` | Unexpected server error                           |

## Postman Testing

Postman is used to test complete API workflows.

Typical workflows include:

```text
Register User
      ↓
Login
      ↓
Receive JWT
      ↓
Authorize Request
      ↓
Create Battery
      ↓
Retrieve Battery
      ↓
Create Booking
      ↓
Update Booking
```

For IoT-related functionality, sensor-reading endpoints can also be tested by submitting representative telemetry payloads and validating the returned response.

## Swagger Testing

FastAPI's automatically generated Swagger UI can be used for quick endpoint verification.

Run the backend:

```bash
uvicorn main:app --reload
```

Then open:

```text
http://localhost:8000/docs
```

Developers can authenticate and execute API requests directly from the Swagger interface.
