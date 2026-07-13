RailYatra — Train Ticket Booking App
A full-stack train ticket booking application:

Backend: Java 17, Spring Boot 3.3, Spring Security + JWT, Spring Data JPA, H2 (in-memory, seeded with demo data) / MySQL-ready
Frontend: React 18 + Vite, React Router, Axios
What it does
Register / log in (JWT-based auth)
Search trains between 10 seeded Indian cities by date
See live seat availability and fares per class (Sleeper, AC 3-Tier, AC 2-Tier, AC First Class, Chair Car)
Book tickets for one or more passengers, with auto-assigned seat numbers and a generated PNR
View and cancel your bookings
Project structure
train-ticket-booking/
├── backend/     Spring Boot REST API (port 8080)
└── frontend/    React app (port 5173)
Running the backend
Requirements: Java 17+ and Maven.

cd backend
mvn spring-boot:run
The API starts on http://localhost:8080. It uses an in-memory H2 database that's recreated and re-seeded every time you start the app — no setup needed. The H2 console is available at http://localhost:8080/h2-console (JDBC URL: jdbc:h2:mem:traindb, user sa, empty password).

A demo account is seeded automatically:

Email: demo@traintix.com
Password: Demo@1234
Switching to MySQL
Open backend/src/main/resources/application.yml and swap the commented-out MySQL block in for the H2 block (instructions are inline in the file). Then set ddl-auto: update so Hibernate creates the schema instead of dropping it on restart.

Running the frontend
Requirements: Node.js 18+.

cd frontend
npm install
npm run dev
The app starts on http://localhost:5173 and expects the backend at http://localhost:8080/api (configurable via frontend/.env → VITE_API_BASE_URL).

Notes on the seat-locking logic
This is a demo, so availability is computed on read (total seats minus seats booked in CONFIRMED bookings for that train/date/class) rather than stored as a running counter, and booking creation is guarded with an in-process lock rather than a database-level constraint. That's fine for a single-instance demo but would need row-level locking or an optimistic-locking column before running with multiple backend instances.

API overview
Method	Endpoint	Auth required	Description
POST	/api/auth/register	–	Create an account, returns a JWT
POST	/api/auth/login	–	Log in, returns a JWT
GET	/api/stations	–	List all stations
GET	/api/trains/search	–	Search trains (source, destination, date)
GET	/api/trains/{id}	–	Train detail + availability for a date (date)
POST	/api/bookings	✅	Create a booking
GET	/api/bookings/my	✅	List the logged-in user's bookings
GET	/api/bookings/{pnr}	✅	Get one booking by PNR
DELETE	/api/bookings/{pnr}	✅	Cancel a booking
Send the JWT from login/register as Authorization: Bearer <token> on authenticated routes.
