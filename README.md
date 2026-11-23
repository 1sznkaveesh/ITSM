# ITSM – IT Service Management System

A full-stack IT Service Management application for reporting, assigning and resolving IT incidents. It is built with object-oriented Java (Spring Boot) on the backend and React (Vite) on the frontend, with PostgreSQL for storage.

## Features

- **Role-based access:** Employee, Technician and Administrator dashboards
- **Incident management:** report, prioritise, assign, acknowledge, work on, resolve and close incidents
- **Automatic priority and SLA:** priority is calculated from impact, urgency and business criticality, and SLA policies are applied to each incident
- **Smart assignment:** incidents are assigned to technicians by skill and current workload
- **Delegation and workload limits:** technicians can delegate incidents, and workload limits are enforced
- **Duplicate detection and suggested fixes:** similar incidents are flagged, and past resolved incidents are suggested as fixes
- **Feedback:** employees rate resolved incidents
- **Notifications and audit trail:** key actions generate notifications and are recorded in the audit log

## Tech Stack

| Layer | Technology |
|---|---|
| Backend | Java 21, Spring Boot, Spring Security, Spring Data JPA, Hibernate, Maven |
| Frontend | React, Vite, Node.js, npm |
| Database | PostgreSQL 16 |
| Tools | Git, GitHub |

## Project Structure

```text
ITSM/
├── Backend/     Spring Boot application (port 8080)
└── Frontend/    React + Vite application (port 5173)
```

## Prerequisites

| Software | Version |
|---|---|
| Java JDK | 21 or later |
| Apache Maven | 3.9 or later |
| Node.js | LTS (npm included) |
| PostgreSQL | 16 or compatible |
| Git | Latest stable |

Verify the installations:

```bash
java -version
mvn -version
node -v
npm -v
psql --version
git --version
```

## Quick Start

### 1. Clone the repository

```bash
git clone https://github.com/1sznkaveesh/ITSM.git
cd ITSM
```

### 2. Set up the database

Make sure PostgreSQL is running, then create the database and give the `postgres` user a password.

**Linux (peer authentication is the default)**

```bash
cd /tmp
sudo -u postgres psql -c "CREATE DATABASE itsm_db;"
sudo -u postgres psql -c "ALTER USER postgres PASSWORD 'postgres123';"
```

**Windows / macOS**

```bash
psql -U postgres -c "CREATE DATABASE itsm_db;"
```

Check that password login works (this is how the backend connects):

```bash
psql -U postgres -h localhost -d itsm_db -c "SELECT 1;"
```

> Use your own password instead of `postgres123`. Never commit real passwords to GitHub.

### 3. Start the backend

Set the database connection through environment variables, then run the app.

**Linux / macOS**

```bash
cd Backend
export DB_URL=jdbc:postgresql://localhost:5432/itsm_db
export DB_USERNAME=postgres
export DB_PASSWORD=postgres123
mvn spring-boot:run
```

**Windows (PowerShell)**

```powershell
cd Backend
$env:DB_URL="jdbc:postgresql://localhost:5432/itsm_db"
$env:DB_USERNAME="postgres"
$env:DB_PASSWORD="postgres123"
mvn spring-boot:run
```

**Windows (CMD)**

```cmd
cd Backend
set DB_URL=jdbc:postgresql://localhost:5432/itsm_db
set DB_USERNAME=postgres
set DB_PASSWORD=postgres123
mvn spring-boot:run
```

Wait for the log line `Started IItsmApplication`. Tables are created automatically on first start. Keep this terminal open.

### 4. Start the frontend

In a second terminal:

```bash
cd Frontend
npm ci
npm run dev
```

Open <http://localhost:5173>.

## Sample Data and Logins

On the first start with an **empty database**, the backend loads sample data so the app can be tried immediately:

- 3 technicians, each with their own login
- 6 sample incidents at different stages (new, acknowledged, in progress, resolved, closed, possible duplicate)

The backend log confirms it:

```text
Sample data: added 3 technicians (logins technician, priya, arjun)
Sample data: added 6 incidents
```

| Login | Password | Role |
|---|---|---|
| `technician` | `tech123` | Technician (Ravi Kumar, Hardware) |
| `priya` | `priya123` | Technician (Priya Sharma, Network and Security) |
| `arjun` | `arjun123` | Technician (Arjun Mehta, Software and Database) |

The `employee` and `admin` accounts are created by the application's default-user setup. Change all default passwords before any real deployment.

### Existing data is never touched

Sample data loads **only** when the database has no technicians and no incidents. On a database that already has real data, it does nothing:

- no sample technicians or incidents are added
- the `technician` login is not linked to any existing technician
- the PostgreSQL user `postgres` is only the database connection account. It is not an application login

### Turning sample data off

Recommended for any real database.

```bash
# Linux / macOS
export SAMPLE_DATA=false

# Windows PowerShell
$env:SAMPLE_DATA="false"

# Windows CMD
set SAMPLE_DATA=false
```

Or set `app.sample-data=false` in `application.properties`.

### Starting over with a fresh sample database (deletes all data)

Stop the backend first, then:

```bash
cd /tmp
sudo -u postgres psql -c "DROP DATABASE IF EXISTS itsm_db WITH (FORCE);"
sudo -u postgres psql -c "CREATE DATABASE itsm_db;"
```

Start the backend again. The database must be created again after every drop, because Spring Boot creates tables but not the database itself.

## Running Tests and Builds

```bash
# Backend
cd Backend
mvn clean compile
mvn test

# Frontend
cd Frontend
npm run build
```

`mvn test` should finish with `Failures: 0, Errors: 0` and `BUILD SUCCESS`.

## Verification Checklist

After starting both servers, check:

- [ ] Login page loads; valid logins work and invalid ones are rejected
- [ ] Each role reaches its own dashboard; logout works
- [ ] **Employee:** can report an incident, view their incidents and give feedback on a resolved one
- [ ] **Technician:** sees only assigned incidents; can acknowledge, update status, add work notes and resolve; workload is shown
- [ ] **Admin:** sees all incidents and technicians; can assign, reassign, manage SLA policies and view notifications and audit logs
- [ ] Delegation works and workload limits are enforced
- [ ] SLA status and notifications appear on incidents
- [ ] Data survives a page refresh, a re-login and a backend restart
- [ ] No errors in the browser console (F12) or in the backend terminal

## Troubleshooting

| Problem | Solution |
|---|---|
| `Peer authentication failed for user "postgres"` | On Linux, use `sudo -u postgres psql ...`, or connect with a password: `psql -U postgres -h localhost`. |
| `could not change directory ... Permission denied` | Harmless warning from `sudo -u postgres`. Run `cd /tmp` first to hide it. |
| `database "itsm_db" does not exist` | Create it: `sudo -u postgres psql -c "CREATE DATABASE itsm_db;"`. |
| `database "itsm_db" already exists` | Skip creation, or drop and recreate it if you want a clean start. |
| `password authentication failed` | Check `DB_USERNAME` and `DB_PASSWORD`, and set the password with `ALTER USER postgres PASSWORD '...'`. |
| `Connection refused` | Start PostgreSQL (`sudo systemctl start postgresql` on Linux). |
| `Port 8080 / 5173 already in use` | Stop the process using the port or configure another port. |
| `Sample data: skipped ...` | The database already had technicians or incidents. Recreate it to reload sample data. |
| `Sample data: disabled` | `SAMPLE_DATA` is set to `false`. Unset it. |
| `vite is not recognized` | Run `npm ci` inside `Frontend`. |
| `mvn` or `java` not found | Install the JDK or Maven and add them to PATH. |
| API requests fail in the browser | Confirm the backend is running on port 8080 and check the Network tab (F12). |

## Security Notes

- Never commit database passwords, API keys, private keys or local environment files.
- Use environment variables or an untracked local configuration file for credentials.
- Change all default and sample passwords before any real deployment.
- Use test accounts and test data when verifying functionality.

## Notes

- Start PostgreSQL before the backend, and the backend before the frontend.
- Keep the backend and frontend running in separate terminals.
- A successful build does not guarantee that every feature works, so complete the verification checklist as well.
