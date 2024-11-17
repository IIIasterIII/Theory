## JWT Authentication Example 🚀🔒
This repository demonstrates the implementation of JWT (JSON Web Token) authentication in a simple application. The app features user registration, login, and a protected route that can only be accessed by authenticated users.

Features ✨
User Registration 📝
User Login 🔑
Protected Route (only accessible with valid JWT) 🔐
Tech Stack 🛠️
Backend: Node.js, Express, JWT
Frontend: React, Axios
Database: In-memory simulation (could be replaced with a real database)
Overview 📖
JWT (JSON Web Token)
JWT is used for securely transmitting information between parties. It consists of three parts: Header, Payload, and Signature.

```css
Header.Payload.Signature
```
Structure of JWT
 1. Header: Describes the token type (JWT) and the algorithm used for signing (e.g., HMAC SHA256).
 2. Payload: Contains the claims (user data).
 3. Signature: Ensures that the token has not been altered and can be verified using a secret key.
Example JWT:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyaWQiOiIxMjM0IiwidXNlcm5hbWUiOiJqb2huZG9lIiwiaWF0IjoxNjA5NjU5OTk5fQ.7fLiJBM9YJ9x8NqZDz0H1FZ7YViTJlZJfwHfgz9-jfE
```

## Backend Setup 👨‍💻

```js
const express = require('express');
const jwt = require('jsonwebtoken');
const cors = require('cors');

const app = express();
app.use(cors());
app.use(express.json());

const tokenKey = "1a2b-3c4d-5e6f-7g8h"; // Secret key for JWT
const PORT = 3000; // Server port

// Simulating a database (this can be replaced with a real database)
const users = [];

// Function to generate a JWT token
const generateToken = (user) => {
  return jwt.sign({ username: user.username }, tokenKey, { expiresIn: '1h' });
};

// Registration route
app.post('/register', (req, res) => {
  const { username, password } = req.body;
  if (!username || !password) {
    return res.status(400).json({ message: 'Username and password are required' });
  }

  // Check if the user already exists
  const existingUser = users.find((user) => user.username === username);
  if (existingUser) {
    return res.status(409).json({ message: 'User already exists' });
  }

  // Create a new user
  const newUser = { username, password };
  users.push(newUser);

  // Generate JWT token
  const token = generateToken(newUser);
  res.status(201).json({ token });
});

// Login route
app.post('/login', (req, res) => {
  const { username, password } = req.body;
  if (!username || !password) {
    return res.status(400).json({ message: 'Username and password are required' });
  }

  // Check user credentials
  const user = users.find(
    (user) => user.username === username && user.password === password
  );
  if (!user) {
    return res.status(401).json({ message: 'Invalid credentials' });
  }

  // Generate JWT token
  const token = generateToken(user);
  res.json({ token });
});

// Middleware to authenticate JWT
const authenticateToken = (req, res, next) => {
  const authHeader = req.headers['authorization'];
  const token = authHeader && authHeader.split(' ')[1];
  if (!token) {
    return res.status(403).json({ message: 'Token is required' });
  }

  // Verify JWT token
  jwt.verify(token, tokenKey, (err, user) => {
    if (err) {
      return res.status(401).json({ message: 'Invalid token' });
    }
    req.user = user;
    next();
  });
};

// Protected route (only accessible by authenticated users)
app.get('/protected', authenticateToken, (req, res) => {
  res.json({ message: `Hello, ${req.user.username}! This is a protected route.` });
});

// Start the server
app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});

```

## Frontend Setup 🎨
This is the React frontend for the JWT authentication app. It allows users to register, log in, and access a protected route after authentication.

```js
import React, { useState } from 'react';
import axios from 'axios';

const API_URL = 'http://localhost:3000';

function App() {
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');
  const [message, setMessage] = useState('');
  const [token, setToken] = useState(null);
  const [protectedMessage, setProtectedMessage] = useState('');

  // Handle user registration
  const handleRegister = async (e) => {
    e.preventDefault();
    try {
      const response = await axios.post(`${API_URL}/register`, { username, password });
      setMessage('Registration successful!');
      setToken(response.data.token);
    } catch (error) {
      setMessage(error.response?.data?.message || 'Registration failed');
    }
  };

  // Handle user login
  const handleLogin = async (e) => {
    e.preventDefault();
    try {
      const response = await axios.post(`${API_URL}/login`, { username, password });
      setMessage('Login successful!');
      setToken(response.data.token);
    } catch (error) {
      setMessage(error.response?.data?.message || 'Login failed');
    }
  };

  // Fetch protected data
  const handleFetchProtectedData = async () => {
    try {
      const response = await axios.get(`${API_URL}/protected`, {
        headers: { Authorization: `Bearer ${token}` },
      });
      setProtectedMessage(response.data.message);
    } catch (error) {
      setProtectedMessage(error.response?.data?.message || 'Access denied');
    }
  };

  return (
    <div style={{ maxWidth: '500px', margin: '0 auto', padding: '20px' }}>
      <h1>JWT Authentication</h1>
      
      {!token && (
        <>
          <h2>Register</h2>
          <form onSubmit={handleRegister}>
            <input
              type="text"
              placeholder="Username"
              value={username}
              onChange={(e) => setUsername(e.target.value)}
              style={{ width: '100%', marginBottom: '10px' }}
            />
            <input
              type="password"
              placeholder="Password"
              value={password}
              onChange={(e) => setPassword(e.target.value)}
              style={{ width: '100%', marginBottom: '10px' }}
            />
            <button type="submit" style={{ width: '100%' }}>Register</button>
          </form>

          <h2>Login</h2>
          <form onSubmit={handleLogin}>
            <input
              type="text"
              placeholder="Username"
              value={username}
              onChange={(e) => setUsername(e.target.value)}
              style={{ width: '100%', marginBottom: '10px' }}
            />
            <input
              type="password"
              placeholder="Password"
              value={password}
              onChange={(e) => setPassword(e.target.value)}
              style={{ width: '100%', marginBottom: '10px' }}
            />
            <button type="submit" style={{ width: '100%' }}>Login</button>
          </form>
        </>
      )}

      {message && <p>{message}</p>}

      {token && (
        <>
          <h2>Protected Route</h2>
          <button onClick={handleFetchProtectedData} style={{ width: '100%', marginBottom: '10px' }}>
            Get Protected Data
          </button>
          {protectedMessage && <p>{protectedMessage}</p>}
        </>
      )}
    </div>
  );
}

export default App;
```
