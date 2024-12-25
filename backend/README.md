# User Management API

## Description
This API provides endpoints for user registration, login, profile management, and logout. It includes input validation, secure password handling, and token-based authentication using JSON Web Tokens (JWT).

---

## Endpoints

### 1. POST `/register`

#### Description
Registers a new user by validating input data and storing the user information in the database. A JWT is issued upon successful registration.

#### Request Body
| Field      | Type   | Required | Validation Notes                                  |
|------------|--------|----------|--------------------------------------------------|
| `email`    | String | Yes      | Must be a valid email address and unique         |
| `password` | String | Yes      | Minimum length of 3 characters                   |

#### Example Request
```json
{
  "email": "user@example.com",
  "password": "securePassword123"
}
```

#### Responses
- **Success Response**
  - **Status Code:** `201 Created`
  - **Content:**
    ```json
    {
      "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
      "user": {
        "_id": "64fca324f84b23d28e041ab",
        "email": "user@example.com"
      }
    }
    ```

- **Validation Error**
  - **Status Code:** `400 Bad Request`
  - **Content:**
    ```json
    {
      "errors": [
        {
          "msg": "Password must be at least 3 characters long",
          "param": "password",
          "location": "body"
        }
      ]
    }
    ```

- **User Already Exists**
  - **Status Code:** `400 Bad Request`
  - **Content:**
    ```json
    {
      "message": "User already exists"
    }
    ```

---

### 2. POST `/login`

#### Description
Authenticates a user by validating their email and password. Returns a JWT upon successful authentication.

#### Request Body
| Field      | Type   | Required | Validation Notes                  |
|------------|--------|----------|------------------------------------|
| `email`    | String | Yes      | Must be a valid email address      |
| `password` | String | Yes      | Minimum length of 3 characters     |

#### Example Request
```json
{
  "email": "user@example.com",
  "password": "securePassword123"
}
```

#### Responses
- **Success Response**
  - **Status Code:** `200 OK`
  - **Content:**
    ```json
    {
      "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
      "user": {
        "_id": "64fca324f84b23d28e041ab",
        "email": "user@example.com"
      }
    }
    ```

- **Invalid Credentials**
  - **Status Code:** `401 Unauthorized`
  - **Content:**
    ```json
    {
      "errors": "Invalid credentials"
    }
    ```

- **Validation Error**
  - **Status Code:** `400 Bad Request`
  - **Content:**
    ```json
    {
      "errors": [
        {
          "msg": "Invalid Email",
          "param": "email",
          "location": "body"
        }
      ]
    }
    ```

---

### 3. GET `/profile`

#### Description
Fetches the profile information of the authenticated user. Requires a valid JWT token in the Authorization header or as a cookie.

#### Responses
- **Success Response**
  - **Status Code:** `200 OK`
  - **Content:**
    ```json
    {
      "user": {
        "_id": "64fca324f84b23d28e041ab",
        "email": "user@example.com"
      }
    }
    ```

- **Unauthorized Access**
  - **Status Code:** `401 Unauthorized`
  - **Content:**
    ```json
    {
      "error": "Unauthorized User"
    }
    ```

---

### 4. GET `/logout`

#### Description
Logs out the user by invalidating their JWT token via a blacklist mechanism.

#### Responses
- **Success Response**
  - **Status Code:** `200 OK`
  - **Content:**
    ```json
    {
      "message": "Logged out successfully"
    }
    ```

- **Unauthorized Access**
  - **Status Code:** `401 Unauthorized`
  - **Content:**
    ```json
    {
      "error": "Unauthorized User"
    }
    ```

---

## Middleware

### Authentication Middleware (`auth.middleware.js`)
Ensures only authorized users can access protected endpoints.

#### Functionality
- Verifies the presence and validity of the JWT token.
- Checks if the token is blacklisted.
- Attaches the decoded user information to the request object for further use.

---

## Implementation Notes
1. Passwords are hashed securely before being stored.
2. Tokens are signed using `jsonwebtoken` and stored in cookies.
3. Input validation is implemented using `express-validator`.
4. Redis is used for token blacklisting to ensure secure logout.

---

## Prerequisites
- Environment variable `JWT_SECRET` must be set for token signing.
- Redis and MongoDB must be configured and running for proper functionality.

---

## Author
This documentation describes the `/register`, `/login`, `/profile`, and `/logout` endpoints of the User Management API.

