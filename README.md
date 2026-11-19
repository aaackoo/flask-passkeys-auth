# Flask Passkeys Auth app by Adam Šupej

My app is a Flask-based web application that implements **passkeys authentication** using **FIDO2 WebAuthn**. It demonstrates registration and authentication flow using passkeys, storing user credentials in a PostgreSQL database. After signing in with a passkey, each user can store a private message that only they can see.

---

## Table of Contents
- [Deployment](#deployment)
- [Running locally](#running-locally)
- [Routes](#routes)
- [Usage](#usage)

---
## Deployment
The app used to run on my own domain (supej.me), on an ARM Hetzner cloud server with its own PostgreSQL database. That deployment is no longer live.

## Running locally
Tested with Python 3.13.9 and the versions pinned in `requirements.txt`.

1. Create a virtual environment with Python 3.13 and run `pip install -r requirements.txt`.
2. Copy `.env.example` to `.env` and fill in the environment variables: `APP_SECRET_KEY`, `DB_URI`, `DB_HOST`, `DB_NAME`,
   `DB_USER`, `DB_PASSWORD`, `RP_ID` (use `localhost` for local runs) and `RP_NAME`.
   `DB_URI` must use the `postgresql+psycopg2://` scheme, for example `postgresql+psycopg2://passkeys:change-me@localhost:5432/passkeys`.
3. Create a PostgreSQL database and run `python init_db.py`.
4. Start the app with `python app.py` and open `http://localhost:5000`, not `http://127.0.0.1:5000`. WebAuthn does not accept an IP address as the RP ID, and the fido2 library only allows plain `http` on `localhost`.

Passkeys are bound to a domain (the Relying Party ID), so `RP_ID` must match the host you open the app on. Browsers allow WebAuthn on `localhost` without HTTPS; any other domain needs HTTPS.

On macOS, AirPlay Receiver also listens on port 5000 and answers `http://localhost:5000` with a 403. Turn it off in System Settings > General > AirDrop & Handoff before starting the app.

## Routes

### 1. HTML Views
| Route           | Method | Description         |
|----------------|--------|---------------------|
| `/`            | GET    | Renders `index.html` |
| `/register`    | GET    | Renders `register.html` |
| `/login`       | GET    | Renders `login.html` |
| `/secret`      | GET    | Renders `secret.html` |
| `/logout`      | GET    | Logs out the user |

### 2. API Endpoints
| Route                  | Method | Description |
|------------------------|--------|-------------|
| `/register/begin`      | POST   | Initiates user registration |
| `/register/complete`   | POST   | Completes registration with passkey |
| `/login/begin`         | POST   | Initiates authentication |
| `/login/complete`      | POST   | Completes authentication |
| `/current_user_string` | GET    | Returns stored string for logged-in user |
| `/check_login`         | GET    | Checks if user is logged in |

---

## Usage
Run the app locally (see [Running locally](#running-locally)) and open http://localhost:5000.

### Registering a User
1. Navigate to `/register`
2. Enter a **username** and **secret** to store.
3. Follow the **passkey registration flow**.

### Logging In
1. Navigate to `/login`
2. Enter your **username**.
3. Authenticate using your passkey.
