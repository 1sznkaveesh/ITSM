# Setup and Verification Guide

Follow these instructions on a new system to install, run, and verify the ITSM application.

## 1. Prerequisites

Install the following software before setting up the project:

| Software | Requirement |
|---|---|
| Java JDK | 21 or later (Java 21 recommended) |
| Apache Maven | 3.10.0 or compatible version |
| Node.js | LTS version |
| npm | Installed with Node.js |
| PostgreSQL | 16 or compatible version |
| Git | Latest stable version |
| Code Editor | VS Code recommended |

### Verify installations

Open CMD/Terminal and execute:

```bash
java -version
javac -version
mvn -version
node -v
npm -v
psql --version
git --version
```

Make sure all commands execute successfully.

## 2. Clone the Repository

Clone the GitHub repository:

```bash
git clone https://github.com/1sznkaveesh/ITSM.git
```

Navigate into the project:

```bash
cd ITSM
```

Check that the following directories are present:

```text
ITSM/
├── Backend/
└── Frontend/
```

## 3. Database Setup (PostgreSQL)

### Step 1: Start PostgreSQL

Make sure the PostgreSQL server is running.

### Step 2: Create the database

Open PostgreSQL using:

```bash
psql -U postgres
```

Create the database:

```sql
CREATE DATABASE itsm_db;
```

Exit PostgreSQL:

```sql
\q
```

If the database already exists, skip the creation step.

### Step 3: Configure database credentials

Open the backend configuration file:

```text
Backend/src/main/resources/application.properties
```

Check the following settings:

- Database URL
- Database name
- PostgreSQL username
- PostgreSQL password
- Server port

Ensure the credentials match your local PostgreSQL installation.

**Important:** Never commit actual database passwords, API keys, or other sensitive credentials to GitHub. Use environment variables or a local configuration file excluded from Git.

## 4. Backend Setup

Open a terminal inside the Backend directory:

```bash
cd Backend
```

### Step 1: Compile the backend

```bash
mvn clean compile
```

Expected result:

```text
BUILD SUCCESS
```

### Step 2: Run backend tests

```bash
mvn test
```

Expected result:

```text
Tests run: <number>
Failures: 0
Errors: 0
Skipped: 0

BUILD SUCCESS
```

All automated tests should pass.

### Step 3: Start the backend

```bash
mvn spring-boot:run
```

Wait for the Spring Boot application to start.

Expected log message:

```text
Started IItsmApplication
```

Keep this terminal open while testing the application.

## 5. Frontend Setup

Open a separate terminal inside the Frontend directory:

```bash
cd Frontend
```

### Step 1: Install dependencies

```bash
npm ci
```

This installs the dependencies specified in the package-lock file.

### Step 2: Build the frontend

```bash
npm run build
```

Expected result:

```text
✓ built successfully
```

The build should complete without errors.

### Step 3: Start the frontend

```bash
npm run dev
```

Open the local URL displayed in the terminal. Usually:

```text
http://localhost:5173
```

The login page should load successfully.

## 6. Functional Testing Checklist

After starting both the backend and frontend, test the following features.

### A. Application and Login

- [ ] Frontend loads without errors.
- [ ] Login page is displayed correctly.
- [ ] Valid user credentials allow login.
- [ ] Invalid credentials are rejected.
- [ ] User is redirected to the appropriate dashboard.
- [ ] Logout works correctly.

### B. Employee Features

- [ ] Employee can view the dashboard.
- [ ] Employee can create a new incident.
- [ ] Incident details are saved correctly.
- [ ] Employee can view their incidents.
- [ ] Employee can view incident details.
- [ ] Employee can add comments or notes, where permitted.
- [ ] Incident status is displayed correctly.

### C. Technician Features

- [ ] Technician can log in.
- [ ] Technician can view assigned incidents.
- [ ] Technician can open incident details.
- [ ] Technician can update incident status.
- [ ] Technician can add work notes.
- [ ] Technician can resolve incidents.
- [ ] Technician workload is displayed correctly.
- [ ] Technician can use the delegation functionality, where permitted.

### D. Admin Features

- [ ] Admin can log in.
- [ ] Admin dashboard loads correctly.
- [ ] Admin can view all incidents.
- [ ] Admin can view technicians and their workloads.
- [ ] Admin can assign incidents.
- [ ] Admin can reassign incidents.
- [ ] Admin can manage SLA policies.
- [ ] Admin can view notifications.
- [ ] Admin can access audit logs and settings, where implemented.

### E. Delegation and Workload Management

- [ ] Eligible users can delegate incidents.
- [ ] Delegated incidents appear under the appropriate technician.
- [ ] Delegation information is displayed correctly.
- [ ] Workload limits are enforced.
- [ ] Unauthorized delegation attempts are rejected.
- [ ] Changes are reflected in the relevant dashboards.

### F. SLA and Notifications

- [ ] SLA policies are loaded correctly.
- [ ] SLA information is associated with incidents.
- [ ] SLA status is displayed correctly.
- [ ] SLA-related notifications are generated when applicable.
- [ ] Incident-related notifications work correctly.

### G. Data Persistence

- [ ] Newly created incidents remain after refreshing the page.
- [ ] Updated incident statuses remain saved.
- [ ] Assignment and delegation changes persist.
- [ ] Notes and comments remain available.
- [ ] Data remains available after logging out and logging back in.

## 7. API and Error Verification

Use the browser developer tools to verify frontend-backend communication.

1. Open the application in Chrome.
2. Press `F12`.
3. Open the **Network** tab.
4. Select **Fetch/XHR**.
5. Perform actions such as login, incident creation, and assignment.
6. Verify that API requests reach the backend.
7. Check HTTP response codes and response data.

Also inspect the Console tab for JavaScript errors.

Check the backend terminal for:

- Database connection failures.
- HTTP 4xx and 5xx errors.
- Application exceptions.
- Unexpected server crashes.

## 8. Final Verification Checklist

Before declaring the repository ready to use on a new system, confirm:

- [ ] All prerequisites are installed.
- [ ] Repository clones successfully.
- [ ] PostgreSQL starts successfully.
- [ ] Database is created and configured.
- [ ] Backend compiles successfully.
- [ ] All backend tests pass.
- [ ] Backend starts without errors.
- [ ] Frontend dependencies install successfully.
- [ ] Frontend production build succeeds.
- [ ] Frontend starts successfully.
- [ ] Login and role-based access work.
- [ ] Incident management works.
- [ ] Assignment and delegation work.
- [ ] SLA and notifications work.
- [ ] Database persistence works.
- [ ] No critical browser console or backend errors remain.

## 9. Troubleshooting

| Problem | Possible Solution |
|---|---|
| `java is not recognized` | Install JDK 21 and configure PATH. |
| `mvn is not recognized` | Install Maven and configure PATH. |
| `npm is not recognized` | Install Node.js and restart the terminal. |
| `vite is not recognized` | Run `npm ci` inside Frontend. |
| Database connection refused | Ensure PostgreSQL is running. |
| Database does not exist | Create `itsm_db`. |
| Database authentication failed | Verify PostgreSQL credentials. |
| Port already in use | Identify and stop the process using the port, or configure another available port. |
| API requests fail | Verify backend startup and frontend API configuration. |
| Frontend build fails | Check Node.js version, dependencies, and build errors. |
| Backend tests fail | Inspect the Maven test output and failing test reports. |

## 10. Important Notes

- Start PostgreSQL before starting the backend.
- Start the backend before testing frontend API functionality.
- Keep the backend and frontend running in separate terminals.
- Do not commit passwords, private keys, or local environment files.
- Use test accounts and test data when verifying application functionality.
- A successful build does not guarantee that every application feature works. Complete the functional testing checklist as well.

**The repository is considered successfully verified on a new system only after the backend tests, frontend build, application startup, and functional checks have been completed.**

we have to add this in readme
