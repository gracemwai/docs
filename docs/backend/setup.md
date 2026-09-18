#  Backend Setup

This section explains how to configure the Probe backend for local development.

## Prerequisites

Before setting up the backend, ensure the following are installed:

* Python
* PostgreSQL
* Git
* `uv`
* A code editor such as VS Code

## Install Dependencies

Navigate to the backend project:

```bash
cd HERckers_Backend
```

Create a virtual environment:

```bash
uv venv env
```

Activate the environment:

```bash
source env/bin/activate
```

Install the project dependencies:

```bash
pip install -r requirements.txt
```

## Environment Variables

Create a `.env` file in the backend project root.

```env
DATABASE_URL=postgresql://<user>:<password>@localhost:5432/probe-db

JWT_SECRET_KEY=<a-long-random-secret>
JWT_ALGORITHM=HS256
JWT_EXPIRE_DAYS=30

CORS_ORIGINS=http://localhost:3000,http://127.0.0.1:3000,https://herckersdashboard-nine.vercel.app
```

### Environment Variable Reference

| Variable          | Purpose                                    |
| ----------------- | ------------------------------------------ |
| `DATABASE_URL`    | PostgreSQL database connection string      |
| `JWT_SECRET_KEY`  | Secret used to sign JWT tokens             |
| `JWT_ALGORITHM`   | JWT signing algorithm                      |
| `JWT_EXPIRE_DAYS` | Token expiration period                    |
| `CORS_ORIGINS`    | Frontend origins allowed to access the API |

> **Security:** Never commit `.env` files or secret values to source control.

## Database Migration

Apply the latest database migrations using Alembic:

```bash
alembic upgrade head
```

## Run the Backend

Start the FastAPI development server:

```bash
uvicorn main:app --reload
```

The API will normally be available at:

```text
http://localhost:8000
```

## Interactive API Documentation

FastAPI automatically generates interactive API documentation.

Open:

```text
http://localhost:8000/docs
```

The Swagger UI provides access to the available API routers, including:

* Users
* Devices
* Batteries
* Sensor Readings
* Bookings

A successful request to an authenticated endpoint confirms that the API and authentication pipeline are functioning correctly.
