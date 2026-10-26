# JWT-Based User Authentication System
> **College Mini Project Demonstration Guide**  
> Built with: **HTML5, CSS3, JavaScript, Node.js, Express.js, MongoDB, JWT (JSON Web Tokens), and bcrypt**

---

## 📌 Project Overview

The **JWT-Based User Authentication System** is a full-stack web application demonstrating secure, modern user authentication and authorization practices. 

Instead of traditional stateful server sessions, this application implements stateless **JSON Web Tokens (JWT)** delivered via secure **HttpOnly cookies**. User passwords are never saved in plain text; they are salted and hashed using **bcrypt** before persistence in **MongoDB**.

---

## 🚀 Quick Start Guide (How to Run the Project)

### Prerequisites
- **Node.js** (v16 or higher) installed on your system.
- *(Optional)* **MongoDB** installed locally (or a free **MongoDB Atlas** connection string).  
  *Note:* The system includes an automatic fallback mode, so if MongoDB is not running yet, you can still test registration, login, protected dashboard, and logout immediately!

### Step 1: Open VS Code Terminal
Open your project root folder (`MWP MINI PRO`) in VS Code and open the terminal (`Ctrl + ~` or `Terminal -> New Terminal`).

### Step 2: Install Backend Dependencies (Already completed)
If running on a new computer:
```bash
cd backend
npm install
cd ..
```

### Step 3: Configure Environment Variables
Inside the `backend/` directory, there is a `.env` file pre-configured for you:
```env
PORT=5000
NODE_ENV=development
MONGODB_URI=mongodb://127.0.0.1:27017/jwt_auth_db
JWT_SECRET=super_secret_jwt_key_mwp_mini_project_2026_secure_key_12345
JWT_EXPIRES_IN=1d
COOKIE_EXPIRES_IN_DAYS=1
```
*(If you want to use MongoDB Atlas Cloud, simply replace `MONGODB_URI` with your Atlas connection string!)*

### Step 4: Start the Server
From the root folder in your terminal, run:
```bash
npm start
```
*Or, from inside the `backend` folder:*
```bash
node server.js
```

### Step 5: Open in Your Web Browser
Open your browser and visit:
👉 **[http://localhost:5000](http://localhost:5000)**

---

## 🧭 Application Pages & URLs

| Page | URL | Description |
| :--- | :--- | :--- |
| **Landing Page** | `http://localhost:5000` | Project overview, architecture flowchart, and technology stack |
| **User Registration** | `http://localhost:5000/register` | Create account with name, email, password strength meter, confirm password |
| **User Login** | `http://localhost:5000/login` | Secure sign in with email and password |
| **Protected Dashboard** | `http://localhost:5000/dashboard` | Shows authenticated user name, email, user ID, JWT payload inspection |
| **Health Check API** | `http://localhost:5000/api/health` | Diagnostic endpoint checking server & DB status |

---

## 📂 Project Structure

```text
MWP MINI PRO/
│
├── package.json                   # Root package script (runs backend)
├── README.md                      # College project documentation & viva guide
│
├── backend/                       # Backend Application
│   ├── .env                       # Secret keys, port, MongoDB connection URI
│   ├── .env.example               # Template environment file
│   ├── package.json               # Backend npm dependencies & scripts
│   ├── server.js                  # Main Express app, CORS, static serving & routing
│   │
│   ├── config/
│   │   └── db.js                  # MongoDB connection logic using Mongoose
│   │
│   ├── controllers/
│   │   └── authController.js      # Register, login, logout, profile logic
│   │
│   ├── middleware/
│   │   └── authMiddleware.js      # Verifies JWT from HttpOnly cookie
│   │
│   ├── models/
│   │   └── User.js                # Mongoose schema, bcrypt pre-save hashing & methods
│   │
│   └── routes/
│       └── authRoutes.js          # /api/auth API endpoints
│
└── frontend/                      # Client-Side Application
    ├── index.html                 # Modern Landing page
    ├── register.html              # Registration form with password strength meter
    ├── login.html                 # Login form with show/hide password
    ├── dashboard.html             # Protected dashboard (redirects guests)
    │
    ├── css/
    │   └── style.css              # Custom blue, white, and navy responsive styling
    │
    └── js/
        ├── auth.js                # Client validation, register/login API calls, alerts
        └── dashboard.js           # Protected profile fetch, session checks, logout
```

---

## 🔑 Key Features Implemented

1. **User Registration**:
   - Accepts Full Name, Email, Password, and Confirm Password.
   - Client-side and server-side validation against empty fields, invalid email regex, and password mismatches.
   - Real-time password strength meter indicator.
2. **Password Encryption with bcrypt**:
   - Pre-save hook automatically salts and hashes passwords using 10 salt rounds before database storage.
   - Plain text passwords are never stored or logged anywhere.
3. **Secure JWT Token Generation & Verification**:
   - Signs tokens with an encrypted secret key in `.env`.
   - Embeds user ID with an expiration period (1 day).
4. **HttpOnly Cookie Storage (Protection against XSS)**:
   - Tokens are delivered via `res.cookie('token', token, { httpOnly: true, ... })`.
   - Client-side malicious scripts (`document.cookie`) cannot access or steal the token.
5. **Protected Dashboard**:
   - Access is restricted by `authMiddleware.js`.
   - If an unauthenticated user attempts to visit the dashboard, they are automatically redirected to the login page with an alert.
   - Displays user's full name, email, member since date, and security parameters.
6. **Logout Functionality**:
   - Calling `/api/auth/logout` clears the HttpOnly cookie by setting its expiration to the past.
   - User is redirected back to the login screen.
7. **Responsive Modern UI**:
   - Polished aesthetic using blue (`#2563eb`), white, and dark navy (`#0f172a`).
   - Clean alert banners, password toggle visibility (👁), and responsive layouts.

---

## 🎓 College Viva & Mini-Project Demonstration Notes

### 1. What is a JWT (JSON Web Token)?
> **Answer:** A JWT is an open standard (RFC 7519) that defines a compact and self-contained way for securely transmitting information between parties as a JSON object. It consists of three parts separated by dots:
> - **Header:** Algorithm and token type (`{"alg": "HS256", "typ": "JWT"}`).
> - **Payload:** Claims containing user data (`{"id": "...", "iat": ..., "exp": ...}`).
> - **Signature:** Cryptographic hash generated using the secret key (`HMACSHA256(...)`).

### 2. Why use bcrypt for password hashing?
> **Answer:** bcrypt is a slow, computationally intensive hashing function designed specifically for passwords. It uses an adaptive salt (10 rounds in our project) which makes it resistant to:
> - **Rainbow Table attacks** (precomputed hash lookup tables).
> - **Brute-force attacks** (hardware cannot quickly guess millions of attempts per second).

### 3. Why store the JWT in an HttpOnly cookie instead of `localStorage`?
> **Answer:** Storing tokens in `localStorage` makes them accessible to JavaScript, leaving them vulnerable to **Cross-Site Scripting (XSS)** attacks. If an attacker injects a malicious script, they can read `localStorage.getItem('token')`.  
> An **HttpOnly** cookie is strictly handled by the browser engine and **cannot be read by JavaScript**, protecting the user's session from theft.

### 4. How does the Protected Route Middleware work?
> **Answer:** When a user requests `/dashboard` or `/api/auth/me`, the `authMiddleware.js` function intercepts the request:
> 1. Extracts the cookie named `token`.
> 2. Verifies the cryptographic signature using `jwt.verify(token, JWT_SECRET)`.
> 3. If invalid or expired, returns HTTP 401 and the frontend redirects to `/login`.
> 4. If valid, fetches the user details from the database and passes execution to `next()`.

---

## 🛠 Testing Endpoints with Postman / cURL

### 1. Register User:
```http
POST /api/auth/register
Content-Type: application/json

{
  "name": "Jane Doe",
  "email": "jane@example.com",
  "password": "Password123!",
  "confirmPassword": "Password123!"
}
```

### 2. Login User:
```http
POST /api/auth/login
Content-Type: application/json

{
  "email": "jane@example.com",
  "password": "Password123!"
}
```

### 3. Get Authenticated User Profile (Protected):
```http
GET /api/auth/me
```

### 4. Logout User:
```http
POST /api/auth/logout
```
