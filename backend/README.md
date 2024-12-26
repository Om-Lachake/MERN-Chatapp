# User Management API

## Description

This API provides endpoints for user registration, login, profile management, and logout. It includes input validation, secure password handling, and token-based authentication using JSON Web Tokens (JWT).

---

## Endpoints

### 1. POST `/register`

#### Description

Registers a new user by validating input data and storing the user information in the database. A JWT is issued upon successful registration.

#### Request Body

| Field      | Type   | Required | Validation Notes                         |
| ---------- | ------ | -------- | ---------------------------------------- |
| `email`    | String | Yes      | Must be a valid email address and unique |
| `password` | String | Yes      | Minimum length of 3 characters           |

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

| Field      | Type   | Required | Validation Notes               |
| ---------- | ------ | -------- | ------------------------------ |
| `email`    | String | Yes      | Must be a valid email address  |
| `password` | String | Yes      | Minimum length of 3 characters |

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

# Project Management API

This repository contains the backend code for a Project Management API built using Node.js, Express, and Mongoose. The API allows authenticated users to manage projects, including creating projects, adding users, and updating project details.

---

## Features

- **Create a Project**: Authenticated users can create new projects.
- **View All Projects**: Retrieve all projects associated with a specific user.
- **Add Users to Project**: Add users to a project by their IDs.
- **Get Project Details**: Fetch details of a project using its ID.
- **Update File Tree**: Update the file tree structure of a specific project.

---

## Endpoints

### 1. Create a Project

- **Method**: POST
- **URL**: `/create`
- **Middleware**:
  - `authMiddleWare.authUser`
- **Validation**:
  - `body('name').isString().withMessage('Name is required')`
- **Controller**: `projectController.createProject`

### 2. Get All Projects

- **Method**: GET
- **URL**: `/all`
- **Middleware**:
  - `authMiddleWare.authUser`
- **Controller**: `projectController.getAllProject`

### 3. Add Users to Project

- **Method**: PUT
- **URL**: `/add-user`
- **Middleware**:
  - `authMiddleWare.authUser`
- **Validation**:
  - `body('projectId').isString().withMessage('Project ID is required')`
  - `body('users').isArray({ min: 1 }).withMessage('Users must be an array of strings').bail().custom((users) => users.every(user => typeof user === 'string')).withMessage('Each user must be a string')`
- **Controller**: `projectController.addUserToProject`

### 4. Get Project by ID

- **Method**: GET
- **URL**: `/get-project/:projectId`
- **Middleware**:
  - `authMiddleWare.authUser`
- **Controller**: `projectController.getProjectById`

### 5. Update File Tree

- **Method**: PUT
- **URL**: `/update-file-tree`
- **Middleware**:
  - `authMiddleWare.authUser`
- **Validation**:
  - `body('projectId').isString().withMessage('Project ID is required')`
  - `body('fileTree').isObject().withMessage('File tree is required')`
- **Controller**: `projectController.updateFileTree`

---

## Project Structure

### 1. **Routes**

`project.routes.js`

- Defines the endpoints for managing projects.
- Applies middleware for authentication and validation.

### 2. **Controllers**

`project.controllers.js`

- Handles request processing, response formatting, and error handling.
- Relies on services for business logic.

### 3. **Services**

`project.services.js`

- Contains business logic for project operations such as creating projects, fetching projects, and updating file trees.
- Interacts with the database using Mongoose models.

### 4. **Middleware**

`auth.middleware.js`

- Handles user authentication.

### 5. **Models**

`project.model.js`

- Defines the schema for projects.

---

## Technologies Used

- **Node.js**
- **Express**
- **Mongoose**
- **Express Validator**

---

## Error Handling

- Validation errors are captured using `express-validator`.
- Each service and controller has error handling for invalid input or database errors.

--

# AI Integration API

This repository contains the backend code for an AI integration API built using Node.js, Express, and Google Generative AI. The API allows users to generate AI-driven results based on input prompts.

---

## Features

- **AI Result Generation**: Use the Google Generative AI model to generate responses for user-provided prompts.

---

## Endpoints

### 1. Get AI Result

- **Method**: GET
- **URL**: `/get-result`
- **Controller**: `aiController.getResult`
- **Query Parameters**:
  - `prompt` (string): The input prompt for the AI model.

---

## Project Structure

### 1. **Routes**

`ai.routes.js`

- Defines the endpoint for interacting with the AI service.

### 2. **Controllers**

`ai.controller.js`

- Processes the request and sends a response to the client.
- Calls the AI service to generate results.

### 3. **Services**

`ai.services.js`

- Integrates with the Google Generative AI API to fetch AI-generated results.
- Configures the AI model with custom settings and instructions.

---

## Setup Instructions

1. Clone the repository:
   ```bash
   git clone <repository-url>
   ```
2. Install dependencies:
   ```bash
   npm install
   ```
3. Set up environment variables:
   - `GOOGLE_AI_KEY`: Your Google Generative AI API key.
4. Start the server:
   ```bash
   npm start
   ```

---

## Technologies Used

- **Node.js**
- **Express**
- **Google Generative AI**

---

## Configuration

### Google Generative AI Model Settings

- **Model**: `gemini-1.5-flash`
- **Temperature**: `0.4`
- **System Instruction**:
  - Configured for a 10-year experienced developer with expertise in MERN stack and modular development practices.
  - Generates responses and code adhering to best practices and scalability.

---

## Error Handling

- The controller includes error handling to manage unexpected issues.
- Returns appropriate HTTP status codes and error messages.

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
