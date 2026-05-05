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

## 1. Prerequisites

- Python 3.8+ (or any recent version)  
- `pip` installed  
- (Optional but recommended) `virtualenv` or `venv` for an isolated environment  

---

## 2. Create a Virtual Environment (Optional)

```bash
python -m venv venv
# Activate the environment
#   Windows:  venv\Scripts\activate
#   macOS/Linux: source venv/bin/activate
```

---

## 3. Install Django

```bash
pip install django
```

---

## 4. Create a New Django Project

```bash
mkdir login_forms
cd login_forms
django-admin startproject config .

```

---

## 5. Create an App Called `accounts`

```bash
python manage.py startapp accounts
```

Add the new app to `INSTALLED_APPS` in `config/settings.py`:

```python
# myproject/settings.py
INSTALLED_APPS = [
    # ... existing apps
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'accounts',          # <-- add this line
]
```

Also, at the end of settings.py set a redirect URL for after login:

```python
LOGIN_REDIRECT_URL = 'dashboard'
LOGIN_URL = 'login'      # used by @login_required
```

---

## 6. URL Configuration

### 6.1 Project URLs (`config/urls.py`)

```python
# config/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('accounts.urls')),          # Include the app URLs
]
```

### 6.2 App URLs (`accounts/urls.py`)

Create a file `account/urls.py` and add:

```python
# accounts/urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('', views.home, name='home'),
    path('register/', views.register_view, name='register'),
    path('login/', views.login_view, name='login'),
    path('dashboard/', views.dashboard, name='dashboard'),
]
```

---

## 7. Views (`accounts/views.py`)

```python
# accounts/views.py
from django.shortcuts import render, redirect
from django.contrib.auth import login, authenticate
from django.contrib.auth.forms import UserCreationForm, AuthenticationForm
from django.contrib.auth.decorators import login_required

def home(request):
    """Home page with Register / Login buttons."""
    return render(request, 'home.html')

def register_view(request):
    """Handle user registration."""
    if request.method == 'POST':
        form = UserCreationForm(request.POST)
        if form.is_valid():
            user = form.save()
            # Automatically log the user in after successful registration
            login(request, user)
            return redirect('dashboard')
    else:
        form = UserCreationForm()
    return render(request, 'register.html', {'form': form})

def login_view(request):
    """Handle user login."""
    if request.method == 'POST':
        form = AuthenticationForm(request, data=request.POST)
        if form.is_valid():
            # form returns a valid user
            user = form.get_user()
            login(request, user)
            return redirect('dashboard')
    else:
        form = AuthenticationForm()
    return render(request, 'login.html', {'form': form})

@login_required
def dashboard(request):
    """Dashboard page – visible only to logged‑in users."""
    return render(request, 'dashboard.html')
```

---

## 8. Templates

Create a folder `templates` inside the `login_forms` root directory:

```
login_forms/
├── templates/
|.  └──accounts/
│      ├── base.html
│      ├── home.html
│      ├── register.html
│      ├── login.html
│      └── dashboard.html
```

Make sure Django knows where to find templates by adding this to `config/settings.py`:

```python
# config/settings.py
TEMPLATES = [
    {
        # ...
        'DIRS': [BASE_DIR / 'templates' / 'accounts'],
        # ...
    },
]
```

### 8.1 Base Template (`base.html`)

```html
<!-- /templates/accounts/base.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{% block title %}My Site{% endblock %}</title>
</head>
<body>
    {% block content %}{% endblock %}
</body>
</html>
```

### 8.2 Home Page (`home.html`)

```html
<!-- /templates/accounts//home.html -->
{% extends "base.html" %}

{% block title %}Home{% endblock %}

{% block content %}
<h1>Welcome to the Site</h1>
<p>
    <a href="{% url 'register' %}"><button>Register</button></a>
    <a href="{% url 'login' %}"><button>Login</button></a>
</p>
{% endblock %}
```

### 8.3 Register Page (`register.html`)

```html
<!-- /templates/accounts/register.html -->
{% extends "base.html" %}

{% block title %}Register{% endblock %}

{% block content %}
<h1>Register</h1>
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Register</button>
</form>
{% endblock %}
```

### 8.4 Login Page (`login.html`)

```html
<!-- /templates/accounts/login.html -->
{% extends "base.html" %}

{% block title %}Login{% endblock %}

{% block content %}
<h1>Login</h1>
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Login</button>
</form>
{% endblock %}
```

### 8.5 Dashboard Page (`dashboard.html`)

```html
<!-- /templates/accounts/dashboard.html -->
{% extends "base.html" %}

{% block title %}Dashboard{% endblock %}

{% block content %}
<h1>Dashboard</h1>
<p>Hello, {{ request.user.username }}! You are now logged in.</p>
<p><a href="{% url 'home' %}">Back to Home</a></p>
{% endblock %}
```

---

## 9. Migrate the Database

```bash
python manage.py migrate
```

---

## 10. Create a Superuser (Optional)

```bash
python manage.py createsuperuser
```

---

## 11. Run the Development Server

```bash
python manage.py runserver
```

Open a browser and go to `http://127.0.0.1:8000/`.  
You should see the home page with **Register** and **Login** buttons.  

---

## 12. Using the App

1. **Register** – Click the *Register* button, create a username/password.  
   After successful registration you’ll be redirected to the dashboard.  

2. **Login** – Click the *Login* button, enter your credentials.  
   On success you’ll land on the dashboard.  

3. **Dashboard** – Displays a greeting with your username.  
   Only accessible after login; otherwise you’ll be redirected to the login page.  

4. **Home** – Accessible to anyone; no login required.  

---

## 13. Summary

- **Project layout**: `myproject/` (project) + `main/` (app).  
- **Templates**: Pure HTML, no CSS.  
- **Authentication**: Uses Django’s built‑in forms (`UserCreationForm`, `AuthenticationForm`).  
- **Protected view**: `@login_required` decorator on the dashboard.  


--------------------------------------------------------

## 14. Screenshots

### Home Page
![Home Page](screenshots/home.png)

### Login Page
![Login Page](screenshots/login.png)

### Register Page  
![Register](screenshots/register.png)

### Dashboard Page
![Dashboard](screenshots/dashboard.png)

