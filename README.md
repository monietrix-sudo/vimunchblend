# VaultX

VaultX is a production-grade password manager built with Django. It features strong encryption, a modern web interface, an automated CI/CD pipeline, and a full observability stack. The project was developed as a final-year project demonstrating real-world backend engineering, DevOps, and security practices.

![Architecture](vaultx_cloud_architecture.png)

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Architecture](#architecture)
- [Security Design](#security-design)
- [Getting Started](#getting-started)
- [Environment Variables](#environment-variables)
- [CI/CD Pipeline](#cicd-pipeline)
- [Monitoring and Observability](#monitoring-and-observability)
- [Project Structure](#project-structure)
- [Roadmap](#roadmap)
- [License](#license)

---

## Overview

VaultX allows users to securely store, generate, retrieve, and manage their passwords through a clean, responsive web interface. All sensitive data is encrypted at rest using per-user derived keys, and the application is deployed with a fully automated CI/CD pipeline plus real-time monitoring.

This project demonstrates the transition from traditional Django MVC development to a production-ready, security-conscious backend engineering workflow.

---

## Features

- Secure password storage using Fernet symmetric encryption
- Per-user key derivation via PBKDF2-HMAC-SHA256
- Built-in password generator using Python's `secrets` module for cryptographically strong passwords
- AJAX-based decryption, allowing passwords to be revealed without a full page reload
- Modern, responsive UI built with Tailwind CSS
- Security hardening, including CSRF protection, secure cookies, rate limiting, and input validation
- Automated CI/CD pipeline — every push to `main` is tested and automatically redeployed
- Live monitoring of application metrics and database activity via Grafana and Prometheus

---

## Tech Stack

| Layer       | Technology                                |
|-------------|--------------------------------------------|
| Backend     | Django, Django Ninja                        |
| Database    | PostgreSQL (Railway Managed)                |
| Encryption  | Fernet (cryptography), PBKDF2-HMAC-SHA256   |
| Frontend    | Tailwind CSS, AJAX                          |
| Hosting     | Railway                                     |
| CI/CD       | GitHub Actions                              |
| Monitoring  | Prometheus, Grafana Cloud                   |
| Tunnelling  | Ngrok                                        |

---

## Architecture

VaultX follows a fully automated cloud deployment pipeline:

1. The developer pushes code to GitHub.
2. GitHub Actions runs the test suite (pytest, lint, coverage).
3. On a successful test run, the pipeline triggers a redeploy on Railway.
4. The deployed VaultX Django application connects to a PostgreSQL database via the ORM.
5. The application exposes a `/metrics` endpoint in Prometheus format.
6. A local Prometheus instance scrapes `/metrics` through an Ngrok tunnel.
7. Grafana Cloud uses the Ngrok URL as a datasource to render live dashboards.
8. Grafana also connects directly to PostgreSQL for database-level panels.

A full visual diagram of this pipeline is included as `vaultx_cloud_architecture.png`.

---

## Security Design

VaultX takes a defense-in-depth approach to protecting user data:

- Encryption at rest — all stored passwords are encrypted using Fernet (AES-128 in CBC mode with HMAC authentication).
- Per-user key derivation — each user's master key is derived using PBKDF2-HMAC-SHA256 with a unique salt, ensuring no two users share a derivation path.
- No plaintext storage — decrypted passwords exist only in memory during an active request and are never logged.
- Strong password generation — the built-in generator uses Python's `secrets` module, which is cryptographically secure, unlike the standard `random` module.
- CSRF and session protections — Django's CSRF middleware, secure cookie flags, and session expiry are enforced.
- Rate limiting — sensitive endpoints, such as login and decrypt, are throttled to mitigate brute-force attempts.

---

## Getting Started

### Prerequisites

- Python 3.11+
- PostgreSQL
- Node.js (for the Tailwind build, if applicable)

### Installation

```bash
# Clone the repository
git clone https://github.com/<your-username>/vaultx.git
cd vaultx

# Create and activate a virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Apply database migrations
python manage.py migrate

# Create a superuser (optional)
python manage.py createsuperuser

# Run the development server
python manage.py runserver
```

The application will be available at `http://127.0.0.1:8000`.

---

## Environment Variables

Create a `.env` file in the project root with the following variables:

```env
SECRET_KEY=your-django-secret-key
DEBUG=False
DATABASE_URL=postgres://user:password@host:port/dbname
ALLOWED_HOSTS=your-domain.com
FERNET_SALT=your-base64-salt
```

Never commit `.env` files or secrets to version control.

---

## CI/CD Pipeline

VaultX uses GitHub Actions for continuous integration and delivery:

| Step          | Description                                                  |
|---------------|---------------------------------------------------------------|
| Lint and Test | Runs pytest and flake8/black checks on every push              |
| Build         | Validates the build on push to `main`                          |
| Deploy        | On success, Railway automatically redeploys the latest service |

Workflow file: `.github/workflows/ci.yml`

---

## Monitoring and Observability

VaultX ships with a full observability stack:

- Prometheus — the `/metrics` endpoint exposes request counts, latency, and custom application metrics.
- Local Prometheus and Ngrok — metrics are scraped locally and tunnelled to the cloud.
- Grafana Cloud — visualizes application metrics and database statistics using a Prometheus datasource (via Ngrok) and a direct PostgreSQL datasource for database-level panels.

This setup provides real-time visibility into application health, request patterns, and database performance without requiring a dedicated monitoring server.

---

## Project Structure

```
vaultx/
├── vaultx/                 # Project settings
├── accounts/               # User authentication
├── vault/                  # Core password manager app
│   ├── models.py           # Encrypted credential models
│   ├── views.py            # AJAX and standard views
│   ├── encryption.py       # Fernet and PBKDF2 utilities
│   ├── templates/
│   └── static/
├── .github/workflows/      # CI/CD pipeline
├── requirements.txt
├── manage.py
└── README.md
```

---

## Roadmap

- Multi-factor authentication (MFA)
- Encrypted file and document storage
- Shared vaults for teams
- Browser extension integration
- Audit logging for sensitive actions

---

## License

This project is licensed under techcrush community 

---

## Author

Built by techcrush group 13 as a final-year project demonstrating the transition from traditional Django MVC development to production-grade backend engineering.
