# 🚗 Garage Application

A full-stack **car service booking and management platform** that I built from the ground up using **Next.js, TypeScript, Node.js, Express, and MongoDB**.

The application allows customers to create accounts, register and manage their vehicles, schedule service bookings, and track their booking history. Administrators can manage bookings, update service statuses, and access operational statistics through a dedicated dashboard.

![Next.js](https://img.shields.io/badge/Next.js-14-black?logo=next.js)
![React](https://img.shields.io/badge/React-18-blue?logo=react)
![TypeScript](https://img.shields.io/badge/TypeScript-5-blue?logo=typescript)
![Node.js](https://img.shields.io/badge/Node.js-Express-green?logo=node.js)
![MongoDB](https://img.shields.io/badge/MongoDB-Mongoose-green?logo=mongodb)
![JWT](https://img.shields.io/badge/Authentication-JWT-orange?logo=jsonwebtokens)
![Tailwind CSS](https://img.shields.io/badge/Styling-Tailwind%20CSS-06B6D4?logo=tailwindcss)

---

## 📋 Table of Contents

* [Overview](#-overview)
* [Features](#-features)
* [Tech Stack](#-tech-stack)
* [Architecture](#-architecture)
* [Prerequisites](#-prerequisites)
* [Installation](#-installation)
* [Project Structure](#-project-structure)
* [Configuration](#-configuration)
* [Authentication](#-authentication)
* [Car Management](#-car-management)
* [Booking System](#-booking-system)
* [Admin Dashboard](#-admin-dashboard)
* [API Documentation](#-api-documentation)
* [Advanced Features](#-advanced-features)
* [Deployment](#-deployment)
* [Troubleshooting](#-troubleshooting)
* [Security](#-security)
* [Contributing](#-contributing)
* [Future Improvements](#-future-improvements)
* [License](#-license)
* [Author](#-author)

---

## 📱 Overview

**Garage Application** is a full-stack vehicle service booking system designed to simplify the process of managing cars and scheduling garage services.

I designed the application around two primary user roles:

### 👤 Customer

Customers can:

* Create an account
* Login securely
* Register their vehicles
* View their registered vehicles
* Update vehicle information
* Remove vehicles
* Book garage services
* View their booking history

### 👨‍💼 Administrator

Administrators can:

* Access protected administrative functionality
* View all customer bookings
* Update booking statuses
* Monitor service activity
* View aggregated operational statistics
* Analyze booking and service-type data
* View recent bookings and estimated revenue

The system uses a **Next.js frontend** communicating with a **RESTful Express API**, with MongoDB used for persistence.

---

## ✨ Features

### 🔐 Authentication & Authorization

* ✅ User registration
* ✅ User login
* ✅ Password hashing using bcryptjs
* ✅ JWT authentication
* ✅ Protected routes
* ✅ Authenticated user profile endpoint
* ✅ Role-based authorization
* ✅ Separate customer and administrator permissions

---

### 🚗 Vehicle Management

Customers can manage their registered vehicles through a complete CRUD workflow.

* ➕ Add a vehicle
* 📋 View all registered vehicles
* 🔍 View an individual vehicle
* ✏️ Update vehicle information
* 🗑️ Delete a vehicle
* 🔎 Search vehicles
* ⏱️ Debounced search input

The frontend includes a reusable `CarCard` component for presenting vehicle information.

---

### 📅 Service Booking

The application provides a complete garage service booking workflow.

* 📅 Create service bookings
* 🚗 Associate bookings with registered vehicles
* 🛠️ Select service types
* 📋 View personal booking history
* 👨‍💼 Admin access to all bookings
* 🔄 Update booking status
* 📊 Track booking statistics

---

### 👨‍💼 Admin Dashboard

The administrator dashboard provides an overview of garage operations.

The application supports:

* Booking status statistics
* Service-type statistics
* Recent bookings
* Customer information
* Vehicle information
* Estimated revenue

The statistics endpoint uses a MongoDB aggregation pipeline to combine multiple datasets into a single API response.

---

### 🔎 Debounced Vehicle Search

I implemented a reusable generic React hook:

```text
frontend/src/hooks/useDebounce.ts
```

The hook uses:

```text
setTimeout()
clearTimeout()
```

to delay API-triggering search operations.

This prevents unnecessary API requests while the user is typing.

```text
User types
    ↓
Debounce
    ↓
Wait for typing to stop
    ↓
API request
    ↓
Filtered vehicles
```

---

### ⚡ Optimistic UI

Booking status changes use an optimistic UI approach.

Instead of waiting for the server response before updating the interface:

```text
Admin changes booking status
          ↓
UI updates immediately
          ↓
API request
          ↓
      Success?
       /    \
     Yes     No
      ↓       ↓
  Keep UI   Restore
           previous state
```

This makes booking-status management feel faster and more responsive.

---

### 🚦 Custom Sliding-Window Rate Limiter

I implemented a custom rate-limiting middleware without relying on an external rate-limiting package.

The middleware stores request timestamps by IP address and removes expired timestamps during each request.

Current limits:

| Route          |                     Limit |
| -------------- | ------------------------: |
| Authentication |  10 requests / 60 seconds |
| General API    | 100 requests / 60 seconds |

When a client exceeds the configured limit, the API responds with:

```text
HTTP 429 Too Many Requests
```

---

## 🛠️ Tech Stack

### Frontend

| Technology         | Purpose                        |
| ------------------ | ------------------------------ |
| Next.js 14         | Frontend framework             |
| React 18           | UI library                     |
| TypeScript         | Type safety                    |
| Tailwind CSS       | Styling                        |
| Next.js App Router | Application routing            |
| React Hooks        | State and lifecycle management |

### Backend

| Technology | Purpose            |
| ---------- | ------------------ |
| Node.js    | Runtime            |
| Express.js | REST API framework |
| JavaScript | Backend language   |
| JWT        | Authentication     |
| bcryptjs   | Password hashing   |
| Mongoose   | MongoDB ODM        |
| MongoDB    | Database           |
| Nodemon    | Development server |

### Development Practices

* REST API architecture
* MVC-style backend organization
* Middleware-based authentication
* Centralized error handling
* Request logging
* Rate limiting
* Input validation
* Database aggregation
* Optimistic UI
* Debounced API requests

---

## 🏗️ Architecture

The application follows a client-server architecture with a clear separation between frontend and backend responsibilities.

```text
┌────────────────────────────────────────────┐
│              Next.js Frontend              │
│                                            │
│   Pages → Components → Hooks → API Client  │
└──────────────────────┬─────────────────────┘
                       │
                       │ HTTP / REST
                       ▼
┌────────────────────────────────────────────┐
│             Express Backend                │
│                                            │
│ Middleware → Routes → Controllers          │
│                         ↓                  │
│                       Models               │
└──────────────────────┬─────────────────────┘
                       │
                       │ Mongoose
                       ▼
┌────────────────────────────────────────────┐
│                  MongoDB                   │
│                                            │
│       Users / Cars / Bookings              │
└────────────────────────────────────────────┘
```

### Request Flow

```text
Client
  ↓
Next.js UI
  ↓
API Client
  ↓
Express Route
  ↓
Middleware
  ↓
Controller
  ↓
Mongoose Model
  ↓
MongoDB
  ↓
Response
  ↓
Next.js UI
```

---

## 📦 Prerequisites

Before running the project, make sure you have:

* Node.js 18+
* npm
* MongoDB Atlas account or local MongoDB
* Git

Check your Node.js installation:

```bash
node --version
npm --version
```

---

## 🚀 Installation

### 1. Clone the Repository

```bash
git clone https://github.com/MahapatraA/Garage_Application.git
cd Garage_Application
```

---

### 2. Backend Setup

Navigate to the backend:

```bash
cd backend
```

Install dependencies:

```bash
npm install
```

Create the environment file:

```bash
cp .env.example .env
```

Configure the variables as described below.

Start the development server:

```bash
npm run dev
```

The backend runs on:

```text
http://localhost:5000
```

---

### 3. Frontend Setup

Open a second terminal:

```bash
cd frontend
```

Install dependencies:

```bash
npm install
```

Create the local environment file:

```bash
cp .env.example .env.local
```

Start the Next.js development server:

```bash
npm run dev
```

The frontend runs on:

```text
http://localhost:3000
```

---

## ⚙️ Configuration

### Backend `.env`

Create:

```text
backend/.env
```

Example:

```env
PORT=5000

FRONTEND_URL=http://localhost:3000

MONGO_URI=mongodb+srv://<username>:<password>@cluster.mongodb.net/garage-application

WT_SECRET=replace_with_a_long_random_secret

NODE_ENV=development
```

The repository already provides these variables through `backend/.env.example`.

---

### Frontend `.env.local`

Create:

```text
frontend/.env.local
```

Example:

```env
NEXT_PUBLIC_API_URL=http://localhost:5000/api
```

The repository already provides this configuration through `frontend/.env.example`.

---

## 🔑 Authentication

Authentication is implemented using **JWT** and **bcryptjs**.

### Registration

```text
User
 ↓
Registration Form
 ↓
POST /api/auth/register
 ↓
Validate Input
 ↓
Hash Password
 ↓
Create MongoDB User
 ↓
Return Authentication Data
```

### Login

```text
User
 ↓
Login Form
 ↓
POST /api/auth/login
 ↓
Find User
 ↓
Compare Password
 ↓
Generate JWT
 ↓
Authenticated Session
```

### Protected Requests

```text
Frontend
   ↓
Authorization Header
   ↓
JWT Middleware
   ↓
Verify Token
   ↓
Attach User
   ↓
Controller
```

---

## 🚗 Car Management

The vehicle module provides complete CRUD functionality.

### Create

```http
POST /api/cars
```

Creates a vehicle for the authenticated user.

### List

```http
GET /api/cars
```

Returns the user's registered vehicles.

### Get Single Vehicle

```http
GET /api/cars/:id
```

Retrieves a specific vehicle.

### Update

```http
PUT /api/cars/:id
```

Updates vehicle information.

### Delete

```http
DELETE /api/cars/:id
```

Removes a registered vehicle.

---

## 📅 Booking System

Bookings connect customers, vehicles and garage services.

### Booking Flow

```text
Customer
   ↓
Select Vehicle
   ↓
Select Service
   ↓
Provide Booking Details
   ↓
POST /api/bookings
   ↓
Booking Created
   ↓
Booking History
```

Customers can access:

```http
GET /api/bookings/my
```

Administrators can access:

```http
GET /api/bookings
```

Administrators can update booking status using:

```http
PUT /api/bookings/:id/status
```

---

## 👨‍💼 Admin Dashboard

The admin dashboard is designed to provide a quick operational overview.

### Admin Statistics

```http
GET /api/admin/stats
```

The endpoint aggregates:

* Booking counts by status
* Booking counts by service type
* Latest five bookings
* Related user information
* Related vehicle information
* Total estimated revenue

### Aggregation Architecture

```text
                 MongoDB
                    │
                    ▼
             $facet Pipeline
                    │
       ┌────────────┼────────────┐
       ▼            ▼            ▼
   Status       Services      Recent
   Counts        Counts       Bookings
       │            │            │
       └────────────┼────────────┘
                    ▼
              Revenue Data
                    │
                    ▼
              API Response
```

Using a `$facet` pipeline allows multiple aggregation results to be returned through a single database operation.

---

## 🔌 API Documentation

Base URL:

```text
http://localhost:5000/api
```

### 🔐 Authentication

| Method | Endpoint         | Access    |
| ------ | ---------------- | --------- |
| POST   | `/auth/register` | Public    |
| POST   | `/auth/login`    | Public    |
| GET    | `/auth/me`       | Protected |

### 🚗 Cars

| Method | Endpoint    | Access    |
| ------ | ----------- | --------- |
| POST   | `/cars`     | Protected |
| GET    | `/cars`     | Protected |
| GET    | `/cars/:id` | Protected |
| PUT    | `/cars/:id` | Protected |
| DELETE | `/cars/:id` | Protected |

### 📅 Bookings

| Method | Endpoint               | Access    |
| ------ | ---------------------- | --------- |
| POST   | `/bookings`            | Protected |
| GET    | `/bookings/my`         | Protected |
| PUT    | `/bookings/:id/status` | Admin     |
| GET    | `/bookings`            | Admin     |

### 👨‍💼 Admin

| Method | Endpoint       | Access |
| ------ | -------------- | ------ |
| GET    | `/admin/stats` | Admin  |

### ❤️ Health Check

```http
GET /api/health
```

Example response:

```json
{
  "success": true,
  "data": {
    "status": "ok",
    "timestamp": "..."
  }
}
```

---

## 🧠 Advanced Features

### 1. MongoDB Aggregation

The admin statistics feature uses a `$facet` aggregation pipeline to calculate multiple metrics in one database operation.

### 2. Custom Rate Limiter

A sliding-window rate limiter was implemented using an in-memory:

```text
Map<IP, Timestamp[]>
```

Expired timestamps are removed on every request.

### 3. Generic Debounce Hook

The frontend contains:

```text
useDebounce<T>()
```

which can be reused for different value types rather than being tied specifically to vehicle searching.

### 4. Optimistic Updates

Booking-status updates modify the UI immediately and retain a snapshot of the previous state so that failed API operations can be rolled back.

---

## 📁 Project Structure

```text
Garage-Application/
│
├── backend/
│   ├── src/
│   │   ├── config/
│   │   │   └── db.js
│   │   │
│   │   ├── controllers/
│   │   │   ├── adminController.js
│   │   │   ├── authController.js
│   │   │   ├── bookingController.js
│   │   │   └── carController.js
│   │   │
│   │   ├── middleware/
│   │   │   ├── auth.js
│   │   │   ├── errorHandler.js
│   │   │   ├── logger.js
│   │   │   └── rateLimiter.js
│   │   │
│   │   ├── models/
│   │   │   ├── Booking.js
│   │   │   ├── Car.js
│   │   │   └── User.js
│   │   │
│   │   ├── routes/
│   │   │   ├── admin.js
│   │   │   ├── auth.js
│   │   │   ├── bookings.js
│   │   │   └── cars.js
│   │   │
│   │   └── index.js
│   │
│   ├── .env.example
│   ├── .gitignore
│   ├── package.json
│   └── package-lock.json
│
├── frontend/
│   ├── src/
│   │   ├── app/
│   │   │   ├── bookings/
│   │   │   ├── cars/
│   │   │   ├── dashboard/
│   │   │   ├── login/
│   │   │   ├── register/
│   │   │   ├── layout.tsx
│   │   │   └── page.tsx
│   │   │
│   │   ├── components/
│   │   │   ├── BookingCard.tsx
│   │   │   ├── CarCard.tsx
│   │   │   ├── Modal.tsx
│   │   │   └── Navbar.tsx
│   │   │
│   │   ├── hooks/
│   │   │   ├── useAuth.ts
│   │   │   └── useDebounce.ts
│   │   │
│   │   ├── lib/
│   │   │   └── api.ts
│   │   │
│   │   └── types/
│   │       └── index.ts
│   │
│   ├── .env.example
│   ├── .gitignore
│   ├── next.config.mjs
│   ├── package.json
│   ├── postcss.config.mjs
│   └── tsconfig.json
│
└── README.md
```

---

## 🚀 Deployment

The application is designed to deploy the frontend and backend separately.

### Backend

The Express backend can be deployed to platforms such as Railway.

General deployment flow:

```text
GitHub
  ↓
Railway
  ↓
Install dependencies
  ↓
Configure environment variables
  ↓
npm start
```

Required production variables include:

```env
PORT=5000
FRONTEND_URL=<frontend-url>
MONGO_URI=<mongodb-uri>
JWT_SECRET=<secure-secret>
NODE_ENV=production
```

### Frontend

The Next.js frontend can be deployed to Vercel.

Configure:

```env
NEXT_PUBLIC_API_URL=https://<backend-url>/api
```

Then build and deploy the application.

---

## 🐛 Troubleshooting

### MongoDB Connection Error

Check:

* MongoDB URI
* Atlas network access
* Database credentials
* MongoDB cluster availability
* Environment variables

---

### Backend Not Starting

Run:

```bash
cd backend
npm install
npm run dev
```

Check that port `5000` is available.

---

### Frontend Cannot Connect to API

Verify:

```env
NEXT_PUBLIC_API_URL=http://localhost:5000/api
```

and confirm that the backend is running.

---

### Authentication Fails

Check:

* JWT secret
* Login credentials
* Token being sent by the frontend
* Authorization header
* Token expiration

---

### Admin Dashboard Is Inaccessible

Verify the user's MongoDB role:

```text
admin
```

The statistics and administrative booking endpoints require admin authorization.

---

## 🔐 Security

The application includes several security measures:

* 🔒 bcryptjs password hashing
* 🔑 JWT authentication
* 👮 Role-based authorization
* 🚦 Custom sliding-window rate limiting
* ✅ Input validation
* 🌐 CORS configuration
* 🛡️ Centralized error handling
* 🔐 Environment-based secret management

### Rate Limiting

Authentication endpoints are restricted more aggressively than general API endpoints to reduce brute-force attempts.

```text
Auth:
10 requests / 60 seconds

General API:
100 requests / 60 seconds
```

### Environment Variables

Sensitive information must never be committed.

Do not commit:

```text
.env
.env.local
Database credentials
JWT secrets
Production credentials
```

---

## 🧪 Development

### Backend

```bash
cd backend

npm install

npm run dev
```

Production-style start:

```bash
npm start
```

### Frontend

```bash
cd frontend

npm install

npm run dev
```

Production build:

```bash
npm run build
```

Start production build:

```bash
npm start
```

---

## 🤝 Contributing

Contributions and improvements are welcome.

### Create a Branch

```bash
git checkout -b feature/your-feature
```

### Make Changes

Follow the existing architecture and naming conventions.

### Test Locally

Backend:

```bash
cd backend
npm run dev
```

Frontend:

```bash
cd frontend
npm run dev
```

### Commit

```bash
git add .
git commit -m "feat: add your feature"
```

### Push

```bash
git push origin feature/your-feature
```

Then open a Pull Request.

---

## 🚧 Future Improvements

Potential improvements for future versions include:

* 📍 Garage location management
* 📆 Service-slot availability management
* 🔔 Email/SMS notifications
* 📱 Mobile application
* 💳 Online payment support
* ⭐ Customer reviews and ratings
* 📊 Advanced analytics dashboard
* 🔎 Advanced search and filtering
* 📷 Vehicle/service image uploads
* 🧾 Invoice generation
* 📧 Booking confirmation emails
* 🔄 Refresh-token authentication
* 🧪 Automated unit and integration tests
* 🐳 Docker containerization
* ⚙️ CI/CD pipeline
* 📚 Swagger/OpenAPI documentation
* 📈 Application monitoring

---

## 📊 Project Summary

| Category            | Technology                         |
| ------------------- | ---------------------------------- |
| Frontend            | Next.js 14                         |
| UI                  | React 18                           |
| Language            | TypeScript                         |
| Styling             | Tailwind CSS                       |
| Backend             | Node.js + Express                  |
| Database            | MongoDB                            |
| ODM                 | Mongoose                           |
| Authentication      | JWT                                |
| Password Security   | bcryptjs                           |
| Search Optimization | Custom debounce hook               |
| API Protection      | Custom sliding-window rate limiter |
| UI Optimization     | Optimistic updates                 |
| Deployment          | Vercel + Railway                   |

---

## 🎓 What I Built

This project demonstrates my ability to build a complete full-stack application across both frontend and backend layers.

### Frontend

* Designed the application pages
* Built reusable React components
* Implemented TypeScript types
* Created authentication flows
* Integrated REST APIs
* Implemented debounced search
* Implemented optimistic UI updates
* Built customer and admin interfaces

### Backend

* Designed REST API routes
* Implemented authentication
* Implemented JWT authorization
* Designed MongoDB schemas
* Built CRUD operations
* Implemented booking business logic
* Built admin statistics
* Implemented MongoDB aggregation
* Created custom rate limiting
* Added request logging
* Added centralized error handling

### Database

Designed relationships between:

```text
User
 │
 ├── Cars
 │
 └── Bookings
        │
        └── Car
```

This provides a structured data model for connecting customers, vehicles and service bookings.

---

## 📄 License

This project is maintained as a personal/portfolio application.

If you plan to distribute it as open-source software, add an appropriate `LICENSE` file to the repository.

---

## 👨‍💻 Author

**Anish Mahapatra**

GitHub:

https://github.com/MahapatraA

---

## 📝 Project Status

**Version:** 1.0.0

**Status:** 🚧 Development / Portfolio Project

---

**Built from the ground up with ❤️ using Next.js, TypeScript, Node.js, Express and MongoDB.**

**Garage Application 🚗🔧**
