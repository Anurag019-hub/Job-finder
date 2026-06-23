# Job Search Portal

A full-stack job search platform built with React, Node.js, Express, and MongoDB. The application connects job seekers with recruiters through a streamlined interface for posting, discovering, and applying to jobs, with role-based dashboards, cloud-hosted file uploads, and persistent authentication.

---

## Overview

The modern hiring process is fragmented across dozens of platforms, spreadsheets, and email threads. Job seekers struggle to track applications, while recruiters lack a centralized pipeline for managing postings and evaluating candidates.

This platform solves both sides of the equation:

- **Job Seekers** get a single interface to search openings by keyword, location, industry, and salary range, apply with a single click, upload resumes, and track every application status in real time.
- **Recruiters** get a dedicated admin panel to register companies, create detailed job listings, review applicants, and accept or reject candidates, all from one dashboard.

The system is designed as a production-grade monorepo with a clear separation between the React frontend and the Express REST API backend, communicating over a versioned API (`/api/v1`).

---

## Key Features

### For Job Seekers
- **Keyword Search** — Search jobs by title or description with regex-powered queries.
- **Advanced Filtering** — Filter results by location, industry, and salary range using a sidebar filter panel.
- **Category Browsing** — Browse jobs through an interactive category carousel on the home page.
- **One-Click Apply** — Apply to any job with a single action; duplicate applications are automatically prevented.
- **Application Tracking** — View all submitted applications with real-time status updates (Pending, Accepted, Rejected).
- **Profile Management** — Update bio, skills, phone number, email, and resume at any time through a modal dialog.
- **Resume Upload** — Upload resumes via Cloudinary with original filename preservation.
- **Profile Photo** — Upload a profile photo during registration, stored on Cloudinary.

### For Recruiters
- **Company Registration** — Register one or more companies with name, description, website, location, and logo.
- **Company Management** — Update company details and branding at any time.
- **Job Posting** — Create detailed job listings with title, description, requirements, salary, experience level, location, job type, and number of open positions.
- **Applicant Review** — View all applicants for a given job, sorted by most recent.
- **Status Management** — Accept or reject applicants with a single status update.
- **Admin Dashboard** — Dedicated admin views for managing companies and jobs, protected by role-based route guards.

### Platform-Wide
- **JWT Authentication** — Secure cookie-based authentication with 24-hour token expiry.
- **Role-Based Access Control** — Separate workflows and route protection for `student` and `recruiter` roles.
- **Persistent State** — Redux state is persisted to localStorage via `redux-persist`, so sessions survive page refreshes.
- **Responsive UI** — Built with Tailwind CSS and Shadcn/UI (Radix primitives) for a polished, accessible interface.
- **Toast Notifications** — User feedback through Sonner toast notifications for all key actions.

---

## Technology Stack

| Layer              | Technology                                                   |
| ------------------ | ------------------------------------------------------------ |
| **Frontend**       | React 18, Vite, React Router v6                             |
| **UI Components**  | Shadcn/UI (Radix UI primitives), Tailwind CSS, Lucide Icons |
| **Animations**     | Framer Motion                                                |
| **State Management** | Redux Toolkit, React-Redux, Redux Persist                 |
| **HTTP Client**    | Axios                                                        |
| **Backend**        | Node.js, Express 4                                           |
| **Database**       | MongoDB with Mongoose ODM                                    |
| **Authentication** | JSON Web Tokens (jsonwebtoken), bcrypt.js                    |
| **File Uploads**   | Multer (memory storage), Cloudinary, DataURI                 |
| **Dev Tooling**    | Nodemon, ESLint, PostCSS, Autoprefixer                       |

---

## Architecture

```
┌─────────────────────────────┐
│         React Frontend      │
│  (Vite Dev Server :5173)    │
│                             │
│  Redux Store ◄── Axios ─────┼──── HTTP (JSON + Cookies) ────┐
│  Redux Persist              │                                │
│  React Router               │                                ▼
└─────────────────────────────┘                    ┌──────────────────────┐
                                                   │   Express API :8000  │
                                                   │                      │
                                                   │  ┌────────────────┐  │
                                                   │  │  Middleware     │  │
                                                   │  │  - CORS        │  │
                                                   │  │  - Cookie Parse│  │
                                                   │  │  - JWT Auth    │  │
                                                   │  │  - Multer      │  │
                                                   │  └───────┬────────┘  │
                                                   │          ▼           │
                                                   │  ┌────────────────┐  │
                                                   │  │  Controllers   │  │
                                                   │  │  - User        │  │
                                                   │  │  - Job         │  │
                                                   │  │  - Application │  │
                                                   │  │  - Company     │  │
                                                   │  └───────┬────────┘  │
                                                   └──────────┼──────────┘
                                                              │
                                          ┌───────────────────┼──────────────┐
                                          ▼                                  ▼
                                   ┌─────────────┐                   ┌──────────────┐
                                   │  MongoDB     │                   │  Cloudinary   │
                                   │  (Atlas)     │                   │  (File CDN)   │
                                   └─────────────┘                   └──────────────┘
```

**Data flow:** The React frontend communicates with the Express backend exclusively through RESTful JSON endpoints under `/api/v1`. Authentication tokens are stored as HTTP cookies. File uploads (profile photos, resumes, company logos) are received by Multer as in-memory buffers, converted to Data URIs, and uploaded to Cloudinary. The returned CDN URLs are then stored in MongoDB documents.

---

## Project Structure

```
jobportal-yt/
├── backend/
│   ├── controllers/
│   │   ├── application.controller.js  # Apply, list, review, update status
│   │   ├── company.controller.js      # Register, get, update companies
│   │   ├── job.controller.js          # Post, search, get jobs
│   │   └── user.controller.js         # Register, login, logout, update profile
│   ├── middlewares/
│   │   ├── isAuthenticated.js         # JWT verification middleware
│   │   └── mutler.js                  # Multer memory storage config
│   ├── models/
│   │   ├── application.model.js       # Application schema (job, applicant, status)
│   │   ├── company.model.js           # Company schema (name, logo, location)
│   │   ├── job.model.js               # Job schema (title, salary, requirements)
│   │   └── user.model.js              # User schema (profile, role, credentials)
│   ├── routes/
│   │   ├── application.route.js       # /api/v1/application/*
│   │   ├── company.route.js           # /api/v1/company/*
│   │   ├── job.route.js               # /api/v1/job/*
│   │   └── user.route.js              # /api/v1/user/*
│   ├── utils/
│   │   ├── cloudinary.js              # Cloudinary SDK configuration
│   │   ├── datauri.js                 # Buffer-to-DataURI converter
│   │   └── db.js                      # MongoDB connection handler
│   ├── index.js                       # Express app entry point
│   └── package.json
│
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   │   ├── admin/                 # Recruiter dashboard components
│   │   │   │   ├── AdminJobs.jsx
│   │   │   │   ├── AdminJobsTable.jsx
│   │   │   │   ├── Applicants.jsx
│   │   │   │   ├── ApplicantsTable.jsx
│   │   │   │   ├── Companies.jsx
│   │   │   │   ├── CompaniesTable.jsx
│   │   │   │   ├── CompanyCreate.jsx
│   │   │   │   ├── CompanySetup.jsx
│   │   │   │   ├── PostJob.jsx
│   │   │   │   └── ProtectedRoute.jsx
│   │   │   ├── auth/                  # Login and Signup forms
│   │   │   │   ├── Login.jsx
│   │   │   │   └── Signup.jsx
│   │   │   ├── shared/                # Navbar and Footer
│   │   │   │   ├── Navbar.jsx
│   │   │   │   └── Footer.jsx
│   │   │   ├── ui/                    # Shadcn/UI primitives
│   │   │   ├── Browse.jsx
│   │   │   ├── CategoryCarousel.jsx
│   │   │   ├── FilterCard.jsx
│   │   │   ├── HeroSection.jsx
│   │   │   ├── Home.jsx
│   │   │   ├── Job.jsx
│   │   │   ├── JobDescription.jsx
│   │   │   ├── Jobs.jsx
│   │   │   ├── LatestJobCards.jsx
│   │   │   ├── LatestJobs.jsx
│   │   │   ├── Profile.jsx
│   │   │   ├── AppliedJobTable.jsx
│   │   │   └── UpdateProfileDialog.jsx
│   │   ├── hooks/                     # Custom data-fetching hooks
│   │   │   ├── useGetAllAdminJobs.jsx
│   │   │   ├── useGetAllCompanies.jsx
│   │   │   ├── useGetAllJobs.jsx
│   │   │   ├── useGetAppliedJobs.jsx
│   │   │   └── useGetCompanyById.jsx
│   │   ├── redux/                     # Redux Toolkit slices
│   │   │   ├── authSlice.js
│   │   │   ├── jobSlice.js
│   │   │   ├── companySlice.js
│   │   │   ├── applicationSlice.js
│   │   │   └── store.js
│   │   ├── utils/
│   │   │   └── constant.js           # API base URL constants
│   │   ├── App.jsx                    # Route definitions
│   │   ├── main.jsx                   # App entry with Provider & Persist
│   │   └── index.css                  # Global styles & Tailwind directives
│   ├── tailwind.config.js
│   ├── vite.config.js
│   └── package.json
│
└── README.md
```

---

## Installation and Setup

### Prerequisites

- **Node.js** >= 18.x
- **npm** >= 9.x
- **MongoDB** instance (local or [MongoDB Atlas](https://www.mongodb.com/atlas))
- **Cloudinary** account ([sign up free](https://cloudinary.com/))

### Environment Variables

Create a `.env` file in the `backend/` directory:

```env
# Server
PORT=8000

# MongoDB
MONGO_URI=mongodb+srv://<username>:<password>@cluster.mongodb.net/jobportal

# JWT
SECRET_KEY=your_jwt_secret_key_here

# Cloudinary
CLOUD_NAME=your_cloudinary_cloud_name
API_KEY=your_cloudinary_api_key
API_SECRET=your_cloudinary_api_secret
```

### Backend Setup

```bash
# Navigate to the backend directory
cd backend

# Install dependencies
npm install

# Start the development server (with hot reload via Nodemon)
npm run dev
```

The API server will start on `http://localhost:8000`.

### Frontend Setup

```bash
# Navigate to the frontend directory
cd frontend

# Install dependencies
npm install

# Start the Vite development server
npm run dev
```

The frontend will start on `http://localhost:5173`.

### Database Configuration

No manual schema setup is required. Mongoose will automatically create collections on first write. Ensure your `MONGO_URI` points to a running MongoDB instance with network access from your development machine.

---

## API Documentation

All endpoints are prefixed with `/api/v1`. Protected routes require a valid JWT token in the `token` cookie.

### User Routes (`/api/v1/user`)

| Route              | Method | Description                        | Access        |
| ------------------ | ------ | ---------------------------------- | ------------- |
| `/register`        | POST   | Create a new user account          | Public        |
| `/login`           | POST   | Authenticate and receive JWT token | Public        |
| `/logout`          | GET    | Clear authentication cookie        | Public        |
| `/profile/update`  | POST   | Update user profile and resume     | Authenticated |

### Job Routes (`/api/v1/job`)

| Route            | Method | Description                              | Access        |
| ---------------- | ------ | ---------------------------------------- | ------------- |
| `/post`          | POST   | Create a new job listing                 | Authenticated (Recruiter) |
| `/get`           | GET    | Search all jobs (supports `?keyword=`)   | Authenticated |
| `/get/:id`       | GET    | Get a single job with its applications   | Authenticated |
| `/getadminjobs`  | GET    | Get all jobs created by the logged-in recruiter | Authenticated (Recruiter) |

### Application Routes (`/api/v1/application`)

| Route                 | Method | Description                           | Access        |
| --------------------- | ------ | ------------------------------------- | ------------- |
| `/apply/:id`          | GET    | Apply to a job by job ID              | Authenticated (Job Seeker) |
| `/get`                | GET    | Get all applications by current user  | Authenticated (Job Seeker) |
| `/:id/applicants`     | GET    | Get all applicants for a specific job | Authenticated (Recruiter) |
| `/status/:id/update`  | POST   | Update application status             | Authenticated (Recruiter) |

### Company Routes (`/api/v1/company`)

| Route          | Method | Description                              | Access        |
| -------------- | ------ | ---------------------------------------- | ------------- |
| `/register`    | POST   | Register a new company                   | Authenticated (Recruiter) |
| `/get`         | GET    | Get all companies owned by current user  | Authenticated (Recruiter) |
| `/get/:id`     | GET    | Get a single company by ID               | Authenticated |
| `/update/:id`  | PUT    | Update company details and logo          | Authenticated (Recruiter) |

---

## Database Design

The application uses four Mongoose models with the following relationships:

```
┌──────────────┐       ┌──────────────┐
│     User     │       │   Company    │
│──────────────│       │──────────────│
│ fullname     │       │ name         │
│ email (uniq) │       │ description  │
│ phoneNumber  │       │ website      │
│ password     │  1──N │ location     │
│ role (enum)  │◄──────│ logo         │
│ profile      │       │ userId (FK)  │
│  ├─ bio      │       └──────┬───────┘
│  ├─ skills[] │              │
│  ├─ resume   │              │ 1
│  ├─ resumeName│             │
│  ├─ profilePhoto│           │
│  └─ company  │──────────────┘
└──────┬───────┘
       │ 1
       │
       │              ┌──────────────┐
       │         N    │     Job      │
       ├─────────────►│──────────────│
       │ (created_by) │ title        │
       │              │ description  │
       │              │ requirements[]│
       │              │ salary       │
       │              │ experienceLevel│
       │              │ location     │
       │              │ jobType      │
       │              │ position     │
       │              │ company (FK) │
       │              │ created_by(FK)│
       │              │ applications[]│
       │              └──────┬───────┘
       │                     │ 1
       │                     │
       │              ┌──────┴───────┐
       │         N    │ Application  │
       └─────────────►│──────────────│
         (applicant)  │ job (FK)     │
                      │ applicant(FK)│
                      │ status (enum)│
                      │  pending     │
                      │  accepted    │
                      │  rejected    │
                      └──────────────┘
```

**Key relationships:**
- A **User** with role `recruiter` can own multiple **Companies** (one-to-many via `userId`).
- A **Recruiter** creates **Jobs**, each linked to one **Company** (many-to-one via `company` and `created_by`).
- A **Job Seeker** submits **Applications** to **Jobs** (many-to-many through the Application junction model).
- Each **Application** tracks status as an enum: `pending`, `accepted`, or `rejected`.
- All models include automatic `createdAt` and `updatedAt` timestamps.

---

## Authentication and Security

### JWT Authentication
- Tokens are generated on login using `jsonwebtoken` with a configurable `SECRET_KEY`.
- Tokens are stored as HTTP cookies with a 24-hour expiry (`maxAge: 86400000ms`).
- The `sameSite: 'strict'` flag prevents CSRF attacks.

### Password Hashing
- All passwords are hashed using `bcrypt.js` with a salt round of 10 before storage.
- Raw passwords are never stored or logged.

### Protected Routes
- **Backend:** The `isAuthenticated` middleware extracts and verifies the JWT from the cookie on every protected route. The decoded `userId` is attached to `req.id` for downstream use.
- **Frontend:** The `ProtectedRoute` component wraps all `/admin/*` routes, redirecting unauthorized users.

### Role-Based Access Control
- Users register as either `student` (job seeker) or `recruiter`.
- Role is validated at login — a user cannot authenticate with a mismatched role.
- Admin routes (`/admin/*`) are guarded on both the frontend (ProtectedRoute component) and through business logic in controllers.

### Input Validation
- All required fields are validated server-side before database operations.
- Duplicate email addresses are rejected at registration.
- Duplicate job applications are prevented per user-job pair.
- Company names are enforced as unique.

---

## User Workflows

### Job Seeker

1. **Registration** — Create an account by providing name, email, phone number, password, and profile photo. Select the "Student" role.
2. **Login** — Authenticate with email, password, and role selection. A JWT cookie is set automatically.
3. **Discover Jobs** — Browse the home page with the hero search bar, category carousel, and latest job listings.
4. **Search and Filter** — Use the keyword search to find jobs by title/description. Navigate to the Jobs page to apply sidebar filters by location, industry, or salary range.
5. **View Job Details** — Click any job card to see the full description, requirements, experience level, salary, and current applicant count.
6. **Apply** — Click "Apply Now" on a job description page. The system prevents duplicate applications.
7. **Track Applications** — Visit the Profile page to view all submitted applications in a table with company name, job title, date, and current status.
8. **Update Profile** — Open the profile update dialog to modify personal information, skills, and upload a new resume.

### Recruiter

1. **Registration** — Create an account with the "Recruiter" role.
2. **Register a Company** — Navigate to the admin Companies panel and register a new company by name.
3. **Complete Company Profile** — Update the company with a description, website URL, location, and logo upload.
4. **Post Jobs** — Create job listings under a registered company, specifying title, description, comma-separated requirements, salary, experience level, location, job type, and number of positions.
5. **Manage Listings** — View all posted jobs in the admin Jobs table with creation dates and applicant counts.
6. **Review Applicants** — Click into any job to see a table of all applicants with their profile details.
7. **Accept or Reject** — Update each application's status to `accepted` or `rejected` through the applicants table.

---

## Performance Considerations

- **Indexed Queries** — MongoDB automatically indexes `_id` fields. The `email` field on the User model is marked as `unique`, which creates an index for fast lookup during authentication.
- **Regex Search** — Job search uses case-insensitive regex on `title` and `description` fields via MongoDB's `$or` operator, providing flexible keyword matching.
- **Population Control** — Mongoose `.populate()` is used selectively to load only the related data needed for each endpoint, avoiding unnecessary joins.
- **Sorted Results** — All list queries are sorted by `createdAt: -1` to surface the most recent items first.
- **Memory-Based Uploads** — Multer uses in-memory storage (`memoryStorage`), converting files to Data URIs for direct Cloudinary upload without writing temporary files to disk.
- **State Persistence** — Redux Persist stores the application state in `localStorage`, reducing redundant API calls on page reload.
- **Client-Side Caching** — Custom hooks (`useGetAllJobs`, `useGetAllCompanies`, etc.) centralize data fetching and dispatch results to the Redux store, acting as a client-side cache layer.

---

## Challenges and Solutions

| Challenge | Decision | Rationale |
| --------- | -------- | --------- |
| **File upload without disk I/O** | Multer memory storage + DataURI + Cloudinary | Avoids temporary file management on the server. Files are converted to base64 Data URIs in memory and uploaded directly to Cloudinary, keeping the server stateless. |
| **Session persistence across refreshes** | Redux Persist with `localStorage` | Users expect their session and UI state to survive page reloads. Redux Persist serializes the entire Redux store to localStorage transparently. |
| **Role-based UI divergence** | Separate component trees (`/admin/*` vs public routes) | Rather than conditionally rendering within shared components, the recruiter and job seeker interfaces are fully separated at the routing level for clarity and maintainability. |
| **Preventing duplicate applications** | Pre-check with `Application.findOne()` | Before creating an application, the controller checks for an existing record with the same `job` + `applicant` combination, returning a clear error message if found. |
| **Consistent UI primitives** | Shadcn/UI (Radix) + Tailwind CSS | Radix primitives provide accessible, unstyled components. Tailwind handles styling. This combination avoids CSS framework lock-in while ensuring accessibility compliance. |
| **Cookie-based auth vs. header-based** | HTTP cookies with `sameSite: strict` | Cookies are automatically sent with every request, simplifying the frontend. The `sameSite` flag provides built-in CSRF protection without additional middleware. |

---

## Future Enhancements

- **AI-Powered Job Recommendations** — Implement a recommendation engine using NLP to match job descriptions with user skills and resume content.
- **Resume Parsing and Matching** — Integrate a resume parser to automatically extract skills, experience, and education, then rank candidates by fit score.
- **Interview Scheduling** — Add a calendar integration (Google Calendar API) for recruiters to schedule interviews directly within the platform.
- **Real-Time Notifications** — Implement WebSocket-based notifications (Socket.IO) to alert job seekers of status changes and recruiters of new applications.
- **Analytics Dashboard** — Provide recruiters with metrics on job listing performance: views, application rates, and time-to-fill.
- **Real-Time Messaging** — Enable direct communication between recruiters and applicants through an in-app chat system.
- **Email Notifications** — Send transactional emails (Nodemailer / SendGrid) for registration confirmation, application updates, and interview invitations.
- **Pagination and Infinite Scroll** — Add server-side pagination with cursor-based navigation for large datasets.
- **Saved Jobs / Bookmarks** — Allow job seekers to bookmark jobs and return to them later.
- **Advanced Search** — Add multi-field search with filters for experience level, job type (remote/on-site/hybrid), and date posted.

---

## Deployment

### Recommended Production Architecture

| Component    | Platform                          |
| ------------ | --------------------------------- |
| **Frontend** | Vercel or Netlify (static build)  |
| **Backend**  | Render, Railway, or AWS EC2       |
| **Database** | MongoDB Atlas (managed cluster)   |
| **File CDN** | Cloudinary (already integrated)   |

### Deployment Steps

1. **Frontend** — Run `npm run build` in the `frontend/` directory. Deploy the `dist/` folder to Vercel or Netlify. Set the API base URL in `constant.js` to the production backend URL.
2. **Backend** — Deploy the `backend/` directory to Render or Railway. Set all environment variables (`MONGO_URI`, `SECRET_KEY`, `CLOUD_NAME`, `API_KEY`, `API_SECRET`) in the platform's dashboard.
3. **Database** — Use MongoDB Atlas with IP whitelisting configured for your backend's deployment IP.
4. **CORS** — Update the `corsOptions.origin` in `backend/index.js` to match the production frontend URL.

---

## Screenshots

> Screenshots can be added here to showcase the application UI.

| Screen | Description |
| ------ | ----------- |
| **Home Page** | Hero section with search bar, category carousel, and latest job listings. |
| **Jobs Page** | Sidebar filters with job cards displaying company, location, salary, and job type. |
| **Job Description** | Detailed job view with requirements, experience level, and apply button. |
| **Profile Page** | User profile with bio, skills, resume link, and applied jobs table. |
| **Admin — Companies** | Recruiter dashboard for managing registered companies. |
| **Admin — Post Job** | Form for creating new job listings with all required fields. |
| **Admin — Applicants** | Table view of all applicants for a job with status management. |
| **Login / Signup** | Authentication forms with role selection (Student / Recruiter). |

---

## Contributors

| Name | Role | GitHub |
| ---- | ---- | ------ |
| Anurag | Full-Stack Developer | [Anurag019-hub](https://github.com/Anurag019-hub) |

---

## License

This project is licensed under the [ISC License](https://opensource.org/licenses/ISC).

---

## Acknowledgements

- [Shadcn/UI](https://ui.shadcn.com/) — Accessible component primitives built on Radix UI.
- [Radix UI](https://www.radix-ui.com/) — Unstyled, accessible UI component library.
- [Tailwind CSS](https://tailwindcss.com/) — Utility-first CSS framework.
- [Cloudinary](https://cloudinary.com/) — Cloud-based media management platform.
- [MongoDB Atlas](https://www.mongodb.com/atlas) — Managed cloud database service.
- [Redux Toolkit](https://redux-toolkit.js.org/) — Official toolset for efficient Redux development.
- [Vite](https://vitejs.dev/) — Next-generation frontend build tool.
- [Framer Motion](https://www.framer.com/motion/) — Production-ready animation library for React.
- [Lucide](https://lucide.dev/) — Beautiful, consistent icon set.
