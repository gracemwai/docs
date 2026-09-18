#  Testing and QA

Testing and quality assurance for Probe focused on verifying the reliability of the backend API, authentication and authorization, inventory management, booking workflows, and communication between the frontend and backend.

Testing was performed using **FastAPI Swagger UI** and **Postman**. Swagger UI was primarily used for interactive endpoint verification, while Postman was used for repeatable API requests, response assertions, authentication checks, and scenario-based testing.

At the current stage of development, there is no formal automated unit-test suite for the backend or frontend. The testing documented here therefore focuses on manual functional verification and Postman-based API regression and integration checks.

---

##  Testing Approach

Testing was performed across the following areas:

### Backend/API Testing

API endpoints were tested using the FastAPI Swagger UI available through `/docs` and through Postman.

The following functionality was verified:

* User registration and login
* JWT authentication
* Protected API endpoints
* User management
* Device management
* Battery management
* Sensor-reading submission and retrieval
* Booking creation and management
* API validation and error responses

### Authentication and Authorization Testing

Authentication testing verified that:

* Valid credentials produce a JWT access token.
* Protected endpoints reject requests without authentication.
* Invalid or expired tokens are rejected.
* Users with insufficient permissions receive a `403 Forbidden` response.
* Admin-only operations are restricted to users with the `ADMIN` role.

### Configuration and Integration Testing

Configuration and integration testing was used to identify issues affecting communication between system components.

This included testing:

* CORS configuration
* Environment variables
* JWT configuration
* Frontend-to-backend communication
* Database connectivity
* API response handling
* Production and development API URLs

---

##  Manual Verification via Swagger UI

FastAPI provides an interactive Swagger UI through the `/docs` endpoint. This was one of the primary tools used to manually verify backend functionality during development.

The general verification process was:

1. Open the running backend's `/docs` endpoint.
2. Create a user using `POST /users/` where required.
3. Authenticate using `POST /users/login`.
4. Obtain the JWT access token returned by the login request.
5. Select **Authorize** in Swagger UI.
6. Provide the bearer token.
7. Execute protected endpoints.
8. Verify the returned HTTP status code.
9. Inspect the response body for the expected data.
10. Repeat the process for other resources and workflows.
11. Test unauthenticated and unauthorized requests to verify expected `401` and `403` responses.

This process provided direct verification of the API routes, authentication pipeline, authorization rules, request validation, and database interactions.

---

##  Postman API Testing

Postman was used for repeatable API testing and regression checks.

The Postman test collection included assertions for:

* HTTP status codes
* Response time
* Response content type
* Authentication behaviour
* Validation errors
* Resource creation
* Generated user IDs
* Protected endpoint access
* Error handling

### Response Assertions

The following checks were used to identify unexpected API behaviour:

```javascript
pm.test("Response time is under 2000ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(2000);
});

pm.test("Server did not return a 5xx error", function () {
    pm.expect(pm.response.code).to.not.be.oneOf([500, 502, 503, 504]);
});

if (pm.response.code !== 204) {
    pm.test("Response has application/json content type", function () {
        pm.expect(
            pm.response.headers.get("Content-Type")
        ).to.include("application/json");
    });
}
```

These assertions provide a basic contract check for API responses and help identify unexpected server failures or malformed responses.

---

##  Regression Testing Results

Postman collection runs were used to verify that previously implemented API functionality continued to behave as expected after changes.

### Successful Regression Run

A successful test run produced the following results:

| Metric                | Result |
| --------------------- | -----: |
| Duration              | 845 ms |
| Total Assertions      |     24 |
| Passed                |     24 |
| Failed                |      0 |
| Average Response Time |   6 ms |

The successful run verified request sequences across the configured Probe environment.

The tests also confirmed that:

* Invalid request structures were handled with appropriate `422` responses.
* Missing authentication tokens returned `401 Unauthorized`.
* User creation generated valid `user_id` values.
* API responses followed the expected structure.

### Staged Regression Run

An additional staged run was used to identify issues in the development environment.

| Metric                | Result |
| --------------------- | -----: |
| Duration              | 842 ms |
| Total Checks          |      6 |
| Passed                |      0 |
| Failed                |      6 |
| Average Response Time |  51 ms |

The main logged issues included:

* `POST /users/login` returned an unexpected `401 Unauthorized`.
* `POST /users/` encountered an account-creation parameter mismatch.

These failures were useful during development because they identified authentication and request-data issues that required investigation before the affected workflows could be considered stable.

---

##  Scenario-Based Boundary Testing

Postman pre-request scripts were used to dynamically modify request payloads for different test scenarios.

This allowed the same API workflow to be tested against valid and invalid input conditions without manually creating a separate request for every scenario.

Example scenarios included:

* Missing password
* Malformed email address
* Missing required fields
* Invalid authentication credentials
* Unauthorized access
* Invalid resource identifiers

Example pre-request logic:

```javascript
switch (activeScenario) {

    case "MISSING_PASSWORD":
        pm.request.body.update(JSON.stringify({
            email: pm.environment.get("email")
        }));
        break;

    case "MALFORMED_EMAIL":
        pm.request.body.update(JSON.stringify({
            email: "not-an-email",
            password: "SecurePass123"
        }));
        break;
}
```

The API was then expected to reject invalid requests with an appropriate validation or authentication response.

---

##  Error and Validation Testing

Error handling was tested by intentionally submitting invalid requests and verifying that the API returned meaningful HTTP responses.

Key cases included:

| Test Case                    | Expected Response           |
| ---------------------------- | --------------------------- |
| Missing authentication token | `401 Unauthorized`          |
| Invalid JWT                  | `401 Unauthorized`          |
| Insufficient permissions     | `403 Forbidden`             |
| Invalid resource ID          | `404 Not Found`             |
| Invalid request data         | `422 Unprocessable Entity`  |
| Server-side failure          | `500 Internal Server Error` |

These tests helped verify that invalid requests were rejected safely rather than producing unexpected successful responses.

---

##  Frontend Integration Verification

The frontend dashboard was tested against the deployed FastAPI backend to verify that the two applications could communicate correctly.

The verification included:

* User authentication
* JWT transmission
* Loading inventory data
* Device information retrieval
* Battery information retrieval
* Booking requests
* Handling API errors
* CORS communication between the deployed frontend and backend

Successful integration requires the frontend `NEXT_PUBLIC_API_URL` and backend `CORS_ORIGINS` configuration to point to the correct deployed services.

---

##  Current Testing Status

Probe currently relies on a combination of manual API verification and Postman-based automated assertions.

| Area                          | Current Status      |
| ----------------------------- | ------------------- |
| Swagger API verification      | Implemented         |
| Postman API testing           | Implemented         |
| Authentication testing        | Implemented         |
| Authorization testing         | Implemented         |
| Boundary/negative testing     | Implemented         |
| API regression checks         | Implemented         |
| Frontend integration testing  | Manually verified   |
| Backend unit-test suite       | Not yet established |
| Frontend automated test suite | Not yet established |

Future testing can extend the current approach by introducing automated backend unit and integration tests, frontend component tests, and continuous test execution as part of the deployment pipeline.
