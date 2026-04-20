# 🏠 Gharwale — Real Estate Management System

A full-stack Real Estate Management System built with **Spring Boot** (backend) and **React + Vite** (frontend), backed by a **MySQL** database.

---

## 📁 Project Structure

```
Real-Estate-System-main/
├── gharwale-backend/       # Spring Boot REST API (port 8080)
└── gharwale-frontend/      # React + Vite SPA (port 5173)
```

---

## ✅ Prerequisites

Make sure the following tools are installed before getting started:

| Tool    | Version | Download |
|---------|---------|----------|
| Java    | 17+     | https://adoptium.net |
| Maven   | 3.8+    | https://maven.apache.org/download.cgi |
| Node.js | 18+     | https://nodejs.org |
| MySQL   | 8.0+    | https://dev.mysql.com/downloads |

Verify your installations:

```bash
java -version
mvn -version
node -version
mysql --version
```

---

## 🗄️ 1. Database Setup

### Create the Database

Log in to MySQL and run the provided schema script:

```bash
mysql -u root -p
```

```sql
SOURCE /path/to/gharwale_schema.sql;
```

> **Note:** The schema file (`gharwale_schema.sql`) is provided separately. It creates the `gharwale` database, all tables, and the required triggers.

### Verify the Database

```sql
SHOW DATABASES;
USE gharwale;
SHOW TABLES;
```

---

## 🔧 2. Backend Setup (Spring Boot)

### Configure Database Credentials

Open `gharwale-backend/src/main/resources/application.properties` and update the credentials to match your local MySQL setup:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/gharwale?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
spring.datasource.username=root
spring.datasource.password=YOUR_MYSQL_PASSWORD
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

spring.jpa.hibernate.ddl-auto=none
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQLDialect

server.port=8080

# Default admin credentials (used on first login)
app.admin.email=admin@gharwale.com
app.admin.password=admin123
app.admin.name=Admin
```

### Run the Backend

```bash
cd gharwale-backend
mvn spring-boot:run
```

The backend will start at **http://localhost:8080**.

You should see output like:

```
Started GharwaleApplication in X.XXX seconds
```

---

## 💻 3. Frontend Setup (React + Vite)

Open a **new terminal** and run:

```bash
cd gharwale-frontend
npm install
npm run dev
```

The frontend will start at **http://localhost:5173**.

> The Vite dev server is pre-configured to proxy all `/api/*` requests to `http://localhost:8080`. No additional configuration is needed.

---

## 🔐 4. Login Credentials

Navigate to **http://localhost:5173** in your browser.

### Admin Account

| Field    | Value                   |
|----------|-------------------------|
| Email    | `admin@gharwale.com`    |
| Password | `admin123`              |

### Agent Account

| Field    | Value                                        |
|----------|----------------------------------------------|
| Email    | Any agent's registered email in the database |
| Password | Any value (password is not validated in demo mode) |

---

## 🧭 Features by Role

### 🏢 Admin

| Feature            | Details                                              |
|--------------------|------------------------------------------------------|
| Dashboard          | Total agents, listings, deals, and revenue overview  |
| Agents             | Add / Edit / Delete / Activate / Deactivate agents   |
| Employment History | Auto-tracked via database triggers                   |
| Buildings & Units  | Full CRUD for buildings and their units              |
| Listings           | Create listings with owner lookup/create, filter by city or status |
| Deals              | View all sale and rental deals                       |
| Assignments        | Assign agents to listings                            |
| Reports            | Agent performance leaderboard and revenue summary    |

### 👤 Agent

| Feature     | Details                                        |
|-------------|------------------------------------------------|
| Dashboard   | Personal stats and recent activity             |
| My Listings | All open listings currently assigned to me     |
| My Deals    | My closed sales and rentals                    |
| Close Deal  | Close a sale or rental with buyer/tenant lookup|

---

## 🌐 API Endpoints

| Method | Endpoint                              | Description                        |
|--------|---------------------------------------|------------------------------------|
| POST   | `/auth/login`                         | Login (Admin or Agent)             |
| GET    | `/agents`                             | Get all agents                     |
| POST   | `/agents`                             | Create a new agent                 |
| PUT    | `/agents/{id}`                        | Update an agent                    |
| DELETE | `/agents/{id}`                        | Delete an agent                    |
| PATCH  | `/agents/{id}/status`                 | Activate or deactivate an agent    |
| GET    | `/agents/{id}/employment-history`     | Get agent's employment history     |
| GET    | `/agents/{id}/listings`               | Get listings assigned to an agent  |
| GET    | `/buildings`                          | Get all buildings                  |
| POST   | `/buildings`                          | Add a building                     |
| GET    | `/buildings/{id}/units`               | Get all units in a building        |
| POST   | `/buildings/{id}/units`               | Add a unit to a building           |
| GET    | `/listings`                           | Get all listings (filter: city, status) |
| POST   | `/listings`                           | Create a listing                   |
| PUT    | `/listings/{id}`                      | Update a listing                   |
| DELETE | `/listings/{id}`                      | Delete a listing                   |
| GET    | `/deals`                              | Get all deals (sales + rentals)    |
| POST   | `/deals/sale`                         | Close a sale deal                  |
| POST   | `/deals/rent`                         | Close a rental deal                |
| GET    | `/deals/agent/{id}`                   | Get deals by agent                 |
| POST   | `/assignments`                        | Assign an agent to a listing       |
| DELETE | `/assignments/{agentId}/{listingId}`  | Remove an agent assignment         |
| GET    | `/reports/summary`                    | Get dashboard summary              |
| GET    | `/reports/agent-performance`          | Get agent performance data         |

---

## 🗃️ Database Triggers

The following triggers are automatically applied by the schema and require no manual setup:

| Trigger                      | Action                                                    |
|------------------------------|-----------------------------------------------------------|
| `trgAgentAfterInsert`        | Creates an EmploymentHistory record when a new agent is added |
| `trgAgentAfterUpdate`        | Updates EmploymentHistory when an agent's status changes  |
| `trgSaleAfterInsert`         | Sets listing status to `Sold` after a sale deal is closed |
| `trgRentalAfterInsert`       | Sets listing status to `Rented` after a rental deal is closed |
| `trgCheckClosingDateSale`    | Validates that the closing date is on or after the listing date (sale) |
| `trgCheckClosingDateRental`  | Validates that the closing date is on or after the listing date (rental) |

---

## 🏗️ Architecture Overview

```
Browser (http://localhost:5173)
        │
        │  React + Axios → /api/*
        ▼
Vite Dev Server (proxy)
        │
        │  Forwards to http://localhost:8080
        ▼
Spring Boot REST API
        ├── AuthController
        ├── AgentController
        ├── BuildingController
        ├── ListingController
        ├── DealController
        ├── AssignmentController
        └── ReportController
                │
                │  JPA / Hibernate
                ▼
        MySQL Database (gharwale)
```

---

## 🛠️ Tech Stack

| Layer     | Technology                        |
|-----------|-----------------------------------|
| Frontend  | React 18, Vite 5, React Router 6, Axios |
| Backend   | Spring Boot 3.2, Spring Data JPA, Lombok |
| Database  | MySQL 8                           |
| Build     | Maven (backend), npm (frontend)   |

---

## ⚠️ Common Issues

**Backend fails to connect to MySQL**
- Make sure MySQL is running: `sudo systemctl start mysql` (Linux) or start via MySQL Workbench / System Preferences (Mac/Windows).
- Double-check the username and password in `application.properties`.
- Ensure the `gharwale` database exists and the schema has been imported.

**Port already in use**
- Backend (8080): Change `server.port` in `application.properties`.
- Frontend (5173): Change the `port` in `gharwale-frontend/vite.config.js` and update the CORS allowed origins in `CorsConfig.java` accordingly.

**Frontend shows network errors**
- Make sure the backend is running before starting the frontend.
- Confirm the backend is reachable at `http://localhost:8080`.

**`mvn` command not found**
- Ensure Maven is installed and its `bin` directory is added to your system `PATH`.
