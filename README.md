# Notes App (FastAPI + PostgreSQL)

A small full-stack notes app with user accounts. Each user signs up, logs in, and manages a private list of notes: add, edit, tick as done, delete.

**Live demo:** https://YOUR-SERVICE.onrender.com

> The demo runs on a free hosting plan. If it has been idle, the first visit takes up to a minute while the server wakes up.

## Features

- Sign up and log in with email and password
- Passwords stored as Argon2 hashes, never as plain text
- JWT access tokens (1 hour), sent as a Bearer header
- Every note belongs to one user; users cannot see or change each other's notes
- Add, edit, mark as done, and delete notes
- Checkbox updates appear instantly and are confirmed by the server in the background
- Clear messages for slow or unreachable servers (30 second request limit)
- Buttons are disabled while a request is running, to prevent double submits
- Responsive layout for phones and desktops

## Tech stack

| Layer | Technology |
|---|---|
| Backend | Python, FastAPI, Uvicorn |
| Database | PostgreSQL (psycopg 3) |
| Auth | PyJWT, pwdlib (Argon2) |
| Frontend | HTML, CSS, vanilla JavaScript (single page, no framework) |
| Hosting | Render (web service + managed PostgreSQL) |

## API endpoints

All `/notes` routes and `/me` require the header `Authorization: Bearer <token>`.

| Method | Path | Purpose | Success |
|---|---|---|---|
| POST | `/users` | Create an account | 201 |
| POST | `/login` | Log in, returns an access token | 200 |
| GET | `/me` | The logged-in user's id and email | 200 |
| GET | `/notes` | List the user's notes, newest first | 200 |
| POST | `/notes` | Create a note | 201 |
| GET | `/notes/{id}` | Read one note | 200 |
| PUT | `/notes/{id}` | Update a note | 200 |
| DELETE | `/notes/{id}` | Delete a note | 204 |

Error responses: `401` invalid or expired token, or wrong login; `404` note not found (or owned by another user); `409` email already registered; `422` invalid input (for example a password outside 8 to 50 characters).

Interactive API documentation is available at `/docs`.

## Run it locally

Requirements: Python 3.10 or newer, and a PostgreSQL database.

```powershell
git clone https://github.com/aaronaasif/notes-app-fastapi-postgres.git
cd notes-app-fastapi-postgres
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

Set the environment variables (PowerShell):

```powershell
$env:DATABASE_URL = "postgresql://USER:PASSWORD@HOST/DBNAME"
$env:NOTES_SECRET = "a-long-random-string"
```

Start the server:

```powershell
uvicorn notes_app:app --reload
```

Open http://127.0.0.1:8000/. The tables are created automatically on first start.

### Environment variables

| Name | Required | Purpose |
|---|---|---|
| `DATABASE_URL` | Yes | PostgreSQL connection string |
| `NOTES_SECRET` | Yes in production | Key used to sign login tokens. A development default is used locally; the app refuses to start on Render without it. |

Never commit real values. Keep them in your shell or in a file outside the repository.

## Deploy on Render

1. Create a PostgreSQL database on Render.
2. Create a Web Service from this repository.
   - Build command: `pip install -r requirements.txt`
   - Start command: `uvicorn notes_app:app --host 0.0.0.0 --port $PORT`
3. Add the environment variables `DATABASE_URL` (the database's **Internal** URL) and `NOTES_SECRET`.
4. Deploy. Each push to `main` redeploys the service.

## Screenshots

![Login screen iPhone](screenshots/login_iphone.jpg)
![Notes page iPhone](screenshots/notes_iphone.jpg)
![Login screen Android](screenshots/login_android.jpg)
![Notes page Android](screenshots/notes_android.jpg)

## Roadmap

- File attachments on notes
- Export notes to PDF and Excel
- Email notifications
- Automated tests

## Author

Built by [aaronaasif](https://github.com/aaronaasif).