
# RailYatra — Train Ticket Booking App

A full-stack **train ticket booking application** built with **Spring Boot and React**. RailYatra allows users to register, search trains, check real-time seat availability and fares, book tickets for multiple passengers, generate PNR numbers, and manage their bookings.

---

## Features

* User registration and login
* JWT-based authentication and authorization
* Search trains between multiple Indian cities
* Search trains by source, destination, and travel date
* View train details and seat availability
* Fare calculation based on travel class
* Support for multiple passengers in a single booking
* Automatic seat assignment
* Automatic PNR generation
* View personal booking history
* Cancel booked tickets
* H2 in-memory database with pre-seeded demo data
* MySQL-ready database configuration
* RESTful backend APIs
* React-based responsive frontend

---

## Tech Stack

### Backend

* **Java 17**
* **Spring Boot 3.3**
* **Spring Security**
* **JWT Authentication**
* **Spring Data JPA**
* **Hibernate**
* **H2 Database**
* **MySQL** — ready for production configuration
* **Maven**

### Frontend

* **React 18**
* **Vite**
* **React Router**
* **Axios**
* JavaScript
* HTML/CSS

---

## Project Structure

```text
train-ticket-booking/
│
├── backend/
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/
│   │   │   └── resources/
│   │   └── test/
│   │
│   └── pom.xml
│
├── frontend/
│   ├── src/
│   ├── public/
│   ├── package.json
│   └── vite.config.js
│
└── README.md
```

---

# Backend Setup

## Requirements

Make sure the following are installed:

* Java 17 or higher
* Maven

Verify the installations:

```bash
java -version
mvn -version
```

---

## Run the Backend

Navigate to the backend directory:

```bash
cd backend
```

Start the Spring Boot application:

```bash
mvn spring-boot:run
```

The backend will start at:

```text
http://localhost:8080
```

The REST API base URL is:

```text
http://localhost:8080/api
```

---

# H2 Database

RailYatra uses an **H2 in-memory database** by default.

No database installation or configuration is required for the demo.

The database is automatically:

1. Created when the application starts
2. Populated with demo data
3. Reset when the application restarts

### H2 Console

Open:

```text
http://localhost:8080/h2-console
```

Use the following credentials:

| Property | Value                 |
| -------- | --------------------- |
| JDBC URL | `jdbc:h2:mem:traindb` |
| Username | `sa`                  |
| Password | Leave empty           |

---

# Demo Account

A demo user is automatically created when the backend starts.

```text
Email: demo@traintix.com
Password: Demo@1234
```

You can use these credentials to test login and booking functionality.

---

# Frontend Setup

## Requirements

* Node.js 18 or higher
* npm

Verify the installation:

```bash
node -v
npm -v
```

---

## Run the Frontend

Navigate to the frontend directory:

```bash
cd frontend
```

Install dependencies:

```bash
npm install
```

Start the development server:

```bash
npm run dev
```

The frontend will be available at:

```text
http://localhost:5173
```

The frontend communicates with the backend through:

```text
http://localhost:8080/api
```

---

# API Configuration

The frontend API URL can be configured using:

```text
frontend/.env
```

Example:

```env
VITE_API_BASE_URL=http://localhost:8080/api
```

After changing the environment variable, restart the frontend development server.

---

# API Documentation

## Authentication APIs

### Register User

```http
POST /api/auth/register
```

Creates a new user account and returns a JWT token.

Authentication required: **No**

---

### Login

```http
POST /api/auth/login
```

Authenticates a user and returns a JWT token.

Authentication required: **No**

Example authentication header for protected APIs:

```http
Authorization: Bearer <JWT_TOKEN>
```

---

# Station APIs

### Get All Stations

```http
GET /api/stations
```

Returns the list of available railway stations/cities.

Authentication required: **No**

The demo database contains 10 seeded Indian cities.

---

# Train APIs

### Search Trains

```http
GET /api/trains/search
```

Search trains based on:

* Source
* Destination
* Travel date

Authentication required: **No**

Example:

```text
GET /api/trains/search?source=Mumbai&destination=Pune&date=2026-08-20
```

---

### Get Train Details

```http
GET /api/trains/{id}
```

Returns train information along with seat availability and fares for the requested date.

Example:

```text
GET /api/trains/1?date=2026-08-20
```

Authentication required: **No**

---

# Booking APIs

The following APIs require JWT authentication.

## Create Booking

```http
POST /api/bookings
```

Creates a booking for one or more passengers.

The system automatically:

* Checks seat availability
* Assigns seats
* Calculates fare
* Generates a PNR
* Creates the booking

Authentication required: **Yes**

Header:

```http
Authorization: Bearer <JWT_TOKEN>
```

---

## Get My Bookings

```http
GET /api/bookings/my
```

Returns all bookings belonging to the logged-in user.

Authentication required: **Yes**

---

## Get Booking by PNR

```http
GET /api/bookings/{pnr}
```

Returns booking information for the specified PNR.

Authentication required: **Yes**

Example:

```text
GET /api/bookings/PNR123456
```

---

## Cancel Booking

```http
DELETE /api/bookings/{pnr}
```

Cancels a booking using its PNR.

Authentication required: **Yes**

Example:

```text
DELETE /api/bookings/PNR123456
```

---

# API Summary

| Method | Endpoint              | Authentication | Description                        |
| ------ | --------------------- | -------------- | ---------------------------------- |
| POST   | `/api/auth/register`  | No             | Register a new user                |
| POST   | `/api/auth/login`     | No             | Login and receive JWT              |
| GET    | `/api/stations`       | No             | Get all stations                   |
| GET    | `/api/trains/search`  | No             | Search available trains            |
| GET    | `/api/trains/{id}`    | No             | Get train details and availability |
| POST   | `/api/bookings`       | Yes            | Create a booking                   |
| GET    | `/api/bookings/my`    | Yes            | Get user's bookings                |
| GET    | `/api/bookings/{pnr}` | Yes            | Get booking by PNR                 |
| DELETE | `/api/bookings/{pnr}` | Yes            | Cancel a booking                   |

---

# Supported Travel Classes

RailYatra supports the following train classes:

* Sleeper (SL)
* AC 3-Tier (3A)
* AC 2-Tier (2A)
* AC First Class (1A)
* Chair Car (CC)

Availability and fare are calculated based on the selected train, travel date, and class.

---

# Seat Availability Logic

This project uses a simplified seat availability system suitable for a demonstration application.

Availability is calculated as:

```text
Available Seats =
Total Seats - Seats in CONFIRMED Bookings
```

Availability is calculated when the user requests train information.

The application does not maintain a continuously updated seat counter.

---

# Seat Locking

Booking creation is protected using an **in-process application lock** to reduce the possibility of two simultaneous requests booking the same seats.

This approach is suitable for a single-instance demo application.

For a production system running multiple backend instances, database-level concurrency control should be implemented.

Possible production approaches include:

* Database row-level locking
* Optimistic locking using a version column
* Pessimistic locking
* Distributed locking
* Database constraints

---

# Switching to MySQL

RailYatra is designed to work with MySQL as well.

Open:

```text
backend/src/main/resources/application.yml
```

The file contains the MySQL configuration alongside the H2 configuration.

Enable the MySQL configuration and disable the H2 configuration.

Set:

```yaml
ddl-auto: update
```

This allows Hibernate to create/update the required database schema without dropping the database whenever the application restarts.

You will also need to configure:

* MySQL database URL
* Database username
* Database password

Example database:

```text
traindb
```

---

# Authentication Flow

RailYatra uses **JWT-based authentication**.

The authentication flow is:

```text
User
  │
  ▼
Register / Login
  │
  ▼
Spring Security
  │
  ▼
JWT Token Generated
  │
  ▼
Frontend Stores Token
  │
  ▼
Authorization Header
  │
  ▼
Protected API
```

Protected requests must include:

```http
Authorization: Bearer <JWT_TOKEN>
```

---

# Booking Flow

```text
Search Train
     │
     ▼
Select Train
     │
     ▼
Select Date & Class
     │
     ▼
Check Availability
     │
     ▼
Enter Passenger Details
     │
     ▼
Create Booking
     │
     ▼
Seat Assignment
     │
     ▼
Fare Calculation
     │
     ▼
PNR Generation
     │
     ▼
Booking Confirmed
```

---

# Database

The application uses Spring Data JPA and Hibernate for database interaction.

The demo version uses:

```text
H2 In-Memory Database
```

The application is also prepared for:

```text
MySQL
```

The database stores information related to:

* Users
* Stations
* Trains
* Bookings
* Passengers
* Seats
* Booking status
* Train classes
* Fares

---

# Development Ports

| Component           |              Port |
| ------------------- | ----------------: |
| Spring Boot Backend |            `8080` |
| React Frontend      |            `5173` |
| H2 Console          | `8080/h2-console` |

---

# Running the Complete Application

Open two terminals.

### Terminal 1 — Backend

```bash
cd backend
mvn spring-boot:run
```

Backend:

```text
http://localhost:8080
```

### Terminal 2 — Frontend

```bash
cd frontend
npm install
npm run dev
```

Frontend:

```text
http://localhost:5173
```

Then open the frontend in your browser and start using RailYatra.

---

# Example User Flow

1. Open the RailYatra frontend.
2. Register a new account or use the demo account.
3. Log in to receive a JWT token.
4. Search for trains between two cities.
5. Select a travel date.
6. View available seats and fares.
7. Select a travel class.
8. Enter passenger details.
9. Confirm the booking.
10. Receive an automatically generated PNR.
11. View the booking under **My Bookings**.
12. Cancel the booking when required.

---

# Testing

The REST APIs can be tested using tools such as:

* Postman
* Insomnia
* Browser
* React frontend

For protected endpoints, include the JWT token in the request header:

```http
Authorization: Bearer <JWT_TOKEN>
```

---

# Production Considerations

This project is designed primarily as a full-stack demonstration application.

For production deployment, the following improvements would be recommended:

* Replace H2 with MySQL/PostgreSQL
* Use database-level seat locking
* Add distributed locking for multiple backend instances
* Store secrets in environment variables
* Use HTTPS
* Implement refresh tokens
* Add rate limiting
* Add centralized exception handling
* Add comprehensive unit and integration tests
* Add payment gateway integration
* Add email/SMS booking notifications
* Add Docker support
* Add CI/CD pipeline
* Add monitoring and logging
* Implement proper transaction isolation for booking operations

---

# Future Enhancements

* Online payment integration
* E-ticket generation as PDF
* Email confirmation
* SMS notifications
* Train schedule management
* Admin dashboard
* User profile management
* Passenger management
* Waitlist and RAC support
* Refund processing
* PNR status tracking
* Advanced train filtering
* Railway quota support
* Production-grade distributed seat locking

---

# License

This project is intended for **educational and demonstration purposes**.

You are free to modify and extend the project for learning and development.






