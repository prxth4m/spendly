# Spec: Login And Logout

## Overview

Implement session-based login and logout for Spendly. `/login` currently only handles `GET` and renders `login.html`, whose form already posts to `/login`; `/logout` is an unimplemented stub. This step adds the `POST /login` handler (verify email/password against the `users` table, start a session) and a real `/logout` handler (clear the session, redirect to the landing page). This completes the authentication flow started in Step 02 (Registration) and is required before `/profile` and the expense routes can enforce that a user is actually logged in.

## Depends on

- Step 01 — Database Setup (`users` table, `get_db()`)
- Step 02 — Registration (`users` rows with hashed passwords exist to log in against)

## Routes

- `GET /login` — already exists, unchanged — public
- `POST /login` — accepts email/password form data, verifies credentials, starts session, redirects to `/profile` — public
- `GET /logout` — clears the session and redirects to `/` — logged-in (safe no-op if already logged out)

## Database changes

No database changes. `users` table already has `email` and `password_hash`.

## Templates

- **Create:** None
- **Modify:** None — `templates/login.html` already has the `{% if error %}` block and correctly named `email`/`password` fields posting to `/login`; verified by inspection, no structural changes needed.

## Files to change

- `app.py` — add `POST` handling to `/login` (verify credentials, set session), implement `/logout` (clear session, redirect)

## Files to create

None

## New dependencies

No new dependencies. Uses Flask's `session` (secret key already set in `app.py` from Step 02) and `werkzeug.security.check_password_hash` to verify the hashed password.

## Rules for implementation

- No SQLAlchemy or ORMs
- Parameterised queries only
- Passwords hashed with werkzeug (verify with `check_password_hash`, never compare plaintext)
- Use CSS variables — never hardcode hex values
- All templates extend `base.html`
- Reuse `get_user_by_email` from `database/db.py` (added in Step 02) rather than writing new duplicate SQL
- On invalid email or wrong password, show a single generic error ("Invalid email or password") — do not reveal whether the email exists
- `/logout` must work even if no session exists (don't throw an error for an already-logged-out visitor)

## Definition of done

- [ ] Submitting `/login` with a valid registered email and correct password redirects away from `/login` and sets `session["user_id"]` to that user's id
- [ ] Submitting `/login` with a correct email but wrong password re-renders `login.html` with "Invalid email or password" and does not set the session
- [ ] Submitting `/login` with an email that doesn't exist re-renders `login.html` with the same "Invalid email or password" message (no distinct error leaking which part was wrong)
- [ ] Visiting `/logout` after logging in clears the session and redirects to `/`
- [ ] Visiting `/logout` while not logged in does not error, and simply redirects to `/`
- [ ] `GET /login` still renders the empty form as before
- [ ] App starts and runs with no errors (`python app.py`)
