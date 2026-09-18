#  Deployment

The Probe backend is deployed as a FastAPI application and requires access to a PostgreSQL database and its required environment variables.

## Deployment Requirements

The deployment environment must provide:

* Python runtime
* PostgreSQL database
* Application environment variables
* JWT secret
* CORS configuration
* Required Python dependencies

## Environment Configuration

Production secrets must be configured through the deployment platform's environment-variable system.

Sensitive values should never be committed to GitHub.

Required configuration includes:

```env
DATABASE_URL=<production-database-url>
JWT_SECRET_KEY=<production-secret>
JWT_ALGORITHM=HS256
JWT_EXPIRE_DAYS=30
CORS_ORIGINS=<allowed-frontend-origins>
```

## Deployment Process

The general deployment workflow is:

```text
Developer
   ↓
Feature Branch
   ↓
Testing
   ↓
Pull Request
   ↓
Code Review
   ↓
main
   ↓
Deployment Platform
   ↓
Production API
```

Before deployment:

1. Confirm that tests pass.
2. Confirm that required environment variables are configured.
3. Apply database migrations.
4. Deploy the latest backend code.
5. Verify the API is running.
6. Test critical endpoints.

## Database Migration

After deploying a version that contains database schema changes, run:

```bash
alembic upgrade head
```

This applies pending migrations to the production database.

## API Verification

After deployment, verify the API through its deployed Swagger documentation:

```text
https://<your-deployed-api>/docs
```

Verify at minimum:

* Authentication
* User endpoints
* Battery endpoints
* Device endpoints
* Sensor-reading endpoints
* Booking endpoints

## Security

Production deployments must follow these practices:

* Never commit `.env` files.
* Never expose JWT secrets.
* Restrict CORS to trusted frontend origins.
* Use HTTPS for production API communication.
* Keep dependencies updated.
* Apply database migrations carefully.
