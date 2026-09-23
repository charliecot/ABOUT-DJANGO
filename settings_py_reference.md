# Django `settings.py` — Master Settings Names Reference

This is a practical reference for the **actual setting names** you will commonly encounter when building Django and Django REST Framework projects.

---

# 1. BASIC DJANGO SETTINGS

## `SECRET_KEY`

```python
SECRET_KEY = "your-secret-key"
```

Used for cryptographic signing.

Used by things such as:

```text
sessions
password-reset tokens
CSRF
signed data
cryptographic operations
```

In production, keep it secret.

---

## `DEBUG`

```python
DEBUG = True
```

Development:

```python
DEBUG = True
```

Production:

```python
DEBUG = False
```

---

## `ALLOWED_HOSTS`

```python
ALLOWED_HOSTS = [
    "localhost",
    "127.0.0.1",
]
```

Controls which host/domain names Django accepts.

For example:

```text
localhost
127.0.0.1
example.com
api.example.com
```

---

## `ROOT_URLCONF`

```python
ROOT_URLCONF = "config.urls"
```

Points Django to your main URL configuration.

---

## `WSGI_APPLICATION`

```python
WSGI_APPLICATION = "config.wsgi.application"
```

Used for WSGI deployments.

---

## `ASGI_APPLICATION`

```python
ASGI_APPLICATION = "config.asgi.application"
```

Used for ASGI deployments.

Useful for:

```text
async Django
WebSockets
Django Channels
```

---

# 2. INSTALLED APPS

## `INSTALLED_APPS`

```python
INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",

    # Third-party
    "rest_framework",

    # Your apps
    "api",
]
```

This is where you register Django applications and third-party packages.

---

# 3. MIDDLEWARE

## `MIDDLEWARE`

```python
MIDDLEWARE = [
    "django.middleware.security.SecurityMiddleware",
    "django.contrib.sessions.middleware.SessionMiddleware",
    "django.middleware.common.CommonMiddleware",
    "django.middleware.csrf.CsrfViewMiddleware",
    "django.contrib.auth.middleware.AuthenticationMiddleware",
    "django.contrib.messages.middleware.MessageMiddleware",
    "django.middleware.clickjacking.XFrameOptionsMiddleware",
]
```

Middleware processes requests/responses.

Examples:

```text
security
sessions
authentication
CSRF
CORS
messages
```

---

# 4. DATABASE

## `DATABASES`

The main database setting.

### SQLite

```python
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.sqlite3",
        "NAME": BASE_DIR / "db.sqlite3",
    }
}
```

### PostgreSQL

```python
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql",
        "NAME": "mydatabase",
        "USER": "postgres",
        "PASSWORD": "password",
        "HOST": "localhost",
        "PORT": "5432",
    }
}
```

### MySQL

```python
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.mysql",
        "NAME": "mydatabase",
        "USER": "root",
        "PASSWORD": "password",
        "HOST": "localhost",
        "PORT": "3306",
    }
}
```

Important database keys:

```text
ENGINE
NAME
USER
PASSWORD
HOST
PORT
OPTIONS
CONN_MAX_AGE
CONN_HEALTH_CHECKS
ATOMIC_REQUESTS
AUTOCOMMIT
TIME_ZONE
TEST
```

---

# 5. CUSTOM USER MODEL

## `AUTH_USER_MODEL`

Very important when using a custom user.

```python
AUTH_USER_MODEL = "api.User"
```

If your model is:

```python
class User(AbstractUser):
    pass
```

inside:

```text
api/models.py
```

then:

```python
AUTH_USER_MODEL = "api.User"
```

---

# 6. AUTHENTICATION BACKENDS

## `AUTHENTICATION_BACKENDS`

Controls how Django authenticates users.

Default:

```python
AUTHENTICATION_BACKENDS = [
    "django.contrib.auth.backends.ModelBackend",
]
```

With django-allauth:

```python
AUTHENTICATION_BACKENDS = [
    "django.contrib.auth.backends.ModelBackend",
    "allauth.account.auth_backends.AuthenticationBackend",
]
```

---

# 7. PASSWORD VALIDATION

## `AUTH_PASSWORD_VALIDATORS`

```python
AUTH_PASSWORD_VALIDATORS = [
    {
        "NAME":
        "django.contrib.auth.password_validation.UserAttributeSimilarityValidator",
    },
    {
        "NAME":
        "django.contrib.auth.password_validation.MinimumLengthValidator",
    },
    {
        "NAME":
        "django.contrib.auth.password_validation.CommonPasswordValidator",
    },
    {
        "NAME":
        "django.contrib.auth.password_validation.NumericPasswordValidator",
    },
]
```

Important validators:

```text
UserAttributeSimilarityValidator
MinimumLengthValidator
CommonPasswordValidator
NumericPasswordValidator
```

---

# 8. INTERNATIONALIZATION

## `LANGUAGE_CODE`

```python
LANGUAGE_CODE = "en-us"
```

---

## `TIME_ZONE`

```python
TIME_ZONE = "Africa/Douala"
```

or:

```python
TIME_ZONE = "UTC"
```

---

## `USE_I18N`

```python
USE_I18N = True
```

---

## `USE_TZ`

```python
USE_TZ = True
```

---

# 9. STATIC FILES

## `STATIC_URL`

```python
STATIC_URL = "static/"
```

---

## `STATIC_ROOT`

Production static collection:

```python
STATIC_ROOT = BASE_DIR / "staticfiles"
```

Then:

```bash
python manage.py collectstatic
```

---

# 10. MEDIA FILES

## `MEDIA_URL`

```python
MEDIA_URL = "/media/"
```

---

## `MEDIA_ROOT`

```python
MEDIA_ROOT = BASE_DIR / "media"
```

Used for uploaded files.

For example:

```python
image = models.ImageField(
    upload_to="products/"
)
```

---

# 11. DEFAULT PRIMARY KEY

## `DEFAULT_AUTO_FIELD`

```python
DEFAULT_AUTO_FIELD = "django.db.models.BigAutoField"
```

Controls the default primary-key field for models.

---

# 12. TEMPLATES

## `TEMPLATES`

Important settings inside it include:

```python
TEMPLATES = [
    {
        "BACKEND":
        "django.template.backends.django.DjangoTemplates",

        "DIRS": [
            BASE_DIR / "templates",
        ],

        "APP_DIRS": True,

        "OPTIONS": {
            "context_processors": [
                "django.template.context_processors.request",
                "django.contrib.auth.context_processors.auth",
                "django.contrib.messages.context_processors.messages",
            ],
        },
    },
]
```

Important keys:

```text
BACKEND
DIRS
APP_DIRS
OPTIONS
context_processors
```

---

# 13. DJANGO REST FRAMEWORK

## `REST_FRAMEWORK`

This is one of the most important settings for your API.

```python
REST_FRAMEWORK = {
}
```

---

## Default authentication

```python
REST_FRAMEWORK = {
    "DEFAULT_AUTHENTICATION_CLASSES": [
        "rest_framework.authentication.SessionAuthentication",
        "rest_framework.authentication.BasicAuthentication",
    ],
}
```

With JWT:

```python
REST_FRAMEWORK = {
    "DEFAULT_AUTHENTICATION_CLASSES": [
        "rest_framework_simplejwt.authentication.JWTAuthentication",
    ],
}
```

---

## Default permissions

```python
REST_FRAMEWORK = {
    "DEFAULT_PERMISSION_CLASSES": [
        "rest_framework.permissions.IsAuthenticated",
    ],
}
```

Other common permissions:

```text
AllowAny
IsAuthenticated
IsAdminUser
IsAuthenticatedOrReadOnly
```

---

# 14. DRF THROTTLING

Inside `REST_FRAMEWORK`:

```python
REST_FRAMEWORK = {
    "DEFAULT_THROTTLE_CLASSES": [
        "rest_framework.throttling.AnonRateThrottle",
        "rest_framework.throttling.UserRateThrottle",
    ],

    "DEFAULT_THROTTLE_RATES": {
        "anon": "10/minute",
        "user": "100/minute",
    },
}
```

Important names:

```text
DEFAULT_THROTTLE_CLASSES
DEFAULT_THROTTLE_RATES
```

---

# 15. DRF PAGINATION

```python
REST_FRAMEWORK = {
    "DEFAULT_PAGINATION_CLASS":
        "rest_framework.pagination.PageNumberPagination",

    "PAGE_SIZE": 10,
}
```

Common pagination classes:

```text
PageNumberPagination
LimitOffsetPagination
CursorPagination
```

---

# 16. DRF FILTERING

If using `django-filter`:

```python
INSTALLED_APPS = [
    ...
    "django_filters",
]
```

Then:

```python
REST_FRAMEWORK = {
    "DEFAULT_FILTER_BACKENDS": [
        "django_filters.rest_framework.DjangoFilterBackend",
    ],
}
```

Important setting:

```text
DEFAULT_FILTER_BACKENDS
```

---

# 17. DRF RENDERERS

```python
REST_FRAMEWORK = {
    "DEFAULT_RENDERER_CLASSES": [
        "rest_framework.renderers.JSONRenderer",
        "rest_framework.renderers.BrowsableAPIRenderer",
    ],
}
```

---

# 18. DRF PARSERS

```python
REST_FRAMEWORK = {
    "DEFAULT_PARSER_CLASSES": [
        "rest_framework.parsers.JSONParser",
        "rest_framework.parsers.FormParser",
        "rest_framework.parsers.MultiPartParser",
    ],
}
```

Important when receiving:

```text
JSON
form data
multipart/form-data
file uploads
```

---

# 19. DRF EXCEPTION HANDLER

```python
REST_FRAMEWORK = {
    "EXCEPTION_HANDLER":
        "api.exceptions.custom_exception_handler",
}
```

---

# 20. DRF SCHEMA / API DOCUMENTATION

If using drf-spectacular:

```python
INSTALLED_APPS = [
    ...
    "drf_spectacular",
]
```

Then:

```python
REST_FRAMEWORK = {
    "DEFAULT_SCHEMA_CLASS":
        "drf_spectacular.openapi.AutoSchema",
}
```

And:

```python
SPECTACULAR_SETTINGS = {
    "TITLE": "E-Commerce API",
    "DESCRIPTION": "API documentation",
    "VERSION": "1.0.0",
    "SERVE_INCLUDE_SCHEMA": False,
}
```

---

# 21. SIMPLE JWT

Install:

```bash
pip install djangorestframework-simplejwt
```

Basic:

```python
REST_FRAMEWORK = {
    "DEFAULT_AUTHENTICATION_CLASSES": [
        "rest_framework_simplejwt.authentication.JWTAuthentication",
    ],
}
```

Main JWT configuration:

```python
SIMPLE_JWT = {
    "ACCESS_TOKEN_LIFETIME": timedelta(minutes=15),

    "REFRESH_TOKEN_LIFETIME": timedelta(days=7),

    "ROTATE_REFRESH_TOKENS": True,

    "BLACKLIST_AFTER_ROTATION": True,

    "AUTH_HEADER_TYPES": ("Bearer",),

    "AUTH_HEADER_NAME": "HTTP_AUTHORIZATION",

    "USER_ID_FIELD": "id",

    "USER_ID_CLAIM": "user_id",

    "TOKEN_TYPE_CLAIM": "token_type",

    "JTI_CLAIM": "jti",

    "UPDATE_LAST_LOGIN": False,
}
```

Important JWT setting names:

```text
ACCESS_TOKEN_LIFETIME
REFRESH_TOKEN_LIFETIME
ROTATE_REFRESH_TOKENS
BLACKLIST_AFTER_ROTATION
AUTH_HEADER_TYPES
AUTH_HEADER_NAME
USER_ID_FIELD
USER_ID_CLAIM
TOKEN_TYPE_CLAIM
JTI_CLAIM
UPDATE_LAST_LOGIN
```

---

# 22. SIMPLE JWT BLACKLIST

Add:

```python
INSTALLED_APPS = [
    ...
    "rest_framework_simplejwt.token_blacklist",
]
```

Then:

```bash
python manage.py migrate
```

Useful command:

```bash
python manage.py flushexpiredtokens
```

---

# 23. DJOSER

Install:

```bash
pip install djoser
```

Add:

```python
INSTALLED_APPS = [
    ...
    "djoser",
]
```

Djoser configuration:

```python
DJOSER = {
    "USER_ID_FIELD": "id",
    "LOGIN_FIELD": "username",

    "SEND_ACTIVATION_EMAIL": False,
    "SEND_CONFIRMATION_EMAIL": False,

    "SET_PASSWORD_RETYPE": True,

    "PASSWORD_RESET_CONFIRM_URL":
        "password/reset/confirm/{uid}/{token}",

    "ACTIVATION_URL":
        "activate/{uid}/{token}",
}
```

If using Djoser with DRF token authentication:

```python
INSTALLED_APPS = [
    ...
    "rest_framework.authtoken",
    "djoser",
]
```

If using Djoser + SimpleJWT:

```python
DJOSER = {
    "TOKEN_MODEL": None,
}
```

---

# 24. DJANGO ALLAUTH

For allauth:

```python
INSTALLED_APPS = [
    ...
    "allauth",
    "allauth.account",
    "allauth.socialaccount",
]
```

Authentication backend:

```python
AUTHENTICATION_BACKENDS = [
    "django.contrib.auth.backends.ModelBackend",
    "allauth.account.auth_backends.AuthenticationBackend",
]
```

Site configuration:

```python
SITE_ID = 1
```

For social providers, add the provider app, for example:

```python
"allauth.socialaccount.providers.google",
```

Then configure provider credentials through the appropriate allauth configuration.

---

# 25. CORS

Install:

```bash
pip install django-cors-headers
```

Add:

```python
INSTALLED_APPS = [
    ...
    "corsheaders",
]
```

Middleware:

```python
MIDDLEWARE = [
    "corsheaders.middleware.CorsMiddleware",

    "django.middleware.security.SecurityMiddleware",
    "django.contrib.sessions.middleware.SessionMiddleware",

    ...
]
```

Allowed frontend origins:

```python
CORS_ALLOWED_ORIGINS = [
    "http://localhost:5173",
    "http://127.0.0.1:5173",
]
```

IMPORTANT:

Do NOT write:

```python
"http://localhost:5173/"
```

The trailing `/` causes:

```text
corsheaders.E014
```

---

# 26. CORS CREDENTIALS

If your frontend sends cookies/session credentials:

```python
CORS_ALLOW_CREDENTIALS = True
```

This is particularly relevant for cookie/session authentication.

---

# 27. CSRF TRUSTED ORIGINS

Different from CORS.

```python
CSRF_TRUSTED_ORIGINS = [
    "http://localhost:5173",
]
```

Notice:

```text
CORS_ALLOWED_ORIGINS
```

and:

```text
CSRF_TRUSTED_ORIGINS
```

are different settings.

---

# 28. CACHE

Django's general cache configuration:

```python
CACHES = {
    "default": {
        "BACKEND":
            "django.core.cache.backends.locmem.LocMemCache",

        "LOCATION": "unique-cache",
    }
}
```

For Redis:

```bash
pip install django-redis
```

Then:

```python
CACHES = {
    "default": {
        "BACKEND":
            "django_redis.cache.RedisCache",

        "LOCATION":
            "redis://127.0.0.1:6379/1",

        "OPTIONS": {
            "CLIENT_CLASS":
                "django_redis.client.DefaultClient",
        },
    },
}
```

Important setting:

```text
CACHES
```

Important Redis keys:

```text
BACKEND
LOCATION
OPTIONS
CLIENT_CLASS
```

---

# 29. REDIS

A Redis URL commonly looks like:

```python
"redis://127.0.0.1:6379/1"
```

Breakdown:

```text
redis://
   ↓
protocol

127.0.0.1
   ↓
Redis host

6379
   ↓
Redis port

/1
   ↓
Redis database number
```

Redis databases are numbered:

```text
0
1
2
3
...
```

For example:

```python
CELERY_BROKER_URL = "redis://127.0.0.1:6379/0"

CACHES = {
    "default": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379/1",
    }
}
```

You can separate:

```text
Redis DB 0 → Celery
Redis DB 1 → Django cache
```

---

# 30. CELERY

Install:

```bash
pip install celery redis
```

Create Celery configuration.

Important Django settings:

```python
CELERY_BROKER_URL = "redis://127.0.0.1:6379/0"

CELERY_RESULT_BACKEND = "redis://127.0.0.1:6379/0"

CELERY_ACCEPT_CONTENT = [
    "json",
]

CELERY_TASK_SERIALIZER = "json"

CELERY_RESULT_SERIALIZER = "json"

CELERY_TIMEZONE = "Africa/Douala"

CELERY_ENABLE_UTC = True
```

Important settings:

```text
CELERY_BROKER_URL
CELERY_RESULT_BACKEND
CELERY_ACCEPT_CONTENT
CELERY_TASK_SERIALIZER
CELERY_RESULT_SERIALIZER
CELERY_TIMEZONE
CELERY_ENABLE_UTC
```

---

# 31. CELERY BEAT

For scheduled tasks:

```python
CELERY_BEAT_SCHEDULE = {
    "example-task": {
        "task": "api.tasks.example_task",
        "schedule": 60.0,
    },
}
```

Meaning:

```text
every 60 seconds
```

Other scheduling options can use `timedelta`, crontab schedules, or other Celery scheduling mechanisms.

---

# 32. CELERY RESULT STORAGE

If you need task results:

```python
CELERY_RESULT_BACKEND = "redis://127.0.0.1:6379/0"
```

Then:

```python
result = some_task.delay()
```

You can inspect the task result depending on the task/backend configuration.

If you don't need results, you can design your Celery setup not to retain them.

---

# 33. EMAIL

Django's email configuration starts with:

## `EMAIL_BACKEND`

Development console:

```python
EMAIL_BACKEND = (
    "django.core.mail.backends.console.EmailBackend"
)
```

Emails appear in the terminal.

---

# 34. SMTP EMAIL

For an SMTP provider:

```python
EMAIL_BACKEND = (
    "django.core.mail.backends.smtp.EmailBackend"
)

EMAIL_HOST = "smtp.example.com"

EMAIL_PORT = 587

EMAIL_USE_TLS = True

EMAIL_HOST_USER = "your-email@example.com"

EMAIL_HOST_PASSWORD = "your-password"

DEFAULT_FROM_EMAIL = "your-email@example.com"
```

Important settings:

```text
EMAIL_BACKEND
EMAIL_HOST
EMAIL_PORT
EMAIL_USE_TLS
EMAIL_USE_SSL
EMAIL_HOST_USER
EMAIL_HOST_PASSWORD
DEFAULT_FROM_EMAIL
EMAIL_TIMEOUT
```

Do not commit real passwords to Git.

Use environment variables.

---

# 35. EMAIL + CELERY

This is a very common architecture.

```text
Django request
      ↓
Create order
      ↓
Celery task.delay()
      ↓
Redis broker
      ↓
Celery worker
      ↓
send_mail()
      ↓
SMTP server
```

Example:

```python
send_order_confirmation_email.delay(
    order.id,
    request.user.email,
)
```

Django does not need to wait for the email operation.

---

# 36. SESSION

Important session settings include:

```python
SESSION_ENGINE = (
    "django.contrib.sessions.backends.db"
)
```

Session cookie:

```python
SESSION_COOKIE_NAME = "sessionid"
```

Cookie security:

```python
SESSION_COOKIE_SECURE = True
```

For HTTPS.

Other useful settings:

```text
SESSION_COOKIE_HTTPONLY
SESSION_COOKIE_SAMESITE
SESSION_COOKIE_AGE
SESSION_EXPIRE_AT_BROWSER_CLOSE
SESSION_SAVE_EVERY_REQUEST
```

---

# 37. CSRF

Important settings:

```text
CSRF_COOKIE_NAME
CSRF_COOKIE_SECURE
CSRF_COOKIE_HTTPONLY
CSRF_COOKIE_SAMESITE
CSRF_TRUSTED_ORIGINS
CSRF_USE_SESSIONS
CSRF_FAILURE_VIEW
```

Example:

```python
CSRF_COOKIE_SECURE = True
```

Use secure cookies in HTTPS production environments.

---

# 38. SECURITY SETTINGS

Important production security settings include:

```python
SECURE_SSL_REDIRECT = True

SESSION_COOKIE_SECURE = True

CSRF_COOKIE_SECURE = True

SECURE_HSTS_SECONDS = 31536000

SECURE_HSTS_INCLUDE_SUBDOMAINS = True

SECURE_HSTS_PRELOAD = True

SECURE_CONTENT_TYPE_NOSNIFF = True

SECURE_REFERRER_POLICY = "same-origin"

X_FRAME_OPTIONS = "DENY"
```

These are production/security-related settings and should be configured carefully according to your deployment architecture.

---

# 39. LOGGING

Django logging:

```python
LOGGING = {
    "version": 1,

    "disable_existing_loggers": False,

    "handlers": {
        "console": {
            "class": "logging.StreamHandler",
        },
    },

    "loggers": {
        "django": {
            "handlers": ["console"],
            "level": "INFO",
        },
    },
}
```

Important terms:

```text
LOGGING
handlers
loggers
formatters
filters
level
propagate
```

Common levels:

```text
DEBUG
INFO
WARNING
ERROR
CRITICAL
```

---

# 40. FILE UPLOADS

Important settings:

```python
FILE_UPLOAD_MAX_MEMORY_SIZE = 2621440

DATA_UPLOAD_MAX_MEMORY_SIZE = 2621440
```

These control upload/data size limits.

Your model can use:

```python
image = models.ImageField(
    upload_to="products/"
)
```

and settings:

```python
MEDIA_ROOT = BASE_DIR / "media"

MEDIA_URL = "/media/"
```

---

# 41. PASSWORD RESET / EMAIL

If using Django's password-reset functionality, email configuration must be working.

Important:

```python
DEFAULT_FROM_EMAIL = "noreply@example.com"
```

Djoser can then use Django's email system for things such as:

```text
activation emails
password reset emails
confirmation emails
```

---

# 42. DYNAMIC SETTINGS WITH ENVIRONMENT VARIABLES

For production, don't write:

```python
SECRET_KEY = "real-secret-key"

EMAIL_HOST_PASSWORD = "real-password"

DATABASES = {
    ...
    "PASSWORD": "real-db-password",
}
```

Instead use environment variables.

Common approach:

```python
import os

SECRET_KEY = os.getenv("SECRET_KEY")

DEBUG = os.getenv("DEBUG", "False") == "True"
```

For example:

```text
SECRET_KEY=...
DEBUG=True
DB_PASSWORD=...
EMAIL_HOST_PASSWORD=...
```

---

# 43. THIRD-PARTY PACKAGES AND THEIR MAIN SETTINGS

A useful mental map:

```text
PACKAGE
   ↓
MAIN SETTINGS
```

### Django REST Framework

```text
REST_FRAMEWORK
```

### SimpleJWT

```text
SIMPLE_JWT
```

### Djoser

```text
DJOSER
```

### django-allauth

Various allauth-specific settings plus:

```text
AUTHENTICATION_BACKENDS
SITE_ID
```

### django-cors-headers

```text
CORS_ALLOWED_ORIGINS
CORS_ALLOW_CREDENTIALS
CSRF_TRUSTED_ORIGINS
```

### django-redis

```text
CACHES
```

### Celery

```text
CELERY_BROKER_URL
CELERY_RESULT_BACKEND
CELERY_ACCEPT_CONTENT
CELERY_TASK_SERIALIZER
CELERY_RESULT_SERIALIZER
CELERY_TIMEZONE
CELERY_BEAT_SCHEDULE
```

### drf-spectacular

```text
SPECTACULAR_SETTINGS
```

### Django database

```text
DATABASES
```

### Django authentication

```text
AUTH_USER_MODEL
AUTHENTICATION_BACKENDS
AUTH_PASSWORD_VALIDATORS
```

### Django email

```text
EMAIL_BACKEND
EMAIL_HOST
EMAIL_PORT
EMAIL_USE_TLS
EMAIL_USE_SSL
EMAIL_HOST_USER
EMAIL_HOST_PASSWORD
DEFAULT_FROM_EMAIL
```

---

# 44. THE BIG PICTURE

When you look at a large Django `settings.py`, organize it mentally like this:

```text
settings.py
│
├── BASIC
│   ├── SECRET_KEY
│   ├── DEBUG
│   ├── ALLOWED_HOSTS
│   └── BASE_DIR
│
├── APPS
│   └── INSTALLED_APPS
│
├── MIDDLEWARE
│   └── MIDDLEWARE
│
├── DATABASE
│   └── DATABASES
│
├── USER / AUTH
│   ├── AUTH_USER_MODEL
│   ├── AUTHENTICATION_BACKENDS
│   └── AUTH_PASSWORD_VALIDATORS
│
├── DRF
│   └── REST_FRAMEWORK
│
├── JWT
│   └── SIMPLE_JWT
│
├── DJOSER
│   └── DJOSER
│
├── ALLAUTH
│   ├── AUTHENTICATION_BACKENDS
│   └── SITE_ID
│
├── CORS
│   ├── CORS_ALLOWED_ORIGINS
│   └── CORS_ALLOW_CREDENTIALS
│
├── CSRF
│   └── CSRF_TRUSTED_ORIGINS
│
├── CACHE
│   └── CACHES
│
├── REDIS
│   └── Redis URL
│
├── CELERY
│   ├── CELERY_BROKER_URL
│   ├── CELERY_RESULT_BACKEND
│   └── CELERY_BEAT_SCHEDULE
│
├── EMAIL
│   ├── EMAIL_BACKEND
│   ├── EMAIL_HOST
│   ├── EMAIL_PORT
│   ├── EMAIL_HOST_USER
│   ├── EMAIL_HOST_PASSWORD
│   └── DEFAULT_FROM_EMAIL
│
├── STATIC
│   ├── STATIC_URL
│   └── STATIC_ROOT
│
├── MEDIA
│   ├── MEDIA_URL
│   └── MEDIA_ROOT
│
├── SESSIONS
│   └── SESSION_*
│
├── SECURITY
│   └── SECURE_*
│
└── LOGGING
    └── LOGGING
```

# 45. QUICK MEMORY TABLE

| Purpose             | Main setting                              |
| ------------------- | ----------------------------------------- |
| Secret              | `SECRET_KEY`                              |
| Debug               | `DEBUG`                                   |
| Hosts               | `ALLOWED_HOSTS`                           |
| Apps                | `INSTALLED_APPS`                          |
| Middleware          | `MIDDLEWARE`                              |
| Database            | `DATABASES`                               |
| Custom user         | `AUTH_USER_MODEL`                         |
| Auth backends       | `AUTHENTICATION_BACKENDS`                 |
| Password validation | `AUTH_PASSWORD_VALIDATORS`                |
| DRF                 | `REST_FRAMEWORK`                          |
| JWT                 | `SIMPLE_JWT`                              |
| Djoser              | `DJOSER`                                  |
| Allauth             | `AUTHENTICATION_BACKENDS`, `SITE_ID`      |
| CORS                | `CORS_ALLOWED_ORIGINS`                    |
| CSRF                | `CSRF_TRUSTED_ORIGINS`                    |
| Cache               | `CACHES`                                  |
| Redis               | Redis URL inside `CACHES`/Celery settings |
| Celery broker       | `CELERY_BROKER_URL`                       |
| Celery results      | `CELERY_RESULT_BACKEND`                   |
| Celery schedules    | `CELERY_BEAT_SCHEDULE`                    |
| Email backend       | `EMAIL_BACKEND`                           |
| SMTP host           | `EMAIL_HOST`                              |
| SMTP port           | `EMAIL_PORT`                              |
| SMTP user           | `EMAIL_HOST_USER`                         |
| SMTP password       | `EMAIL_HOST_PASSWORD`                     |
| Sender              | `DEFAULT_FROM_EMAIL`                      |
| Static              | `STATIC_URL`, `STATIC_ROOT`               |
| Media               | `MEDIA_URL`, `MEDIA_ROOT`                 |
| Sessions            | `SESSION_*`                               |
| CSRF                | `CSRF_*`                                  |
| Security            | `SECURE_*`                                |
| Logging             | `LOGGING`                                 |
| API documentation   | `SPECTACULAR_SETTINGS`                    |

# 46. The most important prefixes to memorize

Instead of trying to memorize hundreds of individual settings, learn the **families**:

```text
AUTH_*
    ↓
Authentication

REST_FRAMEWORK
    ↓
Django REST Framework

SIMPLE_JWT
    ↓
JWT

DJOSER
    ↓
Djoser

CORS_*
    ↓
CORS

CSRF_*
    ↓
CSRF

SESSION_*
    ↓
Sessions

EMAIL_*
    ↓
Email

CELERY_*
    ↓
Celery

SECURE_*
    ↓
Security

STATIC_*
    ↓
Static files

MEDIA_*
    ↓
Uploaded files

DATABASES
    ↓
Database

CACHES
    ↓
Cache / Redis

LOGGING
    ↓
Logging

SPECTACULAR_SETTINGS
    ↓
API documentation
```

This is a much easier way to remember Django settings than trying to memorize them as one enormous list.
