# Django Complete Development Guide

## Project Creation, Apps, URLs, Views, Parameters, Templates, Testing & `django-extensions`

---

# 1. What is Django?

Django is a Python web framework.

It provides tools for building:

* websites
* web applications
* REST APIs
* authentication systems
* database applications
* admin dashboards
* forms
* templates
* testing systems

A simplified Django request flow is:

```text
Browser / Postman / REST Client
            ↓
          URL
            ↓
          View
            ↓
       Model / Database
            ↓
        Template
            ↓
        Response
```

For Django REST Framework, the response is often JSON instead of HTML:

```text
Client
  ↓
URL
  ↓
DRF View
  ↓
Serializer
  ↓
Model / Database
  ↓
JSON Response
```

---

# 2. Create a virtual environment

First create your project folder:

```bash
mkdir myproject
cd myproject
```

Create a virtual environment:

```bash
python -m venv venv
```

Activate it on Windows Git Bash:

```bash
source venv/Scripts/activate
```

You should see:

```text
(venv)
```

in your terminal.

---

# 3. Install Django

```bash
pip install django
```

Check the installation:

```bash
python -m django --version
```

You can also use:

```bash
django-admin --version
```

---

# 4. Create a Django project

Run:

```bash
django-admin startproject config .
```

The `.` means:

> Create the project in the current directory.

You will get:

```text
myproject/
│
├── manage.py
│
└── config/
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    ├── asgi.py
    └── wsgi.py
```

---

# 5. `manage.py`

`manage.py` is the command-line entry point for your Django project.

You use it for commands such as:

```bash
python manage.py runserver
python manage.py startapp products
python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser
python manage.py test
```

Think:

```text
manage.py
   ↓
Django project management commands
```

---

# 6. `settings.py`

This contains your project configuration.

Important settings include:

```python
INSTALLED_APPS
MIDDLEWARE
ROOT_URLCONF
TEMPLATES
DATABASES
AUTH_PASSWORD_VALIDATORS
STATIC_URL
MEDIA_URL
```

---

# 7. Create an application

A Django project can contain multiple apps.

Create an app:

```bash
python manage.py startapp products
```

Structure:

```text
myproject/
│
├── manage.py
│
├── config/
│
└── products/
    ├── migrations/
    ├── __init__.py
    ├── admin.py
    ├── apps.py
    ├── models.py
    ├── tests.py
    └── views.py
```

A useful mental model:

```text
Project
│
├── config
│
├── products
├── users
├── orders
└── payments
```

The **project** contains configuration.

The **apps** contain application functionality.

---

# 8. Add the app to `INSTALLED_APPS`

In `settings.py`:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    'products',
]
```

---

# 9. Create your first view

In:

```text
products/views.py
```

write:

```python
from django.http import HttpResponse


def home(request):
    return HttpResponse("Hello Django")
```

A view receives a request:

```python
def home(request):
```

and returns a response:

```python
return HttpResponse("Hello Django")
```

---

# 10. Create URLs

In:

```text
products/urls.py
```

create:

```python
from django.urls import path
from .views import home

urlpatterns = [
    path('', home, name='home'),
]
```

Then connect the app URLs to the project's URLs.

`config/urls.py`:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('products.urls')),
]
```

Now:

```text
http://127.0.0.1:8000/
```

calls:

```text
products.urls
      ↓
path('')
      ↓
home()
```

---

# 11. Start the server

```bash
python manage.py runserver
```

Open:

```text
http://127.0.0.1:8000/
```

---

# 12. Understanding `path()`

Basic:

```python
path('', home)
```

means:

```text
/
```

Another:

```python
path('products/', products)
```

means:

```text
/products/
```

Another:

```python
path('about/', about)
```

means:

```text
/about/
```

---

# 13. Static URL parameters

Suppose you want:

```text
/products/10/
```

where `10` is the product ID.

Use:

```python
path(
    'products/<int:id>/',
    product_detail
)
```

Your view:

```python
def product_detail(request, id):
    return HttpResponse(f"Product ID: {id}")
```

Request:

```text
/products/10/
```

Django calls:

```python
product_detail(request, id=10)
```

---

# 14. `<int:id>`

This:

```python
<int:id>
```

means:

> Capture a number from the URL and put it into the variable `id`.

Examples:

```text
/products/1/
/products/10/
/products/500/
```

The view receives an integer.

```python
def product_detail(request, id):
    print(type(id))
```

will show:

```text
<class 'int'>
```

---

# 15. Other path converters

Django provides several built-in converters.

## `str`

```python
path('products/<str:name>/', product)
```

Matches:

```text
/products/shoes/
/products/television/
```

---

## `int`

```python
path('products/<int:id>/', product)
```

Matches:

```text
/products/10/
```

---

## `slug`

```python
path('products/<slug:slug>/', product)
```

Matches:

```text
/products/tennis-shoes/
/products/iphone-15/
```

A slug normally contains letters, numbers, hyphens and underscores.

---

## `uuid`

```python
path('products/<uuid:id>/', product)
```

Matches UUID values.

---

## `path`

```python
path('files/<path:file_path>/', file_view)
```

Can capture paths containing `/`.

Example:

```text
/files/images/products/shoe.jpg/
```

---

# 16. Dynamic URL parameters

Example:

```python
path(
    'products/<int:id>/',
    product_detail,
    name='product-detail'
)
```

View:

```python
def product_detail(request, id):
    return HttpResponse(
        f"Product {id}"
    )
```

URL:

```text
/products/25/
```

Result:

```text
Product 25
```

---

# 17. Multiple URL parameters

You can have multiple parameters:

```python
path(
    'products/<int:id>/<slug:slug>/',
    product_detail
)
```

View:

```python
def product_detail(request, id, slug):
    return HttpResponse(
        f"ID: {id}, Slug: {slug}"
    )
```

URL:

```text
/products/10/tennis-shoes/
```

Django sends:

```python
id=10
slug='tennis-shoes'
```

---

# 18. URL parameter names must match the view

If you have:

```python
path(
    'products/<int:id>/',
    product_detail
)
```

your view should accept:

```python
def product_detail(request, id):
```

Not:

```python
def product_detail(request, product_id):
```

unless your URL is:

```python
path(
    'products/<int:product_id>/',
    product_detail
)
```

The names must correspond.

---

# 19. Query parameters

This is different from path parameters.

URL:

```text
/products/?category=shoes
```

The `category=shoes` part is a query parameter.

Access it using:

```python
request.GET.get('category')
```

Example:

```python
def products(request):

    category = request.GET.get('category')

    return HttpResponse(
        f"Category: {category}"
    )
```

Request:

```text
/products/?category=shoes
```

Result:

```text
Category: shoes
```

---

# 20. Multiple query parameters

URL:

```text
/products/?category=shoes&min_price=10000&max_price=50000
```

View:

```python
def products(request):

    category = request.GET.get('category')
    min_price = request.GET.get('min_price')
    max_price = request.GET.get('max_price')

    return HttpResponse(
        f"{category} {min_price} {max_price}"
    )
```

Remember:

```text
?  → starts query parameters

&  → separates query parameters
```

---

# 21. `request.GET`

`request.GET` contains query parameters.

Example:

```python
request.GET
```

could conceptually contain:

```python
{
    'category': 'shoes',
    'min_price': '10000'
}
```

Get one:

```python
request.GET.get('category')
```

Get all values:

```python
request.GET
```

---

# 22. Path parameter vs query parameter

### Path parameter

```text
/products/10/
```

```python
path(
    'products/<int:id>/',
    product_detail
)
```

Access:

```python
id
```

### Query parameter

```text
/products/?id=10
```

Access:

```python
request.GET.get('id')
```

Mental model:

```text
/products/10/
          ↑
      path parameter

/products/?id=10
           ↑
      query parameter
```

---

# 23. Request methods

A view can inspect:

```python
request.method
```

Possible values:

```text
GET
POST
PUT
PATCH
DELETE
```

Example:

```python
def product(request):

    if request.method == 'GET':
        return HttpResponse('GET request')

    if request.method == 'POST':
        return HttpResponse('POST request')
```

---

# 24. GET view

```python
def products(request):
    products = Product.objects.all()

    return render(
        request,
        'products.html',
        {
            'products': products
        }
    )
```

GET is normally used to retrieve information.

---

# 25. POST view

Example:

```python
def create_product(request):

    if request.method == 'POST':

        name = request.POST.get('name')
        price = request.POST.get('price')

        # create product

        return HttpResponse('Product created')

    return render(
        request,
        'create_product.html'
    )
```

---

# 26. `request.POST`

For a normal HTML form:

```html
<form method="POST">
    {% csrf_token %}

    <input name="name">
    <input name="price">

    <button type="submit">
        Create
    </button>
</form>
```

Django accesses submitted values using:

```python
request.POST.get('name')
```

and:

```python
request.POST.get('price')
```

---

# 27. Context in Django templates

A context is data you send from the view to the template.

Example:

```python
def home(request):

    context = {
        'name': 'Charles',
        'age': 25,
    }

    return render(
        request,
        'home.html',
        context
    )
```

Template:

```django
<h1>Hello {{ name }}</h1>

<p>Age: {{ age }}</p>
```

The context:

```python
{
    'name': 'Charles',
    'age': 25
}
```

becomes available in the template.

---

# 28. Context can contain objects

```python
def product_detail(request):

    product = Product.objects.get(id=1)

    return render(
        request,
        'product.html',
        {
            'product': product
        }
    )
```

Template:

```django
<h1>{{ product.name }}</h1>

<p>{{ product.price }}</p>

<p>{{ product.stock }}</p>
```

---

# 29. Context can contain lists

View:

```python
def products(request):

    products = Product.objects.all()

    return render(
        request,
        'products.html',
        {
            'products': products
        }
    )
```

Template:

```django
{% for product in products %}

    <h2>{{ product.name }}</h2>
    <p>{{ product.price }}</p>

{% endfor %}
```

---

# 30. Django template variables

Basic:

```django
{{ name }}
```

Object attribute:

```django
{{ product.name }}
```

Nested:

```django
{{ product.category.name }}
```

Dictionary:

```django
{{ user_data.username }}
```

---

# 31. Template conditions

```django
{% if product.stock > 0 %}

    <p>Available</p>

{% else %}

    <p>Out of stock</p>

{% endif %}
```

---

# 32. Template loops

```django
{% for product in products %}

    <h2>{{ product.name }}</h2>

{% empty %}

    <p>No products found.</p>

{% endfor %}
```

---

# 33. Template inheritance

Create:

```text
templates/
├── base.html
├── home.html
└── products.html
```

`base.html`:

```django
<!DOCTYPE html>
<html>

<head>
    <title>
        {% block title %}
        My Website
        {% endblock %}
    </title>
</head>

<body>

    <nav>
        Home
        Products
    </nav>

    {% block content %}
    {% endblock %}

</body>

</html>
```

Child template:

```django
{% extends 'base.html' %}

{% block title %}
Products
{% endblock %}

{% block content %}

<h1>Products</h1>

{% endblock %}
```

---

# 34. `{% include %}`

Create:

```text
templates/
├── base.html
├── navbar.html
└── home.html
```

`navbar.html`:

```html
<nav>
    <a href="/">Home</a>
    <a href="/products/">Products</a>
</nav>
```

Then:

```django
{% include 'navbar.html' %}
```

---

# 35. `{% url %}`

Suppose:

```python
path(
    'products/',
    products,
    name='products'
)
```

Instead of hardcoding:

```html
<a href="/products/">
```

use:

```django
<a href="{% url 'products' %}">
    Products
</a>
```

This is better because if the URL changes later, your template can continue using the URL name.

---

# 36. `{% url %}` with parameters

URL:

```python
path(
    'products/<int:id>/',
    product_detail,
    name='product-detail'
)
```

Template:

```django
<a href="{% url 'product-detail' product.id %}">
    View Product
</a>
```

---

# 37. Static files

At the beginning of the template:

```django
{% load static %}
```

Then:

```django
<link
    rel="stylesheet"
    href="{% static 'css/style.css' %}"
>
```

Image:

```django
<img src="{% static 'images/logo.png' %}">
```

---

# 38. Django forms

Simple HTML form:

```django
<form method="POST">

    {% csrf_token %}

    <input
        type="text"
        name="name"
    >

    <button type="submit">
        Save
    </button>

</form>
```

`{% csrf_token %}` is important for Django POST forms.

---

# 39. Django models

Example:

```python
from django.db import models


class Product(models.Model):

    name = models.CharField(
        max_length=200
    )

    price = models.DecimalField(
        max_digits=10,
        decimal_places=2
    )

    stock = models.IntegerField()

    description = models.TextField(
        blank=True
    )

    def __str__(self):
        return self.name
```

Create migration:

```bash
python manage.py makemigrations
```

Apply migration:

```bash
python manage.py migrate
```

---

# 40. Querying models

All products:

```python
Product.objects.all()
```

One product:

```python
Product.objects.get(id=1)
```

Filter:

```python
Product.objects.filter(
    stock__gt=0
)
```

Multiple conditions:

```python
Product.objects.filter(
    price__gte=10000,
    stock__gt=0
)
```

---

# 41. Django testing

Django provides a testing framework based on Python's `unittest`.

Create tests in:

```text
tests.py
```

or:

```text
tests/
├── __init__.py
├── test_models.py
├── test_views.py
└── test_api.py
```

Run all tests:

```bash
python manage.py test
```

Run tests for an app:

```bash
python manage.py test products
```

Run one test class:

```bash
python manage.py test products.tests.ProductTestCase
```

Run one method:

```bash
python manage.py test products.tests.ProductTestCase.test_product_creation
```

---

# 42. Basic test

```python
from django.test import TestCase


class ProductTestCase(TestCase):

    def test_basic(self):

        self.assertEqual(
            2 + 2,
            4
        )
```

Run:

```bash
python manage.py test
```

---

# 43. Testing a model

```python
from django.test import TestCase
from .models import Product


class ProductTestCase(TestCase):

    def test_product_creation(self):

        product = Product.objects.create(
            name='Laptop',
            price=750.00,
            stock=8,
            description='A powerful laptop'
        )

        self.assertEqual(
            product.name,
            'Laptop'
        )

        self.assertEqual(
            product.stock,
            8
        )
```

---

# 44. `setUp()` in tests

`setUp()` runs before every test method.

```python
class ProductTestCase(TestCase):

    def setUp(self):

        self.product = Product.objects.create(
            name='Laptop',
            price=750.00,
            stock=8
        )

    def test_product_name(self):

        self.assertEqual(
            self.product.name,
            'Laptop'
        )

    def test_product_stock(self):

        self.assertEqual(
            self.product.stock,
            8
        )
```

Flow:

```text
test_product_name
       ↑
    setUp()

test_product_stock
       ↑
    setUp()
```

A fresh test database is used for Django tests.

---

# 45. Testing a Django view

Django gives you a test client:

```python
self.client
```

Example:

```python
response = self.client.get(
    reverse('products')
)
```

Then:

```python
self.assertEqual(
    response.status_code,
    200
)
```

---

# 46. `reverse()`

Instead of writing:

```python
self.client.get('/products/')
```

use:

```python
self.client.get(
    reverse('products')
)
```

If your URL is:

```python
path(
    'products/',
    products,
    name='products'
)
```

then:

```python
reverse('products')
```

returns:

```text
/products/
```

This makes tests less dependent on hardcoded URLs.

---

# 47. Testing dynamic URLs

Suppose:

```python
path(
    'products/<int:id>/',
    product_detail,
    name='product-detail'
)
```

You can use:

```python
reverse(
    'product-detail',
    args=[product.id]
)
```

or:

```python
reverse(
    'product-detail',
    kwargs={
        'id': product.id
    }
)
```

---

# 48. Testing authentication

Django test client provides:

```python
self.client.force_login(user)
```

Example:

```python
user = User.objects.create_user(
    username='charles',
    password='password123'
)

self.client.force_login(user)
```

Now requests made through:

```python
self.client
```

are authenticated as that user.

For DRF JWT APIs, however, you should generally test JWT authentication explicitly rather than relying only on Django's session-based `force_login()`.

---

# 49. Testing JSON responses

If the response is JSON:

```python
response = self.client.get(
    reverse('products')
)
```

You can use:

```python
data = response.json()
```

Then:

```python
self.assertEqual(
    data['name'],
    'Laptop'
)
```

For a list:

```python
self.assertEqual(
    len(data),
    2
)
```

---

# 50. Testing status codes

Common codes:

```python
from rest_framework import status
```

Then:

```python
status.HTTP_200_OK
status.HTTP_201_CREATED
status.HTTP_400_BAD_REQUEST
status.HTTP_401_UNAUTHORIZED
status.HTTP_403_FORBIDDEN
status.HTTP_404_NOT_FOUND
```

Example:

```python
self.assertEqual(
    response.status_code,
    status.HTTP_200_OK
)
```

---

# 51. `django-extensions`

Now we come to the package you specifically asked about.

Install:

```bash
pip install django-extensions
```

Add it to `INSTALLED_APPS`:

```python
INSTALLED_APPS = [

    # Django apps
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    # Third-party
    'django_extensions',

    # Your apps
    'products',
]
```

Notice the difference:

```text
pip package:
django-extensions

INSTALLED_APPS:
django_extensions
```

The hyphen changes to an underscore.

---

# 52. What is `django-extensions`?

`django-extensions` is a third-party package that adds useful Django management commands and development tools.

It does **not replace Django**.

It adds additional functionality on top of Django.

You can think of it as:

```text
Django
   +
django-extensions
   ↓
more development commands/tools
```

---

# 53. See the commands it adds

Run:

```bash
python manage.py help
```

You'll see additional commands provided by installed packages.

You can also try:

```bash
python manage.py shell_plus
```

which is one of the most useful commands from `django-extensions`.

---

# 54. `shell_plus`

Normal Django shell:

```bash
python manage.py shell
```

You have to import your models manually:

```python
from products.models import Product
```

With:

```bash
python manage.py shell_plus
```

`django-extensions` automatically imports many models for you.

So you can often immediately write:

```python
Product.objects.all()
```

instead of:

```python
from products.models import Product

Product.objects.all()
```

This is extremely useful when learning and debugging Django.

---

# 55. `shell_plus --print-sql`

Another useful command:

```bash
python manage.py shell_plus --print-sql
```

Now when you execute:

```python
Product.objects.all()
```

you can see the SQL generated by Django.

Conceptually:

```text
Product.objects.all()
        ↓
Django ORM
        ↓
SQL query
        ↓
Database
```

This is excellent for learning the relationship between Django ORM and SQL.

---

# 56. `runserver_plus`

Instead of:

```bash
python manage.py runserver
```

you can use:

```bash
python manage.py runserver_plus
```

This provides enhanced development-server functionality.

One useful feature is the enhanced debugging experience when an exception occurs.

---

# 57. `show_urls`

One of the most useful commands when you have many URLs:

```bash
python manage.py show_urls
```

It displays your project's URL patterns.

Conceptually:

```text
/admin/
products/
products/<int:id>/
login/
api/products/
api/token/
```

This is useful because you don't have to search through every `urls.py` file just to see what routes exist.

---

# 58. Filter `show_urls`

You can pipe the result through tools such as `findstr` on Windows.

Example:

```bash
python manage.py show_urls | findstr product
```

This can help you find URLs related to products.

---

# 59. `graph_models`

`django-extensions` can generate a diagram of your model relationships.

For example:

```bash
python manage.py graph_models
```

In practice, diagram generation can require additional Graphviz-related setup depending on the output format.

The idea is:

```text
User
 │
 ├── Order
 │      │
 │      └── OrderItem
 │
 └── Profile
```

This becomes particularly useful in larger projects.

---

# 60. `admin_generator`

`django-extensions` can help generate Django admin configuration.

For example, commands/features around admin generation can reduce repetitive admin code.

You should still understand normal Django admin configuration:

```python
from django.contrib import admin
from .models import Product


@admin.register(Product)
class ProductAdmin(admin.ModelAdmin):
    list_display = [
        'name',
        'price',
        'stock',
    ]
```

The extension is a productivity tool, not a replacement for understanding the Django admin system.

---

# 61. `update_permissions`

`django-extensions` also provides commands for working with permissions in certain Django setups.

This can be useful when working with Django's:

```text
User
Group
Permission
```

system.

---

# 62. `clean_pyc`

Development projects can accumulate Python bytecode files.

`django-extensions` provides:

```bash
python manage.py clean_pyc
```

to help clean `.pyc` files.

---

# 63. `clear_cache`

Depending on your configured cache backend, `django-extensions` provides:

```bash
python manage.py clear_cache
```

This is useful when debugging cache-related behavior.

---

# 64. `pipchecker`

Another command provided by `django-extensions` is:

```bash
python manage.py pipchecker
```

It can help identify installed packages that may have newer versions available.

---

# 65. `notes`

You can use:

```bash
python manage.py notes
```

to help find developer notes such as:

```python
# TODO
# FIXME
# NOTE
```

in your project.

This can be useful when you have forgotten where unfinished work exists.

---

# 66. `validate_templates`

For projects using Django templates, `django-extensions` provides template validation functionality.

The exact available command/options can depend on the installed version, so check:

```bash
python manage.py help
```

or:

```bash
python manage.py help <command>
```

for the version installed in your environment.

---

# 67. Get help for any command

This is very important.

Don't try to memorize every `django-extensions` command.

Use:

```bash
python manage.py help
```

Then:

```bash
python manage.py help shell_plus
```

or:

```bash
python manage.py help show_urls
```

or:

```bash
python manage.py help runserver_plus
```

This shows the options supported by the version you actually installed.

---

# 68. Most useful `django-extensions` commands

For everyday development, these are particularly useful:

```bash
python manage.py shell_plus
```

```bash
python manage.py shell_plus --print-sql
```

```bash
python manage.py show_urls
```

```bash
python manage.py runserver_plus
```

```bash
python manage.py notes
```

```bash
python manage.py clean_pyc
```

```bash
python manage.py clear_cache
```

```bash
python manage.py graph_models
```

You don't need to use all of them.

---

# 69. Django shell vs shell_plus

### Normal Django

```bash
python manage.py shell
```

Then:

```python
from products.models import Product

Product.objects.all()
```

### With django-extensions

```bash
python manage.py shell_plus
```

Then commonly:

```python
Product.objects.all()
```

So:

```text
shell
 ↓
manual imports

shell_plus
 ↓
automatic model imports
```

---

# 70. `shell_plus` is especially useful for ORM learning

For example:

```bash
python manage.py shell_plus --print-sql
```

Then:

```python
Product.objects.filter(
    price__gte=50000
)
```

You can observe the SQL generated by Django.

Try:

```python
Product.objects.count()
```

```python
Product.objects.order_by('-price')
```

```python
Product.objects.filter(
    stock__gt=0
)
```

```python
Product.objects.aggregate(
    max_price=Max('price')
)
```

This is a great way to learn Django ORM.

---

# 71. URL → View → Context → Template

A complete traditional Django request can look like this.

URL:

```python
path(
    'products/',
    products,
    name='products'
)
```

View:

```python
def products(request):

    products = Product.objects.all()

    return render(
        request,
        'products.html',
        {
            'products': products
        }
    )
```

Template:

```django
{% extends 'base.html' %}

{% block content %}

<h1>Products</h1>

{% for product in products %}

    <article>
        <h2>{{ product.name }}</h2>
        <p>{{ product.price }}</p>
        <p>{{ product.stock }}</p>
    </article>

{% empty %}

    <p>No products.</p>

{% endfor %}

{% endblock %}
```

The complete flow:

```text
GET /products/
       ↓
urls.py
       ↓
products()
       ↓
Product.objects.all()
       ↓
context
       ↓
products.html
       ↓
HTML response
```

---

# 72. Dynamic URL → View → Database

URL:

```python
path(
    'products/<int:id>/',
    product_detail,
    name='product-detail'
)
```

View:

```python
from django.shortcuts import get_object_or_404


def product_detail(request, id):

    product = get_object_or_404(
        Product,
        id=id
    )

    return render(
        request,
        'product_detail.html',
        {
            'product': product
        }
    )
```

Template:

```django
<h1>{{ product.name }}</h1>

<p>{{ product.price }}</p>

<p>{{ product.description }}</p>
```

Flow:

```text
/products/10/
       ↓
id = 10
       ↓
product_detail(request, 10)
       ↓
Product.objects.get(id=10)
       ↓
product
       ↓
context
       ↓
template
```

---

# 73. `get_object_or_404`

Instead of:

```python
product = Product.objects.get(id=id)
```

you can use:

```python
product = get_object_or_404(
    Product,
    id=id
)
```

If the product exists:

```text
return product
```

If it doesn't:

```text
404 Not Found
```

This is commonly used for detail pages.

---

# 74. `render()`

Instead of manually creating an HTTP response:

```python
return HttpResponse("Hello")
```

you can render an HTML template:

```python
return render(
    request,
    'home.html',
    context
)
```

`render()` essentially combines:

```text
template
+
context
+
request
↓
HttpResponse
```

---

# 75. `redirect()`

After successfully creating or updating something, you might redirect:

```python
from django.shortcuts import redirect
```

Example:

```python
return redirect('products')
```

Instead of returning the same page.

Flow:

```text
POST
 ↓
Create object
 ↓
redirect
 ↓
GET /products/
```

This is commonly used after HTML form submissions.

---

# 76. Function-based views

Example:

```python
def products(request):

    if request.method == 'GET':
        ...

    elif request.method == 'POST':
        ...

    return ...
```

Advantages:

* simple
* easy to understand
* explicit
* excellent for learning Django fundamentals

---

# 77. Class-based views

Django also provides class-based views.

Example:

```python
from django.views import View


class ProductView(View):

    def get(self, request):
        return HttpResponse('GET')

    def post(self, request):
        return HttpResponse('POST')
```

URL:

```python
path(
    'products/',
    ProductView.as_view()
)
```

Notice:

```python
ProductView.as_view()
```

converts the class into a callable view Django can use.

---

# 78. Generic class-based views

Django provides generic views such as:

```text
ListView
DetailView
CreateView
UpdateView
DeleteView
FormView
TemplateView
```

Example:

```python
from django.views.generic import ListView


class ProductListView(ListView):

    model = Product
    template_name = 'products.html'
    context_object_name = 'products'
```

URL:

```python
path(
    'products/',
    ProductListView.as_view(),
    name='products'
)
```

---

# 79. View types mental map

```text
Django Views
│
├── Function-Based Views
│      └── def view(request)
│
├── Class-Based Views
│      └── class View
│
└── Generic Class-Based Views
       ├── ListView
       ├── DetailView
       ├── CreateView
       ├── UpdateView
       └── DeleteView
```

---

# 80. Traditional Django vs DRF

Traditional Django:

```text
Request
 ↓
View
 ↓
Model
 ↓
Template
 ↓
HTML
```

Django REST Framework:

```text
Request
 ↓
API View
 ↓
Serializer
 ↓
Model
 ↓
JSON
```

For example, traditional Django:

```python
return render(
    request,
    'products.html',
    {'products': products}
)
```

DRF:

```python
serializer = ProductSerializer(
    products,
    many=True
)

return Response(
    serializer.data
)
```

---

# 81. Useful command reference

## Project

```bash
django-admin startproject config .
```

## App

```bash
python manage.py startapp products
```

## Development server

```bash
python manage.py runserver
```

## Migrations

```bash
python manage.py makemigrations
python manage.py migrate
```

## Superuser

```bash
python manage.py createsuperuser
```

## Django shell

```bash
python manage.py shell
```

## django-extensions shell

```bash
python manage.py shell_plus
```

## SQL from shell

```bash
python manage.py shell_plus --print-sql
```

## URLs

```bash
python manage.py show_urls
```

## Tests

```bash
python manage.py test
```

## Check project

```bash
python manage.py check
```

---

# 82. The complete Django mental model

Remember this:

```text
                 DJANGO PROJECT
                       │
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
     settings.py    urls.py       manage.py
                       │
                       ↓
                    URL
                       │
                       ↓
                     VIEW
                       │
          ┌────────────┼─────────────┐
          ↓            ↓             ↓
       request       Model        Context
          │            │             │
          │            ↓             │
          │         Database         │
          │                          │
          └──────────────┬───────────┘
                         ↓
                      Template
                         │
                         ↓
                      Response
```

For DRF:

```text
Client
  ↓
URL
  ↓
API View
  ↓
Authentication
  ↓
Permission
  ↓
Serializer
  ↓
Validation
  ↓
Model / Database
  ↓
Serializer
  ↓
JSON Response
```

And `django-extensions` sits alongside Django:

```text
Django
  +
django-extensions
  ↓
extra development commands
```

The most important `django-extensions` tools to remember first are:

```text
shell_plus
show_urls
runserver_plus
graph_models
notes
```

You can always discover the others with:

```bash
python manage.py help
```

and get detailed information about one with:

```bash
python manage.py help shell_plus
```
