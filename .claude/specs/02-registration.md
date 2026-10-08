# Spec: Registration

## Overview

Implement user registration for Spendly. The `/register` route and `register.html` template already exist as a GET-only placeholder with a form that posts to `/register`. This step adds the `POST /register` handler that validates input, hashes the password, inserts a new user into the `users` table, starts a session, and redirects to the (future) profile/dashboard page. This builds directly on the database layer from Step 01 and is required before login, logout, or any expense feature can work, since all of those depend on an authenticated `user_id`.

## Depends on

- Step 01 — Database Setup (`users` table, `get_db()`, `generate_password_hash`)

## Routes

- `POST /register` — accepts name/email/password form data, validates, creates user, starts session, redirects to `/profile` — public
- `GET /register` — already exists, unchanged — public

No other new routes.

## Database changes

No database changes. `users` table from Step 01 already has the required columns (`id`, `name`, `email`, `password_hash`, `created_at`).

## Templates

- **Create:** None
- **Modify:** `templates/register.html` — add rendering for field-level validation errors (e.g. "Email already registered") via the existing `{% if error %}` block in `auth-card`; no structural changes needed since the form already posts to `/register` with `name`, `email`, `password` fields

## Files to change

- `app.py` — add `POST` method to the `/register` route, implement registration logic (validate fields, check duplicate email, hash password, insert user, set session, redirect)

## Files to create

None

## New dependencies

No new dependencies. Flask's built-in `session` (via `app.secret_key`) is used for login state; `werkzeug.security.generate_password_hash` is already in use from Step 01.

Note: `app.py` currently has no `app.secret_key` set. This must be added for `session` to work — use a value from an environment variable or a hardcoded dev value, consistent with how the rest of the app currently has no `.env` usage.

## Rules for implementation

- No SQLAlchemy or ORMs
- Parameterised queries only
- Passwords hashed with werkzeug
- Use CSS variables — never hardcode hex values
- All templates extend `base.html`
- Validate: name non-empty, email format, password minimum 8 characters (matches the placeholder text in `register.html`)
- Check for duplicate email before inserting; show "Email already registered" via the existing `error` template variable rather than raising unhandled `IntegrityError`
- On success, store `user_id` in `session` and redirect (do not render a page directly from the POST handler)

## Definition of done

- [ ] Submitting the registration form with valid, unique details creates a row in `users` with a hashed password (not plaintext)
- [ ] Submitting with an email that already exists re-renders `register.html` with an "Email already registered" error and does not create a duplicate row
- [ ] Submitting with a password under 8 characters re-renders the form with a validation error and does not create a user
- [ ] After successful registration, the browser session contains the new user's `user_id` and the user is redirected away from `/register`
- [ ] Visiting `GET /register` still renders the empty form as before
- [ ] App starts and runs with no errors (`python app.py`)
