# HAGMS (Health & Gym Management System)

This repository contains a Flask-based fitness application with workout, nutrition, progress tracking, and a client-side pose-detection feature that records exercise sessions to the backend.

Table of Contents
- Project overview
- Features
- Prerequisites
- Setup
- Database migrations
- Running the app
- Key endpoints
- Pose detection usage & troubleshooting
- Project structure
- Contributing

Project overview
----------------
HAGMS is a lightweight fitness web app built with Flask. It includes user authentication, workout plans, nutrition and progress logs, and a client-side pose detection UI (using MediaPipe) that analyzes form, counts reps, and saves pose-session summaries to the backend.

Features
--------
- User registration and login (Flask-Login)
- Workout plans and exercises
- Nutrition logs with export (CSV)
- Progress logs with charts
- Pose detection UI that:
  - Runs MediaPipe pose model in the browser
  - Calculates form scores and rep counts
  - Sends session summaries to `/api/save_pose_session`

Prerequisites
-------------
- Linux / macOS / Windows
- Python 3.10+ (3.8+ may work)
- Git
- A browser with camera access (Chrome/Firefox recommended)

Setup
-----
1. Clone the repo and change directory:

```bash
git clone <repo-url>
cd HAGMS
```

2. Create and activate a virtual environment:

```bash
python -m venv .venv
source .venv/bin/activate  # Linux/macOS
.venv\Scripts\activate     # Windows (PowerShell/CMD)
```

3. Install Python dependencies:

```bash
pip install -r requirements.txt
```

4. Environment variables (optional but recommended):

- `SECRET_KEY` — Flask secret key
- `DATABASE_URL` — SQLAlchemy DB URL (defaults to `sqlite:///fitness_app.db`)

Example (Linux/macOS):

```bash
export SECRET_KEY='your_secret'
export DATABASE_URL='sqlite:///fitness_app.db'
```

Database migrations
-------------------
This project uses Flask-Migrate (Alembic). If the `migrations/` folder already exists, you can apply migrations:

```bash
export FLASK_APP=app.py
flask db upgrade
```

If you need to create migrations after model changes:

```bash
flask db migrate -m "describe changes"
flask db upgrade
```

Running the app
---------------
Development run:

```bash
export FLASK_APP=app.py
export FLASK_ENV=development
flask run
# or
python app.py
```

Production run (example with gunicorn):

```bash
gunicorn --bind 0.0.0.0:8000 app:app
```

Key endpoints
-------------
- `/` — Login page
- `/register` — User registration
- `/dashboard` — User dashboard
- `/workout_plans`, `/nutrition_logs`, `/progress_logs` — Management pages
- `/pose-detection` — Pose detection UI
- `/api/save_pose_session` — POST endpoint used by the browser UI to save session summaries (exercise, reps, avg_form_score, duration_seconds)

Pose detection usage & troubleshooting
-------------------------------------
1. Open the app in a browser and log in.
2. Visit `/pose-detection`.
3. Select an exercise, click **Start**, perform reps, then click **Stop**.
4. The page will send a JSON POST to `/api/save_pose_session`. Check the browser console (F12 → Console) for logs showing "Sending workout data" and the server response.

If rep counting or saving fails:
- Verify camera permission in the browser.
- Open DevTools and check console for errors and logs.
- Confirm you are authenticated (requests to `/api/save_pose_session` are protected by login).
- Server logs (terminal running Flask) will show incoming requests and any exceptions.

Common fixes
- Rep count not increasing: ensure the selected exercise matches the performed movement and hold a clear camera framing of the body. The frontend counts reps via angle thresholds and a state flag; small camera or landmark jitter can affect counts.
- API returns 401: session may have expired or cookie not present. Re-login and try again.

Project structure (high level)
------------------------------
- `app.py` — Flask app and routes
- `models.py` — SQLAlchemy models (User, WorkoutPlan, Exercise, NutritionLog, Progress, PoseSession)
- `db.py` — DB initialization helper
- `templates/` — HTML/Jinja templates (`pose_detection.html` contains MediaPipe integration)
- `static/mediapipe/` — MediaPipe JS and WASM assets
- `migrations/` — Alembic migration scripts
- `requirements.txt` — Python dependencies

Notes for developers
--------------------
- Pose detection runs fully in the browser using MediaPipe. The frontend computes angles and rep counts and posts the summary to `/api/save_pose_session`.
- The backend endpoint in `app.py` stores sessions into the `PoseSession` model in `models.py`.
- If you modify models, remember to create a new migration and run `flask db upgrade`.

Contributing
------------
1. Fork the repository
2. Create a topic branch
3. Make changes and run tests locally
4. Submit a pull request describing the changes

License
-------
Add your license info here (if applicable).

Contact
-------
For questions, open an issue in the repository or contact the maintainers.
