# TARS Website

A full-stack web application for the TARS club, built with a Django REST API backend and a React (Vite) frontend. It powers the public website (projects, events, teams, resources) and provides an admin panel for managing content.

## Project Structure

```text
TARS-projects/
  backend/        # Django + DRF API and admin
  frontend/       # React + Vite single-page application
```

### Backend (Django)

- **Framework**: Django 5.x
- **API**: Django REST Framework
- **Auth**: JWT (SimpleJWT)
- **Database**: SQLite (development)
- **Apps**:
  - `accounts` – user profiles and JWT auth endpoints
  - `teams` – teams and team members (with profile photos)
  - `projects` – projects/accomplishments
  - `resources` – learning resources (public/member visibility)
  - `events` – events shown on the website

### Frontend (React + Vite)

- **Framework**: React
- **Bundler/Dev server**: Vite
- **Styling**: Tailwind CSS + custom CSS
- **Structure**:
  - `src/components` – reusable UI components
  - `src/pages` – top-level pages (Home, About, Projects, Events, Teams, etc.)
  - `src/data` – static fallback data (used when API is unavailable)
  - `src/utils/api.js` – helper for calling the Django API

## Getting Started

### Prerequisites

- Python 3.10+
- Node.js 18+ and npm (or yarn/pnpm)
- Git

### 1. Clone the repository

```bash
git clone <YOUR_REPO_URL>
cd TARS-projects
```

### 2. Backend setup (Django API)

From the `backend` folder:

```bash
cd backend

# (Optional but recommended) create and activate a virtualenv
python -m venv venv
# Windows PowerShell
venv\\Scripts\\activate

# Install Python dependencies
pip install -r requirements.txt

# Apply database migrations
python manage.py migrate

# Create a superuser for Django admin
python manage.py createsuperuser

# Run the development server
python manage.py runserver
```

The backend will be available at:

- `http://127.0.0.1:8000/` – simple backend home page
- `http://127.0.0.1:8000/admin/` – Django admin interface
- `http://127.0.0.1:8000/api/` – API endpoints (see below)

#### Backend configuration

Configuration is managed in `backend/config/settings.py`. Before production:

- Set a strong `SECRET_KEY` (do *not* commit it).
- Set `DEBUG = False` for production.
- Configure `ALLOWED_HOSTS` with your domain/host.
- Configure CORS origins (for the frontend URL) using `django-cors-headers`.
- Configure proper static and media file hosting.

You can store secrets in environment variables or a local `.env` (which should be ignored by Git).

### 3. Frontend setup (React + Vite)

From the `frontend` folder:

```bash
cd frontend

# Install Node dependencies
npm install

# (Optional) configure API base URL for production/dev
# Create .env.local and add for example:
# VITE_API_BASE_URL=http://127.0.0.1:8000

# Start the Vite dev server
npm run dev
```

The frontend dev server will usually run at:

- `http://localhost:5173/`

By default, the frontend uses `VITE_API_BASE_URL` (if set) or falls back to `http://127.0.0.1:8000` to reach the Django API.

## Main Features

- **Projects/Accomplishments**
  - Managed via Django admin (`projects` app).
  - Public API provides project list and detail.
  - Frontend consumes `/api/projects/` to display accomplishments.

- **Teams & Members**
  - Teams and team members (with roles and optional photos) managed in admin (`teams` app).
  - Public API at `/api/teams/` returns teams and nested members.
  - Frontend shows both curated static content and dynamic backend data.

- **Events**
  - `events` app exposes `/api/events/`.
  - Admin allows adding/updating events.
  - Frontend events section pulls from the API with a static fallback.

- **Resources**
  - `resources` app stores public and member-only resources.
  - API at `/api/resources/` (with visibility handling on the backend).

- **Accounts & Auth**
  - `accounts` app provides user profiles and JWT auth endpoints.
  - Endpoints (paths may differ depending on final URLs):
    - Register: `/api/accounts/register/`
    - Obtain token: `/api/accounts/token/`
    - Refresh token: `/api/accounts/token/refresh/`
    - Current user profile: `/api/accounts/me/`

## API Overview (examples)

Base URL (development): `http://127.0.0.1:8000`

- `GET /api/projects/` – list projects
- `GET /api/projects/<id>/` – project detail
- `GET /api/teams/` – list teams and members
- `GET /api/teams/<id>/` – team detail
- `GET /api/events/` – list events
- `GET /api/events/<id>/` – event detail
- `GET /api/resources/` – list resources (public; members-only may require auth)

Auth endpoints (SimpleJWT):

- `POST /api/accounts/token/` – obtain access & refresh tokens
- `POST /api/accounts/token/refresh/` – refresh access token

## Development Notes

- The frontend includes static data modules so the site still shows content if the API is down; when the API is reachable, dynamic data is preferred.
- CORS is configured on the backend to allow requests from the Vite dev server (`http://localhost:5173` and `http://127.0.0.1:5173`).
- Uploaded team member photos are served from `MEDIA_URL` in development. For production, configure your web server or storage (e.g., S3, Azure Blob) appropriately.

## Running Tests

You can add and run tests from the `backend` and `frontend` projects, for example:

```bash
# Backend (Django)
cd backend
python manage.py test

# Frontend (React)
cd frontend
npm test   # if tests are configured
```

## Deployment

Deployment steps depend on your hosting provider, but at a high level:

- Build the frontend (`npm run build`) and serve the static files via a web server or CDN.
- Deploy the Django backend with a production-ready WSGI/ASGI server (e.g., Gunicorn + Nginx, or a cloud platform).
- Configure environment variables (secret key, database, allowed hosts, CORS, JWT settings).
- Set up HTTPS and domain names.

## Contributing

1. Fork the repository.
2. Create a feature branch.
3. Commit your changes with clear messages.
4. Open a pull request.

## License

Add your chosen license here (for example, MIT). Make sure to include a LICENSE file if you intend to open source the project.
