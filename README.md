# Backend Project - YouTube-like API

A comprehensive backend API server built with Node.js and Express that provides user authentication, video management, and subscription features similar to YouTube.

**Author:** Addan Muhammad  
**License:** ISC

---

## Table of Contents

- [Overview](#overview)
- [Tech Stack](#tech-stack)
- [Installation](#installation)
- [Configuration](#configuration)
- [Project Structure](#project-structure)
- [API Endpoints](#api-endpoints)
- [Features](#features)
- [Database Models](#database-models)
- [Authentication](#authentication)
- [File Uploads](#file-uploads)
- [Running the Server](#running-the-server)

---

## Overview

This is a full-featured backend API for a YouTube-like video sharing platform. It handles user management, video uploads, subscriptions, watch history, and much more. The API is built with best practices for error handling, security, and scalability.

---

## Tech Stack

### Core Framework

- **Node.js** - JavaScript runtime
- **Express.js** (^5.2.1) - Web framework

### Database

- **MongoDB** - NoSQL database
- **Mongoose** (^9.2.1) - MongoDB object modeling

### Authentication & Security

- **JWT (jsonwebtoken)** (^9.0.3) - Token-based authentication
- **bcrypt** (^6.0.0) - Password hashing
- **cookie-parser** (^1.4.7) - HTTP cookies middleware

### File & Media Management

- **Cloudinary** (^2.9.0) - Cloud storage for images and videos
- **Multer** (^2.0.2) - File upload middleware

### Utilities

- **CORS** (^2.8.6) - Cross-origin resource sharing
- **dotenv** (^17.3.1) - Environment variable management
- **mongoose-aggregate-paginate-v2** (^1.1.4) - Pagination for aggregation queries

### Development

- **nodemon** (^3.1.11) - Auto-restart server on file changes

---

## Installation

1. **Clone or extract the project:**

   ```bash
   cd Backend\ Project
   ```

2. **Install dependencies:**
   ```bash
   npm install
   ```

---

## Configuration

Create a `.env` file in the root directory with the following environment variables:

```env
# Server Configuration
PORT=8000

# Database Configuration
MONGODB_URI=mongodb://localhost:27017

# JWT Secrets
ACCESS_TOKEN_SECRET=your_access_token_secret_here
REFRESH_TOKEN_SECRET=your_refresh_token_secret_here

# JWT Expiration Times
ACCESS_TOKEN_EXPIRES_IN=7d
REFRESH_TOKEN_EXPIRES_IN=30d

# CORS Configuration
CORS_ORIGIN=http://localhost:3000

# Cloudinary Configuration
CLOUDINARY_NAME=your_cloudinary_name
CLOUDINARY_API_KEY=your_cloudinary_api_key
CLOUDINARY_API_SECRET=your_cloudinary_api_secret
```

---

## Project Structure

```
Backend Project/
├── src/
│   ├── app.js                          # Express app configuration
│   ├── index.js                        # Entry point & server startup
│   ├── constants.js                    # Application constants
│   │
│   ├── controllers/
│   │   └── user.controller.js          # User-related business logic
│   │
│   ├── models/
│   │   ├── user.model.js               # User schema & methods
│   │   ├── video.model.js              # Video schema
│   │   └── subscription.model.js       # Subscription schema
│   │
│   ├── routes/
│   │   └── user.routes.js              # User API endpoints
│   │
│   ├── middlewares/
│   │   ├── auth.middleware.js          # JWT verification
│   │   └── multer.middleware.js        # File upload handling
│   │
│   ├── db/
│   │   └── index.js                    # MongoDB connection
│   │
│   └── utils/
│       ├── asyncHandler.js             # Async error wrapper
│       ├── ApiError.js                 # Custom error class
│       ├── ApiResponse.js              # Standard response format
│       └── cloudinary.js               # Cloudinary upload service
│
├── public/
│   └── temp/                           # Temporary file storage
│
├── package.json                        # Project metadata & dependencies
└── README.md                           # This file
```

---

## API Endpoints

### User Routes (`/api/v1/users`)

#### Public Endpoints

| Method | Endpoint    | Description                                     |
| ------ | ----------- | ----------------------------------------------- |
| POST   | `/register` | Register a new user with avatar and cover image |
| POST   | `/login`    | Authenticate user and get tokens                |

#### Protected Endpoints (Require JWT)

| Method | Endpoint           | Description                              |
| ------ | ------------------ | ---------------------------------------- |
| POST   | `/logout`          | Logout user and invalidate refresh token |
| POST   | `/refresh-token`   | Refresh access token                     |
| POST   | `/change-password` | Change user password                     |
| POST   | `/current-user`    | Get current logged-in user details       |
| PATCH  | `/update-account`  | Update user account details              |
| PATCH  | `/avatar`          | Update user avatar                       |
| PATCH  | `/cover-image`     | Update user cover image                  |
| GET    | `/c/:username`     | Get user channel profile                 |
| GET    | `/history`         | Get user's watch history                 |

---

## Features

### User Management

- ✅ User registration with email and username validation
- ✅ Secure password hashing with bcrypt
- ✅ User authentication with JWT tokens
- ✅ Access and refresh token system
- ✅ User profile with avatar and cover image
- ✅ Password change functionality
- ✅ Account details update

### Video Management

- ✅ Video upload with thumbnail
- ✅ Video metadata (title, description, duration)
- ✅ Published/unpublished status
- ✅ View count tracking
- ✅ Pagination support for video lists

### Social Features

- ✅ User subscriptions (channel subscriptions)
- ✅ Watch history tracking
- ✅ User channel profiles

### Security

- ✅ JWT-based authentication
- ✅ Password hashing with bcrypt
- ✅ CORS enabled for secure cross-origin requests
- ✅ Cookie-based session management
- ✅ Request validation and error handling

### File Management

- ✅ Avatar upload and management
- ✅ Cover image upload
- ✅ Video file storage on Cloudinary
- ✅ Multer middleware for file handling

---

## Database Models

### User Model

Stores user information with the following fields:

```
- username (unique, lowercase)
- email (unique, lowercase)
- fullName
- avatar (Cloudinary URL)
- coverImage (Cloudinary URL)
- password (hashed)
- refreshToken
- watchHistory (array of Video references)
- timestamps (createdAt, updatedAt)
```

### Video Model

Stores video information with the following fields:

```
- videoFile (Cloudinary URL)
- thumbnail (Cloudinary URL)
- title
- description
- duration
- views (default: 0)
- isPublished (default: true)
- owner (User reference)
- timestamps (createdAt, updatedAt)
```

### Subscription Model

Tracks user subscriptions with the following fields:

```
- subscriber (User reference)
- channel (User reference)
- timestamps (createdAt, updatedAt)
```

---

## Authentication

The application uses JWT (JSON Web Tokens) for authentication:

### Token Flow

1. **Registration** → User provides credentials and uploads avatar
2. **Login** → User receives access and refresh tokens
3. **Protected Routes** → Include access token in Authorization header or cookies
4. **Token Refresh** → Use refresh token to get new access token
5. **Logout** → Invalidate refresh token

### Token Structure

- **Access Token**: Short-lived token for API requests (default: 7 days)
- **Refresh Token**: Long-lived token to refresh access token (default: 30 days)

### Usage in Requests

```
Authorization: Bearer <access_token>
```

Or via cookies (automatically handled by cookie-parser)

---

## File Uploads

### Multer Middleware

Handles file uploads with the following features:

- Avatar upload (single file)
- Cover image upload (single file)
- Multiple file fields support

### Cloudinary Integration

All uploaded files are stored on Cloudinary:

- Images (avatars, thumbnails, cover images)
- Video files
- Secure URLs returned for stored files

---

## Running the Server

### Development Mode

Start the server with auto-reload on file changes:

```bash
npm run dev
```

The server will start on the port specified in `.env` (default: 8000)

### Console Output

```
Server is running on port 8000
MongoDB connected !! DB HOST: <your_mongodb_host>
```

---

## Error Handling

The application uses a custom `ApiError` class with:

- HTTP status codes
- Descriptive error messages
- Error stacks for debugging
- Standardized error format

### Standard Success Response

```json
{
  "statusCode": 200,
  "message": "Success message",
  "data": {
    /* response data */
  },
  "success": true
}
```

### Standard Error Response

```json
{
  "statusCode": 400,
  "message": "Error message",
  "data": null,
  "success": false
}
```

---

## Utility Functions

### asyncHandler

Wraps async route handlers to automatically catch errors and pass them to error middleware.

### ApiResponse

Creates standardized API response objects with status code, message, and data.

### ApiError

Custom error class extending JavaScript Error for consistent error handling.

### Cloudinary Utils

Handles file uploads to Cloudinary and returns secure URLs.

---

## Best Practices Implemented

✅ Error handling with try-catch and custom middleware  
✅ Environment variable configuration  
✅ Password hashing and security  
✅ JWT token management  
✅ Request validation  
✅ CORS configuration  
✅ Modular code structure  
✅ Separation of concerns (Controllers, Models, Routes, Middleware, Utils)  
✅ Database connection with proper error handling  
✅ File upload security with Multer  
✅ Cloud storage integration with Cloudinary

---

## Future Enhancements

- [ ] Video comments and likes system
- [ ] Playlist management
- [ ] Video search and filtering
- [ ] User notifications
- [ ] Video recommendations
- [ ] Rate limiting and compression middleware
- [ ] API documentation (Swagger/OpenAPI)
- [ ] Unit and integration tests
- [ ] Email verification
- [ ] Two-factor authentication

---

## Support

For issues or questions, please contact the project author or review the code structure and comments for more details.

---

**Happy coding! 🚀**
