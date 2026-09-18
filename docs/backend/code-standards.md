### Backend — Python

The FastAPI backend follows standard Python naming conventions.

| Element         | Convention   | Example             |
| --------------- | ------------ | ------------------- |
| Variables       | `snake_case` | `battery_id`        |
| Functions       | `snake_case` | `get_battery()`     |
| Files / Modules | `snake_case` | `battery_router.py` |
| Classes         | `PascalCase` | `BatteryService`    |
| Schemas         | `PascalCase` | `BatteryCreate`     |
| Enums           | `PascalCase` | `BookingStatus`     |

##  Folder & File Structure

The backend uses a layered architecture where each layer has a defined responsibility.

```text
probe/
├── models/
├── repositories/
├── services/
├── schemas/
├── routers/
├── database.py
└── main.py
```

| Layer           | Responsibility                                |
| --------------- | --------------------------------------------- |
| `models/`       | SQLAlchemy database models                    |
| `repositories/` | Direct database operations                    |
| `services/`     | Business logic                                |
| `schemas/`      | Pydantic request and response models          |
| `routers/`      | FastAPI routes and HTTP handling              |
| `database.py`   | Database connection and session configuration |
| `main.py`       | FastAPI application configuration             |

New functionality should follow the existing structure.

For example:

```text
Battery Router
      ↓
Battery Service
      ↓
Battery Repository
      ↓
PostgreSQL
```

This keeps routing, business logic, and database operations separated.

---

##  Error Handling

The backend uses explicit `HTTPException` responses for expected API errors.

```python
raise HTTPException(
    status_code=404,
    detail="Battery not found"
)
```

Common status codes include:

| Status | Meaning                            |
| ------ | ---------------------------------- |
| `400`  | Invalid request or input           |
| `401`  | Authentication required or invalid |
| `403`  | Insufficient permissions           |
| `404`  | Resource not found                 |
| `409`  | Resource conflict                  |
| `500`  | Unexpected server error            |

Error messages should be clear enough to support debugging without exposing sensitive information.

##  Logging

Backend errors and deployment issues can be investigated through application logs.

For the deployed backend:

```bash
heroku logs --tail
```

During frontend development, errors can be logged using:

```typescript
console.error()
```

Logs must not contain sensitive information such as:

* Passwords
* JWT tokens
* Database credentials
* API secrets
* Other private configuration values

##  Code Organization Principles

When contributing to Probe:

* Follow the existing project structure.
* Use the appropriate naming convention.
* Keep business logic inside service layers.
* Keep database operations inside repositories.
* Keep API routes inside routers.
* Use schemas for request and response validation.
* Handle expected errors explicitly.
* Reuse existing components and utilities where appropriate.
* Keep changes focused on the feature being implemented.
* Never commit secrets or credentials to the repository.