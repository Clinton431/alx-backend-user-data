# Basic Authentication

A simple and straightforward implementation of HTTP Basic Authentication for web applications.

## Overview

Basic Authentication is a simple authentication scheme built into the HTTP protocol. The client sends HTTP requests with the Authorization header that contains the word "Basic" followed by a space and a base64-encoded string `username:password`.

## Features

- Simple username/password authentication
- Base64 encoding/decoding
- HTTP header handling
- Configurable user credentials
- Lightweight and minimal dependencies

## Installation

```bash
npm install basic-auth
```

## Quick Start

### Server-side Implementation (Node.js/Express)

```javascript
const express = require('express');
const basicAuth = require('basic-auth');

const app = express();

// Basic auth middleware
const auth = (req, res, next) => {
  const credentials = basicAuth(req);
  
  if (!credentials || !check(credentials.name, credentials.pass)) {
    res.statusCode = 401;
    res.setHeader('WWW-Authenticate', 'Basic realm="Secure Area"');
    res.end('Access denied');
  } else {
    next();
  }
};

// Simple credential check
const check = (name, pass) => {
  return name === 'admin' && pass === 'password123';
};

// Protected route
app.get('/protected', auth, (req, res) => {
  res.send('Welcome to the protected area!');
});

app.listen(3000);
```

### Client-side Implementation

```javascript
// Create base64 encoded credentials
const username = 'admin';
const password = 'password123';
const credentials = btoa(`${username}:${password}`);

// Make authenticated request
fetch('/protected', {
  headers: {
    'Authorization': `Basic ${credentials}`
  }
})
.then(response => response.text())
.then(data => console.log(data));
```

## Configuration

### Environment Variables

Create a `.env` file in your project root:

```env
AUTH_USERNAME=your_username
AUTH_PASSWORD=your_secure_password
AUTH_REALM=Secure Area
```

### Multiple Users

```javascript
const users = {
  'admin': 'admin123',
  'user': 'user456',
  'guest': 'guest789'
};

const authenticate = (name, pass) => {
  return users[name] && users[name] === pass;
};
```

## Security Considerations

⚠️ **Important Security Notes:**

- Basic Authentication transmits credentials in base64 encoding (not encryption)
- Always use HTTPS in production to encrypt the transmission
- Base64 can be easily decoded - never rely on it for security
- Consider using more secure authentication methods for production applications
- Implement rate limiting to prevent brute force attacks
- Use strong, unique passwords

## HTTP Headers

### Request Header
```
Authorization: Basic YWRtaW46cGFzc3dvcmQxMjM=
```

### Response Header (when authentication fails)
```
WWW-Authenticate: Basic realm="Secure Area"
```

## Usage Examples

### Express Middleware

```javascript
const basicAuthMiddleware = (req, res, next) => {
  const authHeader = req.headers.authorization;
  
  if (!authHeader || !authHeader.startsWith('Basic ')) {
    return res.status(401).json({ error: 'Missing or invalid authorization header' });
  }
  
  const credentials = Buffer.from(authHeader.slice(6), 'base64').toString('utf-8');
  const [username, password] = credentials.split(':');
  
  if (isValidCredentials(username, password)) {
    req.user = { username };
    next();
  } else {
    res.status(401).json({ error: 'Invalid credentials' });
  }
};
```

### Browser Integration

```html
<!DOCTYPE html>
<html>
<head>
    <title>Basic Auth Example</title>
</head>
<body>
    <button onclick="makeAuthenticatedRequest()">Access Protected Resource</button>
    
    <script>
        function makeAuthenticatedRequest() {
            const username = prompt('Username:');
            const password = prompt('Password:');
            
            if (username && password) {
                const credentials = btoa(`${username}:${password}`);
                
                fetch('/api/protected', {
                    headers: {
                        'Authorization': `Basic ${credentials}`
                    }
                })
                .then(response => {
                    if (response.ok) {
                        return response.json();
                    }
                    throw new Error('Authentication failed');
                })
                .then(data => console.log(data))
                .catch(error => alert(error.message));
            }
        }
    </script>
</body>
</html>
```

## Testing

### Using curl

```bash
# Test with valid credentials
curl -u admin:password123 http://localhost:3000/protected

# Test with invalid credentials
curl -u admin:wrongpass http://localhost:3000/protected

# Test with base64 encoded credentials
curl -H "Authorization: Basic YWRtaW46cGFzc3dvcmQxMjM=" http://localhost:3000/protected
```

### Using Postman

1. Open Postman
2. Create a new request
3. Go to the "Authorization" tab
4. Select "Basic Auth" from the dropdown
5. Enter your username and password
6. Send the request

## Common Issues

### Issue: 401 Unauthorized
- Check if credentials are correct
- Verify the Authorization header format
- Ensure the server is properly configured

### Issue: CORS Errors
```javascript
app.use((req, res, next) => {
  res.header('Access-Control-Allow-Origin', '*');
  res.header('Access-Control-Allow-Headers', 'Authorization, Content-Type');
  next();
});
```

## Alternatives

For production applications, consider these more secure alternatives:

- **JWT (JSON Web Tokens)**: Stateless and more secure
- **OAuth 2.0**: Industry standard for authorization
- **API Keys**: Simple and effective for API access
- **Session-based authentication**: Server-side session management

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests
5. Submit a pull request

## License

MIT License - see LICENSE file for details

## Support

For issues and questions:
- Create an issue on GitHub
- Check the documentation
- Review existing issues and discussions
