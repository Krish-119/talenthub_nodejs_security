# TalentHub - Campus Placement Management System

Node.js + Express backend, SQLite database, plain HTML/CSS/JavaScript frontend (no build step).

## Run it (2 steps)

1. Install Node.js 16 or newer from https://nodejs.org (check with `node -v`).
2. Open a terminal in this folder and run:

       npm start

   (or `node server.js`; on Windows you can also double-click `start.bat`)

Then open **http://localhost:3000** in your browser. Stop with Ctrl+C.

All dependencies are already included in `node_modules`, so no `npm install` and no internet are needed.
If you ever delete `node_modules`, run `npm install` once to restore it.

## Demo logins (password for every account: `password123`)

| Role        | Email                         |
|-------------|-------------------------------|
| Coordinator | coordinator@talenthub.test    |
| Student     | rahul@talenthub.test (also priya@, arjun@, sneha@, kiran@) |
| Company     | abc@talenthub.test (also xyz@) |

Students can also register themselves on the login page.

## What each user can do

- **Student**: see open jobs with an eligible / not eligible badge (and the reason), apply, track application status, see interview details, accept or decline offers.
- **Company**: post jobs (min CGPA, max backlogs, allowed branches, deadline), view applicants, shortlist or reject, schedule interviews, record results, issue offers.
- **Placement coordinator**: placement statistics (placed students, rate, offers, average / highest CTC, by branch, by company), update student CGPA and backlogs, add company accounts.

## Business rules implemented

- Eligibility = CGPA >= job minimum AND active backlogs <= job maximum AND branch in the job's allowed branches.
- A student can apply once per job, only before the deadline and only while the job is open.
- Pipeline order is enforced: applied -> shortlisted -> interview scheduled -> interview passed -> offer issued -> accepted / declined.
- A company can only act on its own jobs and applicants; a student can only see and answer their own offers.
- One-offer policy (an assumption, easy to change in `src/services/studentService.js`): once a student accepts an offer they are "placed" and cannot apply to or accept other jobs.

## Architecture (matches the layered design in the lab scenario)

    server.js                    starts Express, security headers, mounts routers
    src/routers/        (Router)      receives HTTP requests, checks the user's role
    src/services/       (Service)     business rules: eligibility, status flow, ownership
    src/repositories/   (Repository)  all SQL lives here
    src/db.js, schema.js (Model/DB)   SQLite tables: users, students, companies, job_listings,
                                      job_allowed_branches, applications, interview_slots, offers
    src/auth.js, validate.js          password hashing, sessions, input validation
    src/seed.js                       demo data (only created on first run)
    public/                           the web frontend

## Data and resetting

Data is stored in `data/talenthub.db`, created on first run. To start fresh with demo data:
stop the server, run `npm run reset-db`, and start it again.

## Is it safe to run?

- It only listens on `127.0.0.1` (your own computer); other devices on your network cannot reach it.
- It makes no outgoing network requests and collects nothing. The only files it writes are inside `data/`.
- Only two third-party packages are used: `express` (web server) and `sql.js` (SQLite compiled to WebAssembly), plus express's own dependencies.
- Passwords are stored hashed (scrypt), inputs are validated, all SQL uses parameters, the frontend never inserts user text as HTML, and a Content-Security-Policy header is set.
- The demo accounts share a well-known password. This is a learning/demo project: change those passwords (or remove the demo data) before ever exposing it beyond your own machine.
