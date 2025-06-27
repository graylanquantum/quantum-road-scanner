
# Quantum Road Scanner (QRS)

A Flask-based web application that leverages quantum simulations, hypertime analysis, and advanced AI to scan road conditions and report potential hazards—all while ensuring strong encryption, privacy, and data security.

---

## Table of Contents

1. [Features](#features)  
2. [Architecture & Components](#architecture--components)  
3. [Requirements](#requirements)  
4. [Installation](#installation)  
5. [Configuration](#configuration)  
6. [Running the App](#running-the-app)  
7. [Usage](#usage)  
8. [API Endpoints](#api-endpoints)  
9. [Security & Privacy](#security--privacy)  
10. [Contributing](#contributing)  
11. [License](#license)  

---

## Features

- **Quantum Hazard Scanning**  
  Uses Pennylane quantum circuits to generate probabilistic assessments of road conditions.

- **Hypertime & Multiverse Analysis**  
  Simulates multiple temporal dimensions to explore a spectrum of possible futures.

- **Reverse Geocoding**  
  Falls back between an AI-powered LLM lookup and a local nearest-city heuristic.

- **Strong Encryption & Key Management**  
  - AES-GCM for data at rest  
  - Scrypt-derived keys stored in a locked salt file  
  - Automatic rotation of Flask session secret keys at randomized intervals

- **User Management & Access Control**  
  - Argon2 password hashing  
  - Admin-controlled registration (with invite codes)  
  - Per-user rate limiting

- **Secure Data Deletion**  
  - Overwrites expired records with random patterns  
  - Triple VACUUM on SQLite to mitigate data recovery

- **CSRF Protection & CSP**  
  - Flask-WTF CSRF tokens on all forms  
  - HTTP Content Security Policy headers

- **Interactive Dashboard**  
  - Three-step “Grab Coordinates → Street Name → Run Scan” workflow  
  - Storage and listing of past hazard reports

---

## Architecture & Components

- **Flask** — web framework  
- **SQLite (WAL mode)** — local data store  
- **Pennylane & NumPy** — quantum circuit definitions  
- **HTTPX & Backoff** — resilient calls to OpenAI API  
- **Argon2** — password hashing  
- **AES-GCM / Scrypt** — data encryption  
- **WTForms & Flask-WTF** — form handling & CSRF  
- **Waitress** — production WSGI server  
- **Geonamescache** — local city database fallback  

---

## Requirements

- Python 3.10+  
- System dependencies:  
  - `libssl-dev` / `openssl`  
  - `build-essential` (for compiling any C extensions)  
- An OpenAI API key for LLM–based reverse geocoding and PHF  
- Environment variables:
  - `INVITE_CODE_SECRET_KEY` — used for session-secret generation & HMAC on invite codes  
  - `ENCRYPTION_PASSPHRASE` — used to derive the AES-GCM key  

---

## Installation

1. **Clone the repo**  

   git clone https://github.com/your-org/quantum_road_scanner.git
   cd quantum_road_scanner


2. **Create & activate a virtual environment**


   python3 -m venv venv
   source venv/bin/activate
   ```

3. **Install Python dependencies**

   ```bash
   pip install -r requirements.txt
   ```

---

## Configuration

1. **Set environment variables**


   export INVITE_CODE_SECRET_KEY="your-secure-session-secret"
   export ENCRYPTION_PASSPHRASE="your-encryption-passphrase"
   export OPENAI_API_KEY="sk-xxxxxxxxxxxxxxxxxxxxxxxx"


2. **Permissions for salt file directory**
   The app will create `/home/appuser/.keys` and store a salt file there with `chmod 600`. Ensure your process user can write there.

---

## Running the App

Start the production server on port 3000:


python app.py
# or, explicitly:
waitress-serve --host=0.0.0.0 --port=3000 app:app


Then visit:
[http://localhost:3000/home](http://localhost:3000/home)

---

## Usage

1. **Register** (first user becomes admin)
2. **Login**
3. **Dashboard**

   * Enter latitude & longitude (or “Get Current Location”)
   * Fetch street name
   * Select vehicle type, destination, and AI model
   * Run quantum hazard scan
4. **View Past Reports**

   * Click “View” to see detailed hazard info, risk wheel, and text-to-speech controls

---

## API Endpoints

| Path                | Method   | Description                                         |
| ------------------- | -------- | --------------------------------------------------- |
| `/home`             | GET      | Landing page                                        |
| `/login`            | GET/POST | User login                                          |
| `/register`         | GET/POST | User registration                                   |
| `/dashboard`        | GET      | Main dashboard & scan workflow                      |
| `/start_scan`       | POST     | Triggers quantum scan; returns JSON `{ report_id }` |
| `/view_report/<id>` | GET      | Renders full report details                         |
| `/reverse_geocode`  | GET      | Returns `{ street_name }` for given `lat`/`lon`     |

All POST forms and AJAX calls require a valid CSRF token header.

---

## Security & Privacy

* **No plain-text storage** of any user or report data. Everything is AES-GCM encrypted in SQLite.
* **Secure key derivation** via Scrypt and Argon2 for passwords.
* **Automatic secret-key rotation** for Flask sessions.
* **Rate limiting** (13 requests per 15 minutes per user).
* **Secure overwrite** of expired data (7 random passes) + triple VACUUM.
* **CSP headers** restrict scripts/styles to self.
* **PHF filter** via OpenAI to block harmful inputs.

---

## Contributing

1. Fork the repo
2. Create a feature branch
3. Write tests for new functionality
4. Ensure linting & formatting (`black .`, `flake8`)
5. Submit a pull request 

