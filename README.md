# Hospital Information System (HIS)

![Hospital Information System](https://github.com/user-attachments/assets/8d32b889-22ff-4cce-ad37-a9e0c68497a3)

A full-stack **Hospital Information System** for managing patients, doctors, nurses, and administrators. The platform supports appointment booking, medical scans, surgeries, patient history, and role-based dashboards — built as a database course project with a real three-tier architecture.

---

## Overview

HIS is a web application that digitizes core hospital workflows. Users sign up or log in, then access a dashboard tailored to their role. The system connects a **Next.js** frontend to an **ASP.NET Core REST API**, backed by a **PostgreSQL** database with relational integrity and cascade rules.

### Key capabilities

| Area | Description |
|------|-------------|
| **Authentication** | User registration, login, profile editing, and role-based access |
| **Appointments** | Patients request appointments; doctors accept or reject and add notes |
| **Medical scans** | Patients submit scan requests with images; doctors review and respond |
| **Surgeries** | Doctors schedule surgeries; nurses are assigned; all roles can view schedules |
| **Patient history** | Weight, height, smoking status, and disease records |
| **Doctor profiles** | CV-style profiles with education, specialization, and certifications |
| **Admin panel** | Manage doctors, nurses, and patients; oversee surgeries and statuses |
| **Activity tracking** | Audit-style activity log for user actions across the system |

---

## Tech Stack

### Frontend
- **Next.js 14** (App Router) + **React 18** + **TypeScript**
- **Tailwind CSS** + **Material Tailwind** for UI components
- **Framer Motion**, **GSAP**, **React Awesome Reveal** for animations
- **Font Awesome** / **Heroicons** / **React Icons** for iconography
- Client-side state via **localStorage** for session management

### Backend
- **ASP.NET Core Web API** (.NET)
- **Entity Framework Core** with **PostgreSQL** (Npgsql provider)
- **Swagger / OpenAPI** for API documentation
- **Dependency injection** with service interfaces (`IUserService`, `IDoctorService`, etc.)
- **CORS** enabled for local frontend development

### Database
- **PostgreSQL** with normalized relational schema
- **EF Core Migrations** for schema versioning
- Raw SQL script available in `DataBase/script.sql`

---

## User Roles & Features

### Patient
- Browse doctors and book appointments
- View appointment status (pending / accepted / rejected)
- Request diagnostic scans and upload images
- View surgeries assigned to them
- Manage personal medical history and diseases

### Doctor
- View and respond to appointment requests
- Add clinical notes to appointments
- Review and approve/reject patient scan requests
- Schedule surgeries and assign nurses
- Maintain a professional CV profile (education, specialization, certifications)

### Nurse
- View assigned surgeries and schedules
- See doctors they collaborate with on procedures

### Admin
- Add and manage doctors, nurses, and patients
- View surgery statistics per user
- Update appointment statuses
- Create surgeries and oversee hospital operations

---

## Project Structure

```
Hospital-Information-System/
├── API Endpoints/          # ASP.NET Core REST API
│   ├── Controllers/        # API route handlers (Admin, Doctor, Patient, Nurse, Profile)
│   ├── Data/               # AppDbContext (EF Core)
│   ├── Models/             # Entity models (User, Appointment, Scan, Surgery, etc.)
│   ├── Services/           # Business logic (Interfaces + Classes)
│   ├── Migrations/         # EF Core database migrations
│   └── Program.cs          # App entry point, DI, CORS, Swagger
│
├── Front end/              # Next.js frontend
│   ├── app/
│   │   ├── AdminDashboard/     # Admin management UI
│   │   ├── DoctorDashboard/    # Doctor workflow UI
│   │   ├── PatientDashboard/   # Patient workflow UI
│   │   ├── NurseDashboard/     # Nurse schedule UI
│   │   ├── LoginPage/          # Authentication
│   │   ├── SignupPage/         # Registration
│   │   ├── EditprofilePage/    # Profile & medical history editing
│   │   ├── UserProfile/        # Public user profiles & activity
│   │   ├── Home/               # Landing page
│   │   └── Components/         # Shared navbar, footer, etc.
│   └── package.json
│
└── DataBase/
    └── script.sql          # PostgreSQL schema (tables, FKs, constraints)
```

---

## Database Schema

Core tables and relationships:

| Table | Purpose |
|-------|---------|
| `User` | All users (patients, doctors, nurses, admins) |
| `appointment` | Patient–doctor appointment requests |
| `scan` | Diagnostic scan requests with images and doctor responses |
| `surgery` | Scheduled surgeries with cost, room, and duration |
| `sugery_nurses` | Many-to-many: nurses assigned to surgeries |
| `phistory` | Patient medical history (weight, height, smoking) |
| `disease` | Patient disease records linked to history |
| `activity` | User activity audit log |
| `doctorinfo` | Doctor CV / professional details |
| `deleteduser` | Soft-deleted user archive |

Foreign keys use `ON DELETE CASCADE` to keep referential integrity when users or surgeries are removed.

---

## API Endpoints (Summary)

| Controller | Base Route | Examples |
|------------|------------|----------|
| `ProfileController` | `/api/Profile` | `Login`, `CreateAccount`, `EditUser`, `DeleteUser` |
| `PatientController` | `/api/Patient` | `RequestAppointment`, `AddScan`, `GetPhistory`, `AllSurgeries` |
| `DoctorController` | `/api/Doctor` | `AllDoctors`, `SetAppintmentResponse`, `SetScanResponse`, `AddSurgery`, `SetDoctorCV` |
| `NurseController` | `/api/Nurse` | `AllNurses`, `SurgeryNurses`, `addToSurgery` |
| `AdminController` | `/api/Admin` | `AddDoctor`, `AddNurse`, `AddPatient`, `updateStatus`, `addSurgery` |

Swagger UI is available at `http://localhost:5221/swagger` when the API runs in Development mode.

---

## Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) (v18+)
- [.NET SDK](https://dotnet.microsoft.com/download) (6.0 or 8.0)
- [PostgreSQL](https://www.postgresql.org/download/)

### 1. Database setup

Create a PostgreSQL database, then either:

**Option A — Run the SQL script:**
```bash
psql -U postgres -d hospital_db -f "DataBase/script.sql"
```

**Option B — Use EF Core migrations:**
```bash
cd "API Endpoints"
dotnet ef database update
```

### 2. Backend configuration

Create `API Endpoints/appsettings.json` (this file is gitignored):

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Host=localhost;Port=5432;Database=hospital_db;Username=postgres;Password=YOUR_PASSWORD"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

Run the API:
```bash
cd "API Endpoints"
dotnet run
```

The API starts at **http://localhost:5221**.

### 3. Frontend setup

```bash
cd "Front end"
npm install
npm run dev
```

The frontend starts at **http://localhost:3000**.

> The frontend calls the API at `http://localhost:5221`. If you change the API port, update the `fetch` URLs in the `Front end/app/**/Handling/` and `HandleLogin.tsx` files accordingly.

### 4. First account

The **first registered user** is automatically assigned the **admin** role. Subsequent signups default to **patient**. Admins can then add doctors and nurses from the Admin Dashboard.

---

## Architecture

```
┌─────────────────────┐     HTTP/REST      ┌─────────────────────┐     EF Core      ┌──────────────┐
│   Next.js Frontend  │ ◄────────────────► │  ASP.NET Core API   │ ◄──────────────► │  PostgreSQL  │
│   (Port 3000)       │   JSON over fetch  │   (Port 5221)       │   Npgsql         │   Database   │
└─────────────────────┘                    └─────────────────────┘                  └──────────────┘
         │                                           │
         │  Role-based dashboards                    │  Service layer
         │  (Patient / Doctor / Nurse / Admin)       │  (Interfaces + DI)
         └───────────────────────────────────────────┘
```

---

## Development Notes

- **Authentication** is handled via email/password validation on the API; the frontend stores the logged-in user in `localStorage`.
- **Role-based routing** redirects users to the correct dashboard (`/PatientDashboard`, `/DoctorDashboard`, `/NurseDashboard`, `/AdminDashboard`).
- The backend follows a **repository-style service pattern** — controllers delegate to injected services for all business logic.
- Input validation includes email pattern checks and basic SQL-injection character filtering on login.

---

## License

This project was developed as an academic assignment for a Database Systems course.
