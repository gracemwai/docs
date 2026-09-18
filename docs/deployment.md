#  Deployment

Probe uses **Heroku** for backend deployment and **Vercel** for frontend deployment. The PostgreSQL database is provisioned through Heroku Postgres, while environment variables and secrets are managed through the respective hosting platforms.

##  Backend Deployment — Heroku

The FastAPI backend is deployed to **Heroku**. Heroku hosts the API application and provides the PostgreSQL database through the Heroku Postgres add-on.

### Required Configuration Variables

The following configuration variables must be available in the Heroku application settings under **Settings → Config Vars**:

| Variable          | Purpose                                                  |
| ----------------- | -------------------------------------------------------- |
| `DATABASE_URL`    | Connection string for the PostgreSQL production database |
| `JWT_SECRET_KEY`  | Secret key used to sign JWT authentication tokens        |
| `JWT_ALGORITHM`   | JWT signing algorithm, configured as `HS256`             |
| `JWT_EXPIRE_DAYS` | Number of days before an authentication token expires    |
| `CORS_ORIGINS`    | Comma-separated list of approved frontend origins        |

`DATABASE_URL` is provisioned automatically when the Heroku Postgres add-on is attached to the application.

### Deploying the Backend

When deploying through Git, the production branch can be pushed directly to Heroku:

```bash
git push heroku main
```

Heroku then builds and releases the updated application.

Changes to **configuration variables** take effect automatically because Heroku restarts the application dyno. Changes to the **application code** require a new deployment before they become available in production.

### Database Migrations

When database models are changed and a new Alembic migration has been created, the migration must be applied to the production database:

```bash
alembic upgrade head
```

This ensures that the production database schema matches the current backend application.

---

##  Frontend Deployment — Vercel

The Probe web dashboard is deployed to **Vercel** and connected to the frontend repository.

### Required Environment Variable

The frontend requires the following environment variable:

```text
NEXT_PUBLIC_API_URL=https://<production-backend-url>
```

The value should contain the complete HTTPS URL of the deployed backend API.

The production API URL should:

* Use `https://`
* Not contain a trailing `/`
* Point to the currently deployed backend
* Be configured for the **Production** environment in Vercel

After changing an environment variable in Vercel, the frontend must be redeployed for the change to be applied to the deployed application.

---

##  Production Release Checklist

Before confirming a production release, verify the following:

### Backend

* [ ] `DATABASE_URL` is configured correctly.
* [ ] `JWT_SECRET_KEY` is configured and kept private.
* [ ] `JWT_ALGORITHM` is set correctly.
* [ ] `JWT_EXPIRE_DAYS` is configured.
* [ ] `CORS_ORIGINS` contains the current production frontend URL.
* [ ] New Alembic migrations have been applied to the production database.
* [ ] The deployed API documentation is accessible through `/docs`.
* [ ] Authentication endpoints respond correctly.

### Frontend

* [ ] `NEXT_PUBLIC_API_URL` points to the current production backend.
* [ ] The API URL uses HTTPS.
* [ ] The API URL has no trailing slash.
* [ ] The frontend has been redeployed after environment variable changes.
* [ ] Login and authentication flows work correctly.
* [ ] Inventory data loads successfully.
* [ ] Booking functionality works correctly.
* [ ] No unexpected errors appear in the browser console.

### Final Verification

A deployment is considered successful when the frontend can communicate with the production API, authenticated users can access the appropriate resources, database operations complete successfully, and the main Probe workflows operate without errors.
