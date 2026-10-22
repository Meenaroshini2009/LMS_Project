# LMS Full-Stack Scaffold (Django + React)

A starter scaffold for a Learning Management System with role-based auth
(student / instructor / admin), courses with modules & lessons, enrollment +
progress tracking, quizzes with auto-grading, and PDF certificate generation.

## Project structure

```
lms-project/
├── backend/          Django REST API
│   ├── config/        settings, root urls
│   ├── accounts/       custom User model (roles), JWT auth
│   ├── courses/        Course, Module, Lesson
│   ├── enrollments/     Enrollment, LessonProgress
│   ├── quizzes/         Quiz, Question, Choice, QuizAttempt (auto-graded)
│   └── certificates/    PDF certificate generation (reportlab)
└── frontend/         React (Vite) SPA
    └── src/
        ├── api/         axios instance with JWT refresh
        ├── context/       AuthContext
        ├── components/    Navbar, ProtectedRoute
        └── pages/         Login, Signup, Dashboard, CourseCatalog
```

## Backend setup

```bash
cd backend
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt

cp .env.example .env            # then edit DB credentials, SECRET_KEY, etc.

# Create the Postgres database first (or swap the DB engine in settings.py
# for sqlite3 if you just want to try things out locally):
#   createdb lms_db

python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

API will be live at `http://localhost:8000/api/`. Admin panel at `/admin/`.

### Key endpoints

| Endpoint | Method | Description |
|---|---|---|
| `/api/auth/register/` | POST | Create account (`role`: student/instructor) |
| `/api/auth/login/` | POST | Get JWT access + refresh tokens |
| `/api/auth/refresh/` | POST | Refresh access token |
| `/api/auth/me/` | GET/PATCH | Current user profile |
| `/api/courses/courses/` | GET/POST | List/create courses |
| `/api/courses/courses/<id>/` | GET | Course detail with modules/lessons |
| `/api/courses/modules/?course=<id>` | GET/POST | Modules for a course |
| `/api/courses/lessons/?module=<id>` | GET/POST | Lessons for a module |
| `/api/enrollments/` | GET/POST | My enrollments / enroll in a course |
| `/api/enrollments/<id>/complete-lesson/` | POST | Mark a lesson done |
| `/api/quizzes/` | GET/POST | Quizzes |
| `/api/quizzes/attempts/` | POST | Submit answers, auto-graded |
| `/api/certificates/issue/` | POST | Generate certificate PDF once course is 100% complete |

## Frontend setup

```bash
cd frontend
npm install
npm run dev
```

Runs at `http://localhost:5173`. Set `VITE_API_BASE_URL` in a `.env` file
if your backend isn't on `http://localhost:8000/api`.

## What's stubbed vs. what's real

**Fully wired:** registration/login with JWT, role field, course catalog +
enroll, dashboard progress bars, quiz auto-grading logic, certificate PDF
generation.

**Left for you to build out:** instructor-facing UI for creating
courses/modules/lessons/quizzes (the API endpoints already support it —
`POST /api/courses/courses/` etc. — just needs forms), a lesson-player page
that calls `complete-lesson`, and an admin analytics view. The Django admin
panel is fully functional in the meantime for managing content.

## Suggested next steps
1. Get the backend running against SQLite first (fastest way to see it work) — swap the `DATABASES` engine in `config/settings.py` to `django.db.backends.sqlite3` and drop the other DB_* keys.
2. Log in to `/admin/`, create a course with a module and lesson to have real data to hit the API with.
3. Build the instructor "create course" form in React — it's the biggest missing piece.
4. Add a lesson-player page that calls the `complete-lesson` endpoint as the student progresses.
