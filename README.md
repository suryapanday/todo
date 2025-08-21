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
