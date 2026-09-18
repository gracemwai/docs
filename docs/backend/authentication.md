#  Authentication

Probe uses **JWT (JSON Web Token) authentication** to secure protected API endpoints.

Users authenticate with their email and password. After successful authentication, the backend issues a signed access token that must be included in subsequent requests to protected resources.

## Authentication Flow

```text
User
  ↓
POST /users/login
  ↓
FastAPI validates credentials
  ↓
JWT access token issued
  ↓
Client stores token
  ↓
Protected API request
  ↓
JWT verified
  ↓
Request authorized
```

## User Registration

A new user registers through:

```http
POST /users/
```

The backend validates the submitted information and securely hashes the user's password before storing the user record.

## Login

Users authenticate through:

```http
POST /users/login
```

When authentication succeeds, the backend returns a JWT containing information required to identify the authenticated user.

The token includes:

* `sub` — User ID
* `user_type` — User role
* `exp` — Token expiration time

## Sending the Token

Protected requests include the JWT in the HTTP Authorization header:

```http
Authorization: Bearer <token>
```

The backend uses the `get_current_user` dependency to:

1. Extract the token.
2. Verify the token signature.
3. Check token expiration.
4. Retrieve the corresponding user.
5. Allow the request to continue when authentication succeeds.

## Role-Based Authorization

Probe uses user roles to control access to protected resources.

The main roles are:

| Role            | Access                                                       |
| --------------- | ------------------------------------------------------------ |
| **ADMIN**       | Administrative and user-management operations                |
| **RECYCLER**    | Battery registration, testing data, and inventory management |
| **UPS_COMPANY** | Available battery inventory and booking workflows            |

Admin-only endpoints use an additional authorization dependency to verify that the authenticated user's role is `ADMIN`.

## Password Security

User passwords are not stored as plain text.

Passwords are hashed using **bcrypt** before being persisted in the database.

## Token Expiration

JWT expiration is configured using the `JWT_EXPIRE_DAYS` environment variable.

Example:

```env
JWT_EXPIRE_DAYS=30
```

This allows the token lifetime to be changed through configuration without modifying application code.

> **Security:** Never expose the JWT secret key or commit it to source control. Store secrets in environment variables.
