# Overview
The main reason behind this project is to learn complete Docker and AWS setup for Django Project

-----------------------------------------------------------------------------------------------

## Step 1: Installation 

### Project Setup
Create normal Django project inside virtual env
```zsh
mkdir todo
cd todo
python3 -m venv .venv
source .venv/bin/activate
----
#inside virtual enviroment
django-admin startproject todo_project .
pip freeze > requirements.txt

```
### Requirements.txt
```txt
asgiref==3.9.1
Django==5.2.5
sqlparse==0.5.3

```
-----------------------------------------------------------------------------------------------

## Step 2: Configure Enviromental variables

Create .env file for environment variables (secret key, DB credentials).
using python-decouple

```zsh
touch .env.local
pip install python-decouple
```
### Create .env.local file with following configurations
example,
```.env
SECRET_KEY='yoursecurekey'
DEBUG=True
ALLOWED_HOSTS = localhost,127.0.0.1,
```
###  And Update settings.py
 
 ```py
from decouple import config, Csv
SECRET_KEY = config("SECRET_KEY")
DEBUG = config("DEBUG", default=False, cast=bool)
ALLOWED_HOSTS = config("ALLOWED_HOSTS", default="localhost", cast=Csv())

 ```

 Checkpoint, start web server to check if .env is set properly
 ```zsh
 python manage.py runserver
 ```

-----------------------------------------------------------------------------------------------


## Step 3:  Todo app

### Make Todo app
```zsh
python manage.py startapp todos
```
Add the app to INSTALLED_APPS in settings.py.
```py
INSTALLED_APPS = [
    'todos.apps.TodosConfig',
]

```

### Todo Model
Simple model with content, completed, and created_at fields.
And update admin to reflect those todos

-----------------------------------------------------------------------------------------------


## Step 4:  Migrate and Create Super User

### Migrate

```zsh
python manage.py makemigrations
python manage.py migrate

```

### Superuser

```zsh
python manage.py createsuperuser

```

 Checkpoint, start web server to check if admin and models is working properly or not.

 ```zsh
 python manage.py runserver
 ```

-----------------------------------------------------------------------------------------------


## Step 5:  Docker

### Simply run django app using docker

We need two files, Dockerfile and docker-compose.yml

```zsh
touch Dockerfile && touch docker-compose.yml
```
### Basic Dockerfile

```Dockerfile
# Base image
FROM python:3.12-slim

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# Set workdir
WORKDIR /app

# Install system deps
RUN apt-get update && apt-get install -y \
    libpq-dev gcc \
    && rm -rf /var/lib/apt/lists/*

# Install Python deps
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy project
COPY . .

# Run Django server (for dev)
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]

```

### Basic docker-compose.yml

```docker compose.yml

services:
  web:
    build: .
    command: python manage.py runserver 0.0.0.0:8000
    volumes:
      - .:/app
    ports:
      - "8000:8000"
    env_file:
      - .env
    depends_on:
      - db

  db:
    image: postgres:17
    env_file:
      - .env
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

volumes:
  postgres_data:


```


-----------------------------------------------------------------------------------------------

## Step 6:  Gunicorn

### Install gunicorn
```zsh
pip install gunicorn
```
### Update Dockerfile and docker-compose.yml

```Dockerfile
#Production
CMD ["gunicorn", "--bind", "0.0.0.0:8000", "todo_project.wsgi:application"]
```
```docker-compose.yml
command: gunicorn todo_project.wsgi:application --bind 0.0.0.0:8000
```
Checkpoint: Start server to see if gunicorn is running properly or not. 

```zsh
 python manage.py runserver
 ```

-----------------------------------------------------------------------------------------------

## Step 6:  Gunicorn

### Install gunicorn