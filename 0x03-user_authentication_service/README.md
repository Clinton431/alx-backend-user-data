# User Authentication Service

A robust, secure, and scalable user authentication service built to handle user registration, login, password management, and session management for modern applications.

## Features

- **User Registration & Login** - Secure account creation and authentication
- **Password Security** - Bcrypt hashing with salt rounds
- **JWT Token Management** - Stateless authentication with refresh tokens
- **Password Reset** - Secure password reset via email verification
- **Email Verification** - Account activation through email confirmation
- **Rate Limiting** - Protection against brute force attacks
- **Multi-Factor Authentication (MFA)** - Optional 2FA support
- **Session Management** - Secure session handling and logout
- **Role-Based Access Control** - User roles and permissions
- **Account Lockout** - Automatic lockout after failed attempts

## Tech Stack

- **Runtime**: Node.js
- **Framework**: Express.js
- **Database**: PostgreSQL / MongoDB
- **Authentication**: JWT (JSON Web Tokens)
- **Password Hashing**: bcrypt
- **Email Service**: SendGrid / Nodemailer
- **Validation**: Joi / express-validator
- **Testing**: Jest
- **Documentation**: Swagger/OpenAPI

## Installation

### Prerequisites

- Node.js (v16 or higher)
- PostgreSQL or MongoDB
- Redis (for session storage)
- SMTP server or email service

### Setup

1. Clone the repository:
```bash
git clone https://github.com/your-org/user-auth-service.git
cd user-auth-service
```

2. Install dependencies:
```bash
npm install
```

3. Create environment configuration:
```bash
cp .env.example .env
```

4. Configure environment variables in `.env`:
```env
# Server Configuration
PORT=3000
NODE_ENV=production

# Database
DATABASE_URL=postgresql://username:password@localhost:5432/auth_db

# JWT Configuration
JWT_SECRET=your-super-secret-jwt-key
JWT_EXPIRES_IN=15m
REFRESH_TOKEN_SECRET=your-refresh-token-secret
REFRESH_TOKEN_EXPIRES_IN=7d

# Email Configuration
EMAIL_SERVICE=sendgrid
EMAIL_API_KEY=your-sendgrid-api-key
FROM_EMAIL=noreply@yourapp.com

# Rate Limiting
RATE_LIMIT_WINDOW_MS=900000
RATE_LIMIT_MAX_REQUESTS=5

# Security
BCRYPT_ROUNDS=12
ACCOUNT_LOCKOUT_TIME=1800000
MAX_LOGIN_ATTEMPTS=5
```

5. Run database migrations:
```bash
npm run migrate
```

6. Start the service:
```bash
# Development
npm run dev

# Production
npm start
```

## API Documentation

### Authentication Endpoints

#### Register User
```http
POST /api/auth/register
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "SecurePassword123!",
  "firstName": "John",
  "lastName": "Doe"
}
```

#### Login
```http
POST /api/auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "SecurePassword123!"
}
```

#### Refresh Token
```http
POST /api/auth/refresh
Content-Type: application/json

{
  "refreshToken": "your-refresh-token"
}
```

#### Logout
```http
POST /api/auth/logout
Authorization: Bearer your-jwt-token
```

#### Password Reset Request
```http
POST /api/auth/forgot-password
Content-Type: application/json

{
  "email": "user@example.com"
}
```

#### Reset Password
```http
POST /api/auth/reset-password
Content-Type: application/json

{
  "token": "reset-token-from-email",
  "newPassword": "NewSecurePassword123!"
}
```

#### Verify Email
```http
GET /api/auth/verify-email?token=verification-token
```

### User Management Endpoints

#### Get User Profile
```http
GET /api/users/profile
Authorization: Bearer your-jwt-token
```

#### Update Profile
```http
PUT /api/users/profile
Authorization: Bearer your-jwt-token
Content-Type: application/json

{
  "firstName": "Jane",
  "lastName": "Smith"
}
```

#### Change Password
```http
PUT /api/users/change-password
Authorization: Bearer your-jwt-token
Content-Type: application/json

{
  "currentPassword": "CurrentPassword123!",
  "newPassword": "NewPassword123!"
}
```

## Response Format

### Success Response
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "uuid",
      "email": "user@example.com",
      "firstName": "John",
      "lastName": "Doe",
      "isVerified": true,
      "role": "user"
    },
    "tokens": {
      "accessToken": "jwt-access-token",
      "refreshToken": "refresh-token"
    }
  },
  "message": "Login successful"
}
```

### Error Response
```json
{
  "success": false,
  "error": {
    "code": "INVALID_CREDENTIALS",
    "message": "Invalid email or password",
    "details": {}
  }
}
```

## Security Features

### Password Requirements
- Minimum 8 characters
- At least one uppercase letter
- At least one lowercase letter
- At least one number
- At least one special character

### Rate Limiting
- Login attempts: 5 per 15 minutes per IP
- Password reset: 3 per hour per email
- Registration: 10 per hour per IP

### Token Security
- JWT access tokens expire in 15 minutes
- Refresh tokens expire in 7 days
- Tokens are signed with strong secrets
- Automatic token rotation on refresh

## Configuration

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `PORT` | Server port | 3000 |
| `DATABASE_URL` | Database connection string | - |
| `JWT_SECRET` | JWT signing secret | - |
| `JWT_EXPIRES_IN` | Access token expiration | 15m |
| `REFRESH_TOKEN_SECRET` | Refresh token secret | - |
| `REFRESH_TOKEN_EXPIRES_IN` | Refresh token expiration | 7d |
| `BCRYPT_ROUNDS` | Password hashing rounds | 12 |
| `EMAIL_SERVICE` | Email service provider | sendgrid |
| `RATE_LIMIT_MAX_REQUESTS` | Max requests per window | 5 |

## Testing

Run the test suite:
```bash
# Unit tests
npm test

# Integration tests
npm run test:integration

# Coverage report
npm run test:coverage
```

## Deployment

### Docker

Build and run with Docker:
```bash
docker build -t auth-service .
docker run -p 3000:3000 --env-file .env auth-service
```

### Docker Compose

```bash
docker-compose up -d
```

### Production Checklist

- [ ] Set strong JWT secrets
- [ ] Configure HTTPS/TLS
- [ ] Set up database backups
- [ ] Configure monitoring and logging
- [ ] Set up rate limiting
- [ ] Configure CORS properly
- [ ] Set secure HTTP headers
- [ ] Enable database connection pooling

## Monitoring

The service includes built-in health checks and metrics:

- Health check: `GET /health`
- Metrics: `GET /metrics` (Prometheus format)
- Logs: Structured JSON logging with Winston

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request
