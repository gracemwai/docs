#  API Reference

The Probe backend exposes a REST API through FastAPI.

Interactive API documentation is automatically generated using OpenAPI and can be accessed through Swagger UI.

## API Documentation

When running the backend locally, open:

```text
http://localhost:8000/docs
```

The Swagger interface allows developers to:

* View available endpoints.
* Inspect request and response schemas.
* Authenticate using a JWT.
* Send requests directly to the API.
* Review response status codes.

## API Resources

Probe organizes its API into the following resource groups:

| Resource            | Prefix                | Purpose                            |
| ------------------- | --------------------- | ---------------------------------- |
| **Users**           | `/users`              | Authentication and user management |
| **Devices**         | `/devices`            | Testing device management          |
| **Batteries**       | `/batteries`          | Battery registration and inventory |
| **Sensor Readings** | `/v1/sensor-readings` | Telemetry and battery test data    |
| **Bookings**        | `/bookings`           | Battery booking workflow           |

---

## Users

| Method   | Endpoint                 | Purpose                |
| -------- | ------------------------ | ---------------------- |
| `POST`   | `/users/`                | Register a user        |
| `POST`   | `/users/login`           | Authenticate a user    |
| `POST`   | `/users/forgot-password` | Request password reset |
| `POST`   | `/users/reset-password`  | Reset password         |
| `GET`    | `/users/`                | List users             |
| `GET`    | `/users/{user_id}`       | Retrieve a user        |
| `PATCH`  | `/users/{user_id}`       | Update a user          |
| `DELETE` | `/users/{user_id}`       | Delete a user          |

---

## Devices

| Method   | Endpoint                                    | Purpose                            |
| -------- | ------------------------------------------- | ---------------------------------- |
| `GET`    | `/devices/`                                 | List devices                       |
| `GET`    | `/devices/{device_id}`                      | Retrieve a device                  |
| `GET`    | `/devices/{device_id}/batteries`            | Get batteries assigned to a device |
| `GET`    | `/devices/by-serial-number/{serial_number}` | Find a device by serial number     |
| `POST`   | `/devices/`                                 | Register a device                  |
| `PATCH`  | `/devices/{device_id}`                      | Update a device                    |
| `DELETE` | `/devices/{device_id}`                      | Delete a device                    |

---

## Batteries

| Method   | Endpoint                       | Purpose                                  |
| -------- | ------------------------------ | ---------------------------------------- |
| `GET`    | `/batteries/reference-library` | Retrieve approved battery reference data |
| `GET`    | `/batteries/`                  | List batteries                           |
| `GET`    | `/batteries/{battery_id}`      | Retrieve a battery                       |
| `POST`   | `/batteries/`                  | Register a battery                       |
| `PATCH`  | `/batteries/{battery_id}`      | Update a battery                         |
| `DELETE` | `/batteries/{battery_id}`      | Delete a battery                         |

---

## Sensor Readings

| Method   | Endpoint                                   | Purpose                    |
| -------- | ------------------------------------------ | -------------------------- |
| `POST`   | `/v1/sensor-readings/`                     | Record telemetry           |
| `GET`    | `/v1/sensor-readings/{sensor_reading_id}`  | Retrieve a sensor reading  |
| `PATCH`  | `/v1/sensor-readings/{sensor_reading_id}`  | Update a sensor reading    |
| `GET`    | `/v1/sensor-readings/device/{device_id}`   | Get readings for a device  |
| `GET`    | `/v1/sensor-readings/battery/{battery_id}` | Get readings for a battery |
| `DELETE` | `/v1/sensor-readings/{sensor_reading_id}`  | Delete a sensor reading    |

---

## Bookings

| Method   | Endpoint                 | Purpose            |
| -------- | ------------------------ | ------------------ |
| `GET`    | `/bookings/`             | List bookings      |
| `GET`    | `/bookings/{booking_id}` | Retrieve a booking |
| `POST`   | `/bookings/`             | Create a booking   |
| `PATCH`  | `/bookings/{booking_id}` | Update a booking   |
| `DELETE` | `/bookings/{booking_id}` | Delete a booking   |

## Authentication

Protected endpoints require a valid JWT.

Include the token in the request header:

```http
Authorization: Bearer <token>
```

For the complete request and response schemas, use the interactive Swagger documentation.
