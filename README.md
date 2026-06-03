# 🎓 LNMIIT Faculty Appraisal System

### *A Full-Stack Web Application for End-to-End Annual Faculty Performance Appraisal & Rubric-Based Evaluation*

---

## 📌 Project Overview
The **Faculty Appraisal System** is a production-grade web application custom-built for **The LNM Institute of Information Technology (LNMIIT), Jaipur**. It digitizes the university's annual faculty performance appraisal process—replacing legacy paper forms and spreadsheets with a secure, automated web platform.

### 🎥 [Watch the 2-Minute Demo Video (Loom Placeholder)]()
*Include a Loom video walkthrough here to showcase the live workflow to recruiters.*

---

## 🛠️ Tech Stack

| Frontend | Backend | Database & DevOps |
| :--- | :--- | :--- |
| **React** (v18.x SPA) | **Node.js** & **Express** | **MySQL 8.x** (InnoDB) |
| **Vite** (Build Tool) | **JWT** (Authentication) | **Puppeteer** (PDF Engine) |
| **React Router DOM** (v6.x) | **Bcryptjs** (Security) | **Node-cache** (In-Memory Caching) |
| **Vanilla CSS** (Custom Design) | **Nodemailer** (Notifications) | **Node-cron** (Scheduled Jobs) |

---

## 🚀 Key Technical Highlights (Resume Showcases)

### 1. 🎛️ Drag-and-Drop Dynamic Form Builder (No-Code Engine)
Admins (DOFA Office) can create custom appraisal sections and input fields (Text, Textarea, and Interactive Tables) dynamically **without writing any code or modifying database tables**.
* **Database Design**: Implemented using the **Entity-Attribute-Value (EAV)** pattern across three tables (`dynamic_sections`, `dynamic_fields`, and `dynamic_responses`) with JSON columns for flexible row data.
* **Autosave Engine**: Implemented a **2-second debounced autosave** system that silently saves drafts in the background to minimize server requests and network congestion, complete with a visual sync indicator.

### 2. ⚡ Solving the N+1 Query Problem (~95% Performance Boost)
* **The Issue**: Relational publication details (co-authors, editors) were originally fetched inside a loop, triggering `1 + (2 * N)` queries for `N` publications.
* **The Solution**: Redesigned endpoints to fetch all entries, authors, and editors using advanced SQL `JOIN` statements in exactly 3 database queries, grouping them in memory.
* **Result**: Reduced response times by **~96%** (from `150ms` down to `5ms` on average for large lists).

### 3. 📄 Puppeteer-based PDF Generation
* Generates pixel-perfect, LNMIIT-branded A4 PDF files of submissions.
* Uses an HTML+CSS template to dynamically compile and format 20+ sections (including dynamic EAV fields) on-the-fly, avoiding complex coordinates required by canvas-based PDF writers.

### 4. 🗃️ Versioned Submission Snapshots & Auditing
* Every time a faculty member submits or resubmits, the system serializes all database sections into a single **LONGTEXT JSON snapshot** inside the `submission_versions` table. This creates a permanent audit log, allowing reviewers to compare changes across versions.

---

## 👥 User Roles & Workflow

The platform maintains **5 distinct roles**, each with separate access levels, workflows, and dashboard routes:

```
                  ┌────────────────────────────────────────┐
                  │          Faculty Member (Drafts)       │
                  └───────────────────┬────────────────────┘
                                      │ (Submits)
                                      ▼
                  ┌────────────────────────────────────────┐
                  │         HOD Dashboard (View Only)      │
                  └───────────────────┬────────────────────┘
                                      │
                                      ▼
                  ┌────────────────────────────────────────┐
                  │         DOFA Reviewer (Grading)        │
                  └─────────┬────────────────────┬─────────┘
                            │                    │
                (Approved)  ▼                    ▼  (Sent Back)
       ┌──────────────────────────┐        ┌──────────────────────────┐
       │   Increment Recommendation│        │   Faculty Edits Enabled  │
       │       (Sheet 2 & 3)      │        │      (With Comments)     │
       └──────────────────────────┘        └──────────────────────────┘
```

* **Faculty**: Guided multi-step forms with evidence uploads.
* **HOD**: Department-level read-only statistics and status monitoring.
* **DOFA Reviewer**: Reviews submissions, adds section-level comments, overrides automated rubric scores, and grades forms.
* **DOFA Office**: System-level admin (manages sessions, users, rubrics, dynamic sections, and exports bulk Excel data).
* **System Admin**: General configurations and maintenance.

---

## 📐 Database Schema & Architecture

The database consists of **30+ tables** with InnoDB constraints. Below is a summary of the core schema categories:

### Core Tables
* `users`: Auth credentials, roles, onboarding states.
* `faculty_information`: Faculty profiles and demographics.
* `submissions`: Master tracker per faculty per academic year.
* `appraisal_sessions`: Configured appraisal periods and deadlines.

### EAV Dynamic Forms
* `dynamic_sections`: Section metadata (Form A/B, parent sections).
* `dynamic_fields`: Input fields schema (text, textareas, tables).
* `dynamic_responses`: Serialized JSON values mapped to faculty profiles.

### Appraisal Data
* Relational tables for academic activities: `courses_taught`, `new_courses`, `research_publications`, `research_grants`, `patents`, `technology_transfer`, `paper_reviews`, `conference_sessions`, `keynotes_talks`, `awards_honours`, `consultancy`, `teaching_innovation`, and `institutional_contributions`.

---

## 🔧 Setup & Running Locally

### Prerequisites
* **Node.js** (v18+)
* **MySQL** (v8.x)
* **npm** (v9+)

### 1. Installation
Clone the repository and install dependencies for both the frontend and backend:
```bash
# Clone
git clone https://github.com/Kansal26/Faculty_appraisal_system.git
cd Faculty_appraisal_system

# Install Frontend
npm install

# Install Backend
cd backend
npm install
```

### 2. Environment Variables
Create a `.env` file in the `backend/` directory:
```env
PORT=5001
DB_HOST=localhost
DB_PORT=3306
DB_USER=root
DB_PASSWORD=your_mysql_password
DB_NAME=faculty_appraisal

JWT_SECRET=supersecretkey12345
JWT_EXPIRES_IN=7d

# SMTP Configuration (Optional for Local Development)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your_email@gmail.com
SMTP_PASS=your_app_password
SMTP_FROM=Faculty Appraisal System <your_email@gmail.com>

FRONTEND_URL=http://localhost:5173
```

### 3. Database Initialisation
Create the database and apply the migrations:
```sql
CREATE DATABASE faculty_appraisal;
USE faculty_appraisal;
```
Then run the migration and table seeding commands:
```bash
# Run schema and EAV bootstrap migrations
node backend/database/master_migration.js
node backend/database/run_migration.js
node backend/migrate_form_builder_v2.js
```

### 4. Running the Development Servers

Start both servers to run the application locally:

* **Backend Dev Server** (Port `5001`):
  ```bash
  cd backend
  npm run dev
  ```
* **Frontend Dev Server** (Port `5173`):
  ```bash
  cd ..
  npm run dev
  ```

---

## 🔑 Demo Access Credentials

To test the dashboards locally, you can log in with the following pre-configured credentials:

| Dashboard Role | Email Credentials | Password |
| :--- | :--- | :--- |
| **DOFA Office Admin** | `dofa.office@university.edu` | `password123` |
| **DOFA Reviewer** | `dofa@university.edu` | `password123` |
| **System Admin** | `admin@lnmiit.ac.in` | `password123` |
