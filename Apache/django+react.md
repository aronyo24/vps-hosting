Perfect! If you're building your Django + React.js portfolio on **localhost only**, here's a complete step-by-step coding guide for local development.

---

## 🎯 Goal

Create a **portfolio web app** locally using:

* Django (backend + API)
* React.js (frontend)
* SQLite (default Django DB)
* Axios (React HTTP client)

---

## 🧱 Step 1: Project Setup

### 1. Create Folder & Backend (Django)

```bash
mkdir portfolio_project
cd portfolio_project
python -m venv env
source env/bin/activate   # On Windows: env\Scripts\activate

pip install django djangorestframework django-cors-headers
django-admin startproject backend .
cd backend
python manage.py startapp portfolio
```

---

## 🧠 Step 2: Configure Django

### `backend/settings.py`

* Add apps:

```python
INSTALLED_APPS = [
    ...
    'rest_framework',
    'corsheaders',
    'portfolio',
]
```

* Add middleware:

```python
MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',
    ...
]
CORS_ALLOW_ALL_ORIGINS = True
```

* At the end:

```python
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': ['rest_framework.permissions.AllowAny'],
}
```

---

## 🧩 Step 3: Create Portfolio Model

### `portfolio/models.py`

```python
from django.db import models

class Project(models.Model):
    title = models.CharField(max_length=200)
    description = models.TextField()
    github = models.URLField()
    
    def __str__(self):
        return self.title
```

Run:

```bash
python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser
```

---

## ⚙️ Step 4: Create API

### `portfolio/serializers.py`

```python
from rest_framework import serializers
from .models import Project

class ProjectSerializer(serializers.ModelSerializer):
    class Meta:
        model = Project
        fields = '__all__'
```

### `portfolio/views.py`

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from .models import Project
from .serializers import ProjectSerializer

class ProjectList(APIView):
    def get(self, request):
        projects = Project.objects.all()
        serializer = ProjectSerializer(projects, many=True)
        return Response(serializer.data)
```

### `backend/urls.py`

```python
from django.contrib import admin
from django.urls import path
from portfolio.views import ProjectList

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/projects/', ProjectList.as_view()),
]
```

Run backend:

```bash
python manage.py runserver
```

Open [http://127.0.0.1:8000/api/projects/](http://127.0.0.1:8000/api/projects/) — should show empty list `[]`

---

## 🌐 Step 5: React Frontend Setup

Go back to `portfolio_project` root:

```bash
npx create-react-app frontend
cd frontend
npm install axios
```

### 📄 `frontend/src/App.js`

Replace with:

```jsx
import React, { useEffect, useState } from 'react';
import axios from 'axios';

function App() {
  const [projects, setProjects] = useState([]);

  useEffect(() => {
    axios.get('http://127.0.0.1:8000/api/projects/')
      .then(res => setProjects(res.data))
      .catch(err => console.error(err));
  }, []);

  return (
    <div style={{ padding: '2rem' }}>
      <h1>My Portfolio Projects</h1>
      {projects.map(project => (
        <div key={project.id} style={{ marginBottom: '1rem' }}>
          <h2>{project.title}</h2>
          <p>{project.description}</p>
          <a href={project.github} target="_blank" rel="noreferrer">View on GitHub</a>
        </div>
      ))}
    </div>
  );
}

export default App;
```

Run frontend:

```bash
npm start
```

---

## 🔁 CORS Reminder

If your React is running on port 3000 and Django on 8000, Django must allow React's origin:

In `settings.py`, update:

```python
CORS_ALLOWED_ORIGINS = [
    "http://localhost:3000",
]
```

---

## 🎉 Done!

You now have:

* Django serving a REST API at `/api/projects/`
* React fetching data and displaying it

---

Do you want me to:

* Add **CRUD** (create, edit, delete)?
* Show you how to add **images or skills**?
* Help you **export this project** to a GitHub repo?

Let me know what to build next.
