# Django Project Setup: Home, Register, Login, Dashboard

A minimal Django project with plain HTML pages for:

- **Home** page
- **Register** page
- **Login** page
- **Dashboard** page

This project uses **Django’s built-in authentication system** and keeps everything simple with:

- Function-based views
- Built-in auth forms
- No CSS / no styling
- Plain HTML templates only

---

## Features

- Home page with **Register** and **Login** buttons
- User registration using `UserCreationForm`
- User login using `AuthenticationForm`
- Protected dashboard page for authenticated users only
- Automatic login after successful registration
- Simple and beginner-friendly structure

---

## Prerequisites

Before starting, make sure you have:

- Python 3.8+
- `pip` installed
- (Optional) `venv` or `virtualenv`

---

## 1. Create a Virtual Environment (Optional)

```bash
python -m venv venv

Activate it:

