# Faculty Appraisal System

A full-stack web application built for the LNM Institute of Information Technology (LNMIIT), Jaipur, to digitize and manage the annual faculty performance appraisal process. This system replaces paper forms and spreadsheets with a centralized, automated workflow.

## Project Features

* **Role-Based Dashboards**: Tailored interfaces for Faculty, Head of Department (HOD), Dean of Faculty Affairs (DOFA), and DOFA Office Admin.
* **Dynamic Form Builder**: An administrative module that allows creation of custom appraisal sections and input fields without changing database tables or writing new SQL.
* **Autosave Engine**: Saves form progress in the background using a debounced timer.
* **PDF Report Generation**: Automatically generates printable A4 PDF summaries of appraisal submissions.
* **Rubric-Based Scoring**: Automated marks calculation based on configured appraisal rubrics.
* **Bulk Export**: Export of all appraisal data to Excel format (`.xlsx`) for review.

---

## Technical Architecture & Implementation

### Database Design
The system uses MySQL with an InnoDB storage engine. To support custom form sections, we implemented an **Entity-Attribute-Value (EAV)** pattern across `dynamic_sections`, `dynamic_fields`, and `dynamic_responses` tables. This allows admins to define new structures while storing response values in a flexible JSON format.

### Query Performance Optimization
The original implementation had an N+1 query issue when retrieving publications along with their sub-entities (authors, editors). We resolved this by refactoring the queries to use SQL `JOIN`s and implementing local server caching (`node-cache`), reducing overall query count and latency.

### PDF Rendering
PDF reports are generated on the server using headless Chrome via **Puppeteer**. This approach ensures layout consistency compared to client-side library generators.

---

## Technology Stack

* **Frontend**: React (v18 SPA), Vite, React Router DOM, Vanilla CSS.
* **Backend**: Node.js, Express.js.
* **Database**: MySQL.
* **Libraries**: JWT for authentication, Bcryptjs for password hashing, Multer for file uploads, Nodemailer for emails, and node-cron for reminder jobs.

---

## Getting Started

### Prerequisites
* Node.js (v18 or higher)
* MySQL (v8.x)
* npm

### Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/Kansal26/Faculty_appraisal_system.git
   cd Faculty_appraisal_system
   ```

2. Install dependencies:
   ```bash
   # Install frontend dependencies
   npm install

   # Install backend dependencies
   cd backend
   npm install
   ```

3. Set up the environment variables:
   Create a `.env` file in the `backend/` directory:
   ```env
   PORT=5001
   DB_HOST=localhost
   DB_PORT=3306
   DB_USER=root
   DB_PASSWORD=your_mysql_password
   DB_NAME=faculty_appraisal

   JWT_SECRET=your_jwt_secret_key
   JWT_EXPIRES_IN=7d

   SMTP_HOST=smtp.gmail.com
   SMTP_PORT=587
   SMTP_USER=your_email@gmail.com
   SMTP_PASS=your_app_password
   SMTP_FROM=Faculty Appraisal System <your_email@gmail.com>

   FRONTEND_URL=http://localhost:5173
   ```

4. Initialize the Database:
   Create a database named `faculty_appraisal` and run the migration scripts to initialize tables:
   ```bash
   node database/master_migration.js
   node database/run_migration.js
   node migrate_form_builder_v2.js
   ```

5. Run the servers:
   * **Backend**:
     ```bash
     cd backend
     npm run dev
     ```
   * **Frontend**:
     ```bash
     cd ..
     npm run dev
     ```

---

## Test Accounts

The following credentials can be used locally to test different roles:

| Role | Email | Password |
| :--- | :--- | :--- |
| **DOFA Office Admin** | `dofa.office@university.edu` | `password123` |
| **DOFA Reviewer** | `dofa@university.edu` | `password123` |
| **System Admin** | `admin@lnmiit.ac.in` | `password123` |
