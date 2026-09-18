#  Database

Probe uses **PostgreSQL** as its primary relational database.

The backend uses **SQLAlchemy ORM** to interact with the database, while **Alembic** manages database schema migrations.

## Entity Relationship Diagram

The database relationships are represented in the Entity Relationship Diagram below.

![Probe Database ERD](/images/images/Entity-Relationship-Diagram.jpg)

> The ERD is generated from the PostgreSQL database schema and represents the relationships between Probe's core entities.

## Database Entities

Probe's database is organized around the following core entities:

* **Users** — platform user accounts and roles.
* **Devices** — physical testing devices associated with recyclers.
* **Batteries** — battery assets registered on the platform.
* **Sensor Readings** — measurements collected during battery testing.
* **Bookings** — requests to reserve available battery stock.

## Users

The `users` table stores accounts used to access the Probe platform.

| Column                   | Type     | Constraints     |
| ------------------------ | -------- | --------------- |
| `user_id`                | UUID     | Primary Key     |
| `first_name`             | String   | NOT NULL        |
| `last_name`              | String   | NOT NULL        |
| `email`                  | String   | UNIQUE, INDEXED |
| `password_hash`          | String   | NOT NULL        |
| `user_type`              | Enum     | NOT NULL        |
| `company_name`           | String   | NOT NULL        |
| `reset_token`            | String   | Nullable        |
| `reset_token_expires_at` | DateTime | Nullable        |
| `created_at`             | DateTime | NOT NULL        |
| `updated_at`             | DateTime | NOT NULL        |

## Devices

The `devices` table stores physical testing devices associated with recyclers.

| Column          | Type     | Constraints                   |
| --------------- | -------- | ----------------------------- |
| `device_id`     | UUID     | Primary Key                   |
| `serial_number` | String   | UNIQUE, NOT NULL              |
| `recycler_id`   | UUID     | Foreign Key → `users.user_id` |
| `error_code`    | String   | Nullable                      |
| `channel`       | String   | NOT NULL                      |
| `description`   | String   | Nullable                      |
| `status`        | Enum     | NOT NULL                      |
| `created_at`    | DateTime | NOT NULL                      |
| `updated_at`    | DateTime | NOT NULL                      |

## Batteries

The `batteries` table stores registered battery assets.

| Column        | Type     | Constraints                       |
| ------------- | -------- | --------------------------------- |
| `battery_id`  | UUID     | Primary Key                       |
| `chemistry`   | String   | NOT NULL                          |
| `recycler_id` | UUID     | Foreign Key → `users.user_id`     |
| `device_id`   | UUID     | Foreign Key → `devices.device_id` |
| `quantity`    | Integer  | NOT NULL, ≥ 1                     |
| `created_at`  | DateTime | NOT NULL                          |
| `updated_at`  | DateTime | NOT NULL                          |

### Battery Relationships

A battery is associated with:

* A recycler/user.
* A testing device.
* Sensor readings.
* Booking records.

## Sensor Readings

The `sensor_readings` table stores measurements collected during battery testing.

| Column              | Type     | Constraints                          |
| ------------------- | -------- | ------------------------------------ |
| `sensor_reading_id` | UUID     | Primary Key                          |
| `device_id`         | UUID     | Foreign Key → `devices.device_id`    |
| `battery_id`        | UUID     | Foreign Key → `batteries.battery_id` |
| `temperature`       | Float    | NOT NULL                             |
| `voltage`           | Float    | NOT NULL                             |
| `current`           | Float    | NOT NULL                             |
| `state_of_health`   | Float    | NOT NULL                             |
| `category`          | String   | NOT NULL                             |
| `status`            | Enum     | NOT NULL                             |
| `created_at`        | DateTime | NOT NULL                             |
| `updated_at`        | DateTime | NOT NULL                             |

The voltage measurement represents the load voltage (`v_load`). The API may also receive resting voltage (`v_rest`) as part of the battery health calculation.

## Bookings

The `bookings` table stores requests made to reserve available battery stock.

| Column       | Type     | Constraints                          |
| ------------ | -------- | ------------------------------------ |
| `booking_id` | UUID     | Primary Key                          |
| `user_id`    | UUID     | Foreign Key → `users.user_id`        |
| `battery_id` | UUID     | Foreign Key → `batteries.battery_id` |
| `status`     | Enum     | NOT NULL                             |
| `quantity`   | Integer  | NOT NULL, ≥ 1                        |
| `created_at` | DateTime | NOT NULL                             |
| `updated_at` | DateTime | NOT NULL                             |

## Database Migrations

Alembic is used to manage database schema changes.

Apply migrations using:

```bash
alembic upgrade head
```

This ensures the local database schema matches the migration history used by the application.
