# 🔐 Auth System with 2FA

A from-scratch authentication system built with **Flask** and **SQLite**. It handles registration, login, sessions, brute-force protection and TOTP two-factor authentication, and it's designed to be reused as the login module for other apps.

## 📸 Screenshots

| Register | Login |
|---|---|
<img width="1903" height="971" alt="image" src="https://github.com/user-attachments/assets/55072d0e-61a7-4fef-b7b9-c21199a91982" />

| Enable 2FA (QR code) | 2FA code on login |
|---|---|
|<img width="1905" height="963" alt="image" src="https://github.com/user-attachments/assets/74fefd11-6e74-4d7c-9765-b4ad05a54533" />|<img width="1912" height="972" alt="image" src="https://github.com/user-attachments/assets/ccf03b94-9f81-4b4f-9ec5-cda849811635" />|


| Dashboard (2FA on) | Account lockout |
|---|---|
|<img width="1912" height="982" alt="image" src="https://github.com/user-attachments/assets/c32bdaef-bb9e-417c-aaa0-1fe7f97c571d" />|<img width="1910" height="980" alt="image" src="https://github.com/user-attachments/assets/f0b4c5f5-c036-42c5-9064-dc7dac9b17fd" />|



## ✨ Features

- Registration and login with **bcrypt-hashed** passwords
- Session-based authentication with protected routes
- **Account lockout** after 5 failed attempts
- **IP-based rate limiting** on login, register and 2FA routes
- **TOTP two-factor authentication**, compatible with Google Authenticator
- **Two-stage login**: a correct password alone never grants access

## 🔄 How login works

```
Email + password
      │
      ├── wrong ──► failed_attempts + 1 ──► 5th failure locks the account
      │
      ▼ correct
 2FA enabled?
      │
      ├── no ───► Dashboard
      │
      ▼ yes
 Enter 6-digit code
      │
      ├── wrong ──► failed_attempts + 1 (same lockout)
      │
      ▼ correct
   Dashboard
```

## 🛠️ Tech stack

Python · Flask · SQLite · bcrypt · pyotp · qrcode · Flask-Limiter

## 🚀 Run locally

```
git clone https://github.com/Kevin03-cyber/auth-system.git
cd auth-system
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
python app.py
```

Then open http://127.0.0.1:5001/register

Optional: set your own session key with `$env:SECRET_KEY="your-long-random-string"` before running. Set `$env:FLASK_DEBUG="1"` only during development.

## 🧪 What to try

1. Register an account and log in
2. Click **Enable 2FA**, scan the QR code and verify a code
3. Log out and log in again. The code page appears after the password
4. Enter 5 wrong passwords or codes to trigger the lockout
5. Open `/dashboard` without logging in to see the route protection

## 🛡️ Security design

- Passwords are hashed with bcrypt (salted and slow by design). Plaintext is never stored
- Wrong email and wrong password give the same error, so attackers can't discover which emails exist
- Wrong 2FA codes count toward the same lockout as wrong passwords
- A correct password does not reset the failure counter for 2FA accounts, so an attacker can't reset it to keep guessing codes
- 2FA is switched on only after the user proves their authenticator app works
- Session cookies are `HttpOnly` and `SameSite=Lax`, and the secret key comes from an environment variable
- Debug mode is off by default

## ⚠️ Known limitations

- TOTP secrets are stored unencrypted in the database
- No recovery codes if the user loses their phone
- No CSRF tokens on forms
- Rate limit counters are in memory and reset when the server restarts
- No email verification or password reset
- Cookies need the `Secure` flag when deployed over HTTPS

## 🔮 Future improvements

- Encrypt TOTP secrets at rest
- Recovery codes and a password reset flow
- CSRF protection with Flask-WTF
- Expose the system as a REST API with JWT so other apps can use it as a separate auth service

## 👤 Author

Kevin · [GitHub](https://github.com/Kevin03-cyber)
