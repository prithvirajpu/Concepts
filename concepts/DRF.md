# DRF Notes (Django REST Framework)

> Toolkit for building clean, powerful REST APIs in Django, with serializers, views, and built‑in tools for auth, permissions, and docs.

## Table of Contents
- [DRF Notes (Django REST Framework)](#drf-notes-django-rest-framework)
  - [Table of Contents](#table-of-contents)
  - [What is DRF?](#what-is-drf)
  - [Why use DRF?](#why-use-drf)
  - [Key Features](#key-features)
  - [Core Concepts](#core-concepts)
  - [Basic Architecture](#basic-architecture)
  - [Where is DRF Used?](#where-is-drf-used)
  - [Real‑World Applications](#realworld-applications)
  - [How DRF Works (Step‑by‑Step)](#how-drf-works-stepbystep)
  - [CSRF and Frontend (React Example)](#csrf-and-frontend-react-example)
    - [Case 1: Session‑based auth](#case-1-sessionbased-auth)
    - [Case 2: Token‑based auth (JWT)](#case-2-tokenbased-auth-jwt)
  - [Example – Simple API (Tickets)](#example--simple-api-tickets)


## What is DRF?

**Django REST Framework** is a **third‑party library for Django** that makes building RESTful APIs fast and consistent. [web:48][web:49]  

- It extends Django’s views and models to expose **JSON endpoints** instead of templates.  
- You mainly use:  
  - `serializers` (Python ↔ JSON),  
  - `views` / `ViewSets`,  
  - `routers` to auto‑generate URL patterns.  

In short:  
- **Django** = web app + ORM.  
- **DRF** = JSON API layer on top of Django. [web:43][web:45]  


## Why use DRF?

- **Fast to build APIs**:  
  - Model‑first design → `ModelSerializer` auto‑generates JSON structure from your model. [web:49]  
- **Consistent structure**:  
  - REST‑style endpoints, pagination, filters, and browsable API all included. [web:43]  
- **Built‑in auth & permissions**:  
  - Session, token, JWT, OAuth; fine‑grained permissions per view. [web:43][web:45]  
- **Great for React / Vue / mobile**:  
  - Delivers clean JSON so your frontend can fetch and render data easily. [web:49]  

You *can* build APIs in plain Django with `JsonResponse`, but DRF gives you reusable patterns, validation, and docs out‑of‑the‑box. [web:41][web:49]  


## Key Features

- Serializers (model ↔ JSON, with validation) [web:43][web:49]  
- Class‑based views (`APIView`, `GenericAPIView`, `ViewSet`, `ModelViewSet`) [web:47][web:49]  
- Routers to auto‑generate URLs from `ViewSets` [web:49]  
- Authentication & permissions (session, token, JWT) [web:43][web:45]  
- Pagination, filters, ordering [web:43]  
- Browsable API (web UI for testing endpoints) [web:48]  
- Optional: `drf‑yasg` / `drf‑spectacular` for Swagger/OpenAPI docs [web:66][web:67]  


## Core Concepts

- **Serializer**:  
  - Converts Django model/instance to JSON and validates incoming data.  
  - Example: `TicketSerializer` for your ticket model.  

- **View / ViewSet**:  
  - Handles HTTP methods (`GET`, `POST`, etc.) and returns JSON.  
  - `ModelViewSet` gives you CRUD for free for a model.  

- **Router**:  
  - Automatically maps `ViewSets` to URLs like `/tickets/`, `/tickets/1/`.  

- **Authentication & Permissions**:  
  - `IsAuthenticated`, `IsAdminUser`, custom classes to control who can do what.  

- **Pagination**:  
  - Splits large lists into pages (e.g., `page=1`, `page=2`).  

These concepts let you define **what data is exposed**, **how it is formatted**, and **who can access it**. [web:43][web:49]  


## Basic Architecture

1. **Frontend (React, mobile, etc.)** hits an API endpoint (e.g., `/api/tickets/`).  
2. **Django URL** routes the request to a DRF `View` or `ViewSet`.  
3. **DRF View**:  
   - Calls serializer on the model/queryset.  
   - Performs validation for `POST`/`PUT`/`PATCH`.  
4. **Response**:  
   - Returns JSON (often with `status` and `data`).  
   - Browser‑rendered UI if you access it directly.  

Under the hood, you can plug in:  
- DB (PostgreSQL),  
- Cache (Redis),  
- Background jobs (Celery). [web:49]  


## Where is DRF Used?

- React / Vue / Angular frontends talking to Django backend. [web:49]  
- Mobile apps (Flutter, React Native, native iOS/Android) using JSON APIs. [web:43]  
- Microservices exposing REST over HTTP.  
- Admin dashboards that need JSON‑only APIs.  


## Real‑World Applications

1. **Ticketing / Helpdesk Backend**  
   - Model: `Ticket`, `Agent`, `Status`.  
   - DRF exposes: `/api/tickets/` → React lists tickets, filters, and creates new ones.  

2. **E‑commerce Product API**  
   - `/api/products/`, `/api/orders/` → Android/iOS app uses them.  

3. **User Profiles & Auth**  
   - `/api/users/`, `/api/login/`, `/api/profile/` → React login page + profile page.  

4. **Analytics & Dashboards**  
   - `/api/metrics/` endpoints delivering JSON for charts.  

5. **Microservices**  
   - Each service exposes a small DRF API and talks to others via HTTP.  

DRF is especially handy when you already use Django and want to add a **React frontend** or **mobile app** on top. [web:43][web:49]  


## How DRF Works (Step‑by‑Step)

1. **Define model** (e.g., `Ticket`) in `models.py`.  
2. **Create serializer**:
   ```python
   from rest_framework import serializers
   from .models import Ticket

   class TicketSerializer(serializers.ModelSerializer):
       class Meta:
           model = Ticket
           fields = "__all__"
   ```
3. **Create ViewSet**:
   ```python
   from rest_framework import viewsets
   from .models import Ticket
   from .serializers import TicketSerializer

   class TicketViewSet(viewsets.ModelViewSet):
       queryset = Ticket.objects.all()
       serializer_class = TicketSerializer
   ```
4. **Register router** in `urls.py`:
   ```python
   from django.urls import path, include
   from rest_framework import routers
   from .views import TicketViewSet

   router = routers.DefaultRouter()
   router.register("tickets", TicketViewSet)

   urlpatterns = [
       path("api/", include(router.urls)),
   ]
   ```
5. **React consumes**:
   ```js
   axios.get("/api/tickets/")
     .then(res => console.log(res.data));
   ```  

This “model‑serializer‑ViewSet‑router” pattern is the standard DRF workflow. [web:49]  


## CSRF and Frontend (React Example)

### Case 1: Session‑based auth

When you log in via Django (sessions) and use `SessionAuthentication` in DRF:

- Django sends a `csrftoken` cookie.  
- React reads it and sends `X‑CSRFToken` header on every mutating request.  
- Example Axios setup that handles this:
  ```js
  axios.defaults.xsrfCookieName = "csrftoken";
  axios.defaults.xsrfHeaderName = "X-CSRFToken";
  axios.defaults.withCredentials = true;  // send cookies
  ```  
- In Django settings:
  ```python
  CSRF_COOKIE_HTTPONLY = False  # so JS can read
  ```
  (with `django‑cors‑headers` so React can talk to Django.) [web:53][web:59]  


### Case 2: Token‑based auth (JWT)

If you use `rest‑framework‑simplejwt` or similar:

- No Django session → no CSRF cookie.  
- React sends:
  ```js
  Authorization: Bearer <token>
  ```  
- You disable CSRF‑checking for those API views (or don’t use `SessionAuthentication`).  
- You avoid CSRF altogether and rely on tokens, HTTPS, and short‑lived JWTs.  

For your ticketing‑style project, **token‑based auth is usually simpler** with React. [web:43][web:45]  


## Example – Simple API (Tickets)

Assume you already have a `Ticket` model in Django:

`models.py`:
```python
from django.db import models

class Ticket(models.Model):
    title = models.CharField(max_length=200)
    description = models.TextField()
    status = models.CharField(max_length=50)
    created_at = models.DateTimeField(auto_now_add=True)
```

`serializers.py`:
```python
from rest_framework import serializers
from .models import Ticket

class TicketSerializer(serializers.ModelSerializer):
    class Meta:
        model = Ticket
        fields = "__all__"
```

`views.py`:
```python
from rest_framework import viewsets
from .models import Ticket
from .serializers import TicketSerializer

class TicketViewSet(viewsets.ModelViewSet):
    queryset = Ticket.objects.all()
    serializer_class = TicketSerializer
```

`urls.py`:
```python
from django.urls import path, include
from rest_framework import routers
from .views import TicketViewSet

router = routers.DefaultRouter()
router.register("tickets", TicketViewSet)

urlpatterns = [
    path("api/", include(router.urls)),
]
```

Now your endpoints are:

- `GET /api/tickets/`       → list  
- `POST /api/tickets/`      → create  
- `GET /api/tickets/<id>/` → detail  
- `PUT/PATCH/DELETE /api/tickets/<id>/` → update/delete  

From React:
```js
axios.post("/api/tickets/", {
  title: "New bug",
  description: "...',
  status: "open",
})
.then(response => console.log(response.data));
```
