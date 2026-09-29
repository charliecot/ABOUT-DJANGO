# DJANGO CLASS-BASED VIEWS (CBVs)

## Complete Guide: Basic → Intermediate → Advanced

---

# TABLE OF CONTENTS

1. What is a Django View?
2. Function-Based View vs Class-Based View
3. Why Class-Based Views?
4. The Base `View` Class
5. HTTP Methods in CBVs
6. `request` Object
7. URL Parameters
8. Query Parameters
9. Dynamic URLs
10. `kwargs` and `args`
11. `self`
12. Returning Responses
13. `TemplateView`
14. Context
15. `get_context_data()`
16. `ListView`
17. `DetailView`
18. `CreateView`
19. `UpdateView`
20. `DeleteView`
21. `FormView`
22. `RedirectView`
23. `View`
24. Generic Display Views
25. Generic Editing Views
26. CRUD with CBVs
27. `get_queryset()`
28. `get_object()`
29. Dynamic Querysets
30. Dynamic Templates
31. Dynamic Context
32. Dynamic Forms
33. Dynamic Success URLs
34. `get_form()`
35. `get_form_kwargs()`
36. `form_valid()`
37. `form_invalid()`
38. `dispatch()`
39. Authentication
40. `LoginRequiredMixin`
41. `UserPassesTestMixin`
42. `PermissionRequiredMixin`
43. Mixins
44. Multiple Inheritance
45. Pagination
46. Search and Query Parameters
47. Filtering
48. Messages Framework
49. File Uploads
50. Handling GET and POST
51. Handling JSON
52. Custom APIs with CBV
53. Method Decorators
54. Caching CBVs
55. Transactions
56. Error Handling
57. `404`
58. `403`
59. `HttpResponse`
60. `JsonResponse`
61. `render()`
62. `redirect()`
63. Context Processor vs View Context
64. CBV Method Execution Flow
65. Important Methods Cheat Sheet
66. Complete CRUD Example
67. Advanced Product Example
68. Common Mistakes
69. CBV Decision Guide

---

# 1. WHAT IS A DJANGO VIEW?

A Django view is the Python code responsible for handling a request and producing a response.

The basic flow is:

```
Browser
   ↓
URL
   ↓
View
   ↓
Model / Form / Business Logic
   ↓
Template
   ↓
Response
   ↓
Browser
```

For example:

```
GET /products/
```

could produce:

```
Product list page
```

---

# 2. FUNCTION-BASED VIEW VS CLASS-BASED VIEW

## Function-Based View

A simple function:

```python
from django.http import HttpResponse


def home(request):
    return HttpResponse("Hello World")
```

URL:

```python
from django.urls import path
from .views import home

urlpatterns = [
    path('', home, name='home'),
]
```

---

# 3. CLASS-BASED VIEW

The same idea using a class:

```python
from django.http import HttpResponse
from django.views import View


class HomeView(View):

    def get(self, request):
        return HttpResponse("Hello World")
```

URL:

```python
from django.urls import path
from .views import HomeView

urlpatterns = [
    path('', HomeView.as_view(), name='home'),
]
```

Notice:

```python
HomeView.as_view()
```

This is very important.

Django's URL system expects a callable.

`as_view()` converts the class into a callable view function that Django can use.

---

# 4. WHY USE CLASS-BASED VIEWS?

CBVs become useful when your views contain reusable behavior.

For example:

```text
Authentication
Forms
Pagination
CRUD
Templates
Object retrieval
Permissions
Reusable behavior
```

Django provides many ready-made CBVs.

The major groups are:

```text
Base View
    ↓
View

Template Views
    ↓
TemplateView

Display Views
    ↓
ListView
DetailView

Editing Views
    ↓
CreateView
UpdateView
DeleteView

Other
    ↓
FormView
RedirectView
```

---

# 5. THE BASE `View`

The most fundamental Django CBV is:

```python
from django.views import View
```

Example:

```python
from django.http import HttpResponse
from django.views import View


class HomeView(View):

    def get(self, request):
        return HttpResponse("GET request")

    def post(self, request):
        return HttpResponse("POST request")
```

Now:

```text
GET /home/
```

calls:

```python
get()
```

while:

```text
POST /home/
```

calls:

```python
post()
```

---

# 6. HTTP METHODS IN CBVs

Common methods:

```python
get()
post()
put()
patch()
delete()
```

Traditional Django websites mostly use:

```text
GET
POST
```

because HTML forms normally submit using GET or POST.

Example:

```python
class ProductView(View):

    def get(self, request):
        return HttpResponse("Display products")

    def post(self, request):
        return HttpResponse("Create product")
```

---

# 7. WHAT IS `request`?

Every request handler receives:

```python
request
```

Example:

```python
def get(self, request):
```

The request contains information about the client's request.

Important attributes:

```python
request.method
request.GET
request.POST
request.FILES
request.user
request.path
request.path_info
request.headers
request.COOKIES
request.session
```

---

# 8. `request.method`

```python
class MyView(View):

    def get(self, request):

        print(request.method)

        return HttpResponse("GET")
```

Output:

```text
GET
```

For POST:

```python
request.method
```

returns:

```text
POST
```

---

# 9. REQUEST GET PARAMETERS

Suppose the URL is:

```text
/products/?category=phones
```

The value can be accessed using:

```python
request.GET.get("category")
```

Example:

```python
class ProductView(View):

    def get(self, request):

        category = request.GET.get("category")

        return HttpResponse(
            f"Category: {category}"
        )
```

URL:

```text
/products/?category=phones
```

Result:

```text
Category: phones
```

---

# 10. MULTIPLE QUERY PARAMETERS

URL:

```text
/products/?category=phones&page=2&search=samsung
```

Access:

```python
category = request.GET.get("category")
page = request.GET.get("page")
search = request.GET.get("search")
```

Example:

```python
class ProductView(View):

    def get(self, request):

        category = request.GET.get("category")
        page = request.GET.get("page")
        search = request.GET.get("search")

        return HttpResponse(
            f"""
            Category: {category}
            Page: {page}
            Search: {search}
            """
        )
```

---

# 11. PATH PARAMETERS / DYNAMIC URL PARAMETERS

Suppose:

```python
path(
    'products/<int:id>/',
    ProductDetailView.as_view(),
)
```

URL:

```text
/products/10/
```

Django extracts:

```text
id = 10
```

The value is passed to the view.

---

# 12. USING `kwargs`

Example:

```python
class ProductDetailView(View):

    def get(self, request, *args, **kwargs):

        product_id = kwargs["id"]

        return HttpResponse(
            f"Product ID: {product_id}"
        )
```

URL:

```python
path(
    'products/<int:id>/',
    ProductDetailView.as_view(),
)
```

Request:

```text
/products/10/
```

Result:

```text
Product ID: 10
```

---

# 13. `*args` AND `**kwargs`

You will often see:

```python
def get(self, request, *args, **kwargs):
```

### `*args`

Stores positional arguments.

### `**kwargs`

Stores named keyword arguments.

For Django dynamic URL parameters, you will usually use:

```python
kwargs
```

Example:

```python
/products/<int:id>/
```

produces:

```python
kwargs = {
    "id": 10
}
```

---

# 14. USING A BETTER PARAMETER NAME

Instead of:

```python
<int:id>
```

you can use:

```python
<int:pk>
```

Example:

```python
path(
    'products/<int:pk>/',
    ProductDetailView.as_view(),
)
```

Then:

```python
kwargs["pk"]
```

contains the ID.

This is especially useful with Django's generic views because many of them use `pk` by default.

---

# 15. STRING URL PARAMETERS

```python
path(
    'products/<slug:slug>/',
    ProductDetailView.as_view(),
)
```

URL:

```text
/products/iphone-15/
```

Then:

```python
kwargs["slug"]
```

returns:

```text
iphone-15
```

---

# 16. COMMON PATH CONVERTERS

Django provides:

```text
str
int
slug
uuid
path
```

Examples:

```python
<int:id>
```

```python
<str:name>
```

```python
<slug:slug>
```

```python
<uuid:order_id>
```

```python
<path:file_path>
```

---

# 17. `pk` VS `id`

If your model has:

```python
id = models.AutoField(primary_key=True)
```

then:

```text
pk
```

normally refers to:

```text
id
```

But `pk` means:

> Primary Key

It does not necessarily mean the field must literally be called `id`.

For example:

```python
class Order(models.Model):

    order_id = models.UUIDField(
        primary_key=True
    )
```

Then:

```python
order.pk
```

refers to:

```python
order.order_id
```

---

# 18. TEMPLATEVIEW

`TemplateView` is used when you primarily want to render a template.

```python
from django.views.generic import TemplateView


class HomeView(TemplateView):

    template_name = "home.html"
```

URL:

```python
path(
    '',
    HomeView.as_view(),
    name='home'
)
```

---

# 19. TEMPLATEVIEW WITH CONTEXT

You can provide static context:

```python
class HomeView(TemplateView):

    template_name = "home.html"

    extra_context = {
        "title": "Home Page",
        "message": "Welcome"
    }
```

Template:

```html
<h1>{{ title }}</h1>

<p>{{ message }}</p>
```

---

# 20. `get_context_data()`

For dynamic context:

```python
class HomeView(TemplateView):

    template_name = "home.html"

    def get_context_data(self, **kwargs):

        context = super().get_context_data(**kwargs)

        context["title"] = "Home Page"
        context["username"] = "Nzegge"

        return context
```

Template:

```html
<h1>{{ title }}</h1>

<p>Hello {{ username }}</p>
```

---

# 21. WHAT IS CONTEXT?

Context is simply data sent from your view to your template.

Example:

```python
context["name"] = "Charles"
```

Template:

```html
<h1>Hello {{ name }}</h1>
```

Think:

```text
Python
   ↓
context
   ↓
HTML template
```

---

# 22. LISTVIEW

`ListView` is designed to display a list of objects.

Suppose:

```python
class Product(models.Model):

    name = models.CharField(max_length=200)
    price = models.DecimalField(
        max_digits=10,
        decimal_places=2
    )
```

View:

```python
from django.views.generic import ListView
from .models import Product


class ProductListView(ListView):

    model = Product
    template_name = "products.html"
    context_object_name = "products"
```

---

# 23. TEMPLATE FOR LISTVIEW

`products.html`:

```html
<h1>Products</h1>

{% for product in products %}

    <h2>{{ product.name }}</h2>

    <p>{{ product.price }}</p>

{% empty %}

    <p>No products found.</p>

{% endfor %}
```

---

# 24. WHAT DOES LISTVIEW AUTOMATICALLY DO?

If you write:

```python
class ProductListView(ListView):

    model = Product
```

Django automatically:

```text
Query database
     ↓
Product.objects.all()
     ↓
Put objects into context
     ↓
Render template
```

By default, the context name is commonly:

```text
product_list
```

You can customize it:

```python
context_object_name = "products"
```

Then template:

```html
{% for product in products %}
```

---

# 25. DYNAMIC QUERYSET WITH `get_queryset()`

Instead of:

```python
queryset = Product.objects.all()
```

you can write:

```python
class ProductListView(ListView):

    model = Product
    template_name = "products.html"

    def get_queryset(self):

        return Product.objects.filter(
            in_stock=True
        )
```

Now only products in stock are returned.

---

# 26. USING QUERY PARAMETERS WITH LISTVIEW

URL:

```text
/products/?search=phone
```

View:

```python
class ProductListView(ListView):

    model = Product
    template_name = "products.html"
    context_object_name = "products"

    def get_queryset(self):

        queryset = Product.objects.all()

        search = self.request.GET.get("search")

        if search:
            queryset = queryset.filter(
                name__icontains=search
            )

        return queryset
```

This is a very common real-world pattern.

---

# 27. LISTVIEW WITH USER-SPECIFIC DATA

Suppose every order belongs to a user.

```python
class MyOrderListView(ListView):

    model = Order
    template_name = "orders.html"
    context_object_name = "orders"

    def get_queryset(self):

        return Order.objects.filter(
            user=self.request.user
        )
```

Now each authenticated user sees their own orders.

---

# 28. DETAILVIEW

`DetailView` displays one object.

```python
from django.views.generic import DetailView


class ProductDetailView(DetailView):

    model = Product
    template_name = "product_detail.html"
    context_object_name = "product"
```

URL:

```python
path(
    'products/<int:pk>/',
    ProductDetailView.as_view(),
    name='product-detail'
)
```

Request:

```text
/products/5/
```

Django finds:

```python
Product.objects.get(pk=5)
```

---

# 29. DETAILVIEW TEMPLATE

```html
<h1>{{ product.name }}</h1>

<p>Price: {{ product.price }}</p>

<p>{{ product.description }}</p>
```

---

# 30. DETAILVIEW WITH SLUG

URL:

```python
path(
    'products/<slug:slug>/',
    ProductDetailView.as_view(),
    name='product-detail'
)
```

View:

```python
class ProductDetailView(DetailView):

    model = Product
    slug_field = "slug"
    slug_url_kwarg = "slug"
```

Now:

```text
/products/iphone-15/
```

can find:

```python
Product.objects.get(
    slug="iphone-15"
)
```

---

# 31. `get_object()`

You can customize how the object is retrieved.

```python
class ProductDetailView(DetailView):

    model = Product

    def get_object(self):

        product_id = self.kwargs["pk"]

        return Product.objects.get(
            pk=product_id
        )
```

However, normally you can use Django's built-in behavior unless you need special logic.

---

# 32. `get_object_or_404`

A safer custom implementation:

```python
from django.shortcuts import get_object_or_404


class ProductDetailView(DetailView):

    model = Product

    def get_object(self):

        return get_object_or_404(
            Product,
            pk=self.kwargs["pk"]
        )
```

If the object doesn't exist:

```text
404 Not Found
```

---

# 33. CREATEVIEW

`CreateView` is used to create model objects through a form.

Model:

```python
class Product(models.Model):

    name = models.CharField(max_length=200)

    price = models.DecimalField(
        max_digits=10,
        decimal_places=2
    )
```

Form:

```python
from django import forms
from .models import Product


class ProductForm(forms.ModelForm):

    class Meta:
        model = Product
        fields = ["name", "price"]
```

View:

```python
from django.views.generic import CreateView


class ProductCreateView(CreateView):

    model = Product
    form_class = ProductForm
    template_name = "product_form.html"
    success_url = "/products/"
```

---

# 34. CREATEVIEW URL

```python
path(
    'products/create/',
    ProductCreateView.as_view(),
    name='product-create'
)
```

---

# 35. CREATEVIEW TEMPLATE

```html
<h1>Create Product</h1>

<form method="post">

    {% csrf_token %}

    {{ form.as_p }}

    <button type="submit">
        Create
    </button>

</form>
```

---

# 36. CREATEVIEW FLOW

When user visits:

```text
GET /products/create/
```

Django:

```text
CreateView
   ↓
GET
   ↓
Create form
   ↓
Template
```

When user submits:

```text
POST /products/create/
```

Django:

```text
POST
 ↓
Form validation
 ↓
form_valid()
 ↓
form.save()
 ↓
redirect
```

---

# 37. UPDATEVIEW

`UpdateView` modifies an existing object.

```python
from django.views.generic import UpdateView


class ProductUpdateView(UpdateView):

    model = Product
    form_class = ProductForm
    template_name = "product_form.html"
    success_url = "/products/"
```

URL:

```python
path(
    'products/<int:pk>/edit/',
    ProductUpdateView.as_view(),
    name='product-update'
)
```

Request:

```text
/products/5/edit/
```

Django gets:

```python
Product.objects.get(pk=5)
```

Then displays the form populated with the existing values.

---

# 38. DELETEVIEW

```python
from django.views.generic import DeleteView


class ProductDeleteView(DeleteView):

    model = Product
    template_name = "product_confirm_delete.html"
    success_url = "/products/"
```

URL:

```python
path(
    'products/<int:pk>/delete/',
    ProductDeleteView.as_view(),
    name='product-delete'
)
```

Template:

```html
<h1>Delete {{ object.name }}?</h1>

<form method="post">

    {% csrf_token %}

    <button type="submit">
        Yes, delete
    </button>

</form>
```

---

# 39. FORMVIEW

`FormView` is useful when you're working with a form but don't necessarily have a model.

Example:

```python
from django import forms


class ContactForm(forms.Form):

    name = forms.CharField(max_length=100)
    email = forms.EmailField()
    message = forms.CharField(
        widget=forms.Textarea
    )
```

View:

```python
from django.views.generic import FormView
from django.urls import reverse_lazy


class ContactView(FormView):

    template_name = "contact.html"
    form_class = ContactForm
    success_url = reverse_lazy("contact-success")

    def form_valid(self, form):

        print(form.cleaned_data)

        return super().form_valid(form)
```

---

# 40. `form_valid()`

This runs when the submitted form is valid.

```python
def form_valid(self, form):

    print(form.cleaned_data)

    return super().form_valid(form)
```

You can perform additional logic here.

---

# 41. `form_invalid()`

Runs when validation fails.

```python
def form_invalid(self, form):

    print("Form has errors")

    return super().form_invalid(form)
```

---

# 42. `RedirectView`

Used when you want to redirect from one URL to another.

```python
from django.views.generic import RedirectView


class GoogleRedirectView(RedirectView):

    url = "https://www.google.com"
```

URL:

```python
path(
    'google/',
    GoogleRedirectView.as_view()
)
```

Visiting:

```text
/google/
```

redirects to Google.

---

# 43. DYNAMIC SUCCESS URL

Instead of:

```python
success_url = "/products/"
```

you can use:

```python
from django.urls import reverse_lazy

success_url = reverse_lazy("product-list")
```

This is better because it uses the URL name rather than hard-coding the URL.

---

# 44. DYNAMIC SUCCESS URL WITH `get_success_url()`

Sometimes the redirect depends on the object.

```python
class ProductCreateView(CreateView):

    model = Product
    form_class = ProductForm
    template_name = "product_form.html"

    def get_success_url(self):

        return reverse_lazy(
            "product-detail",
            kwargs={
                "pk": self.object.pk
            }
        )
```

After creation:

```text
Create Product
      ↓
Product created
      ↓
self.object
      ↓
redirect to /products/10/
```

---

# 45. IMPORTANT: `self.object`

Generic editing views often use:

```python
self.object
```

For `CreateView`:

```text
self.object
```

is the newly created object.

For `UpdateView`:

```text
self.object
```

is the updated object.

For `DeleteView`:

```text
self.object
```

is the object being deleted before deletion.

---

# 46. `get_queryset()`

This is one of the most important CBV methods.

Example:

```python
class ProductListView(ListView):

    model = Product

    def get_queryset(self):

        return Product.objects.filter(
            in_stock=True
        )
```

Think:

```text
get_queryset()
    =
"What objects should this view work with?"
```

---

# 47. DYNAMIC QUERYSET

You can use the logged-in user:

```python
class OrderListView(ListView):

    model = Order

    def get_queryset(self):

        return Order.objects.filter(
            user=self.request.user
        )
```

Or query parameters:

```python
def get_queryset(self):

    queryset = Product.objects.all()

    category = self.request.GET.get("category")

    if category:
        queryset = queryset.filter(
            category__name=category
        )

    return queryset
```

---

# 48. MULTIPLE FILTERS

```python
def get_queryset(self):

    queryset = Product.objects.all()

    search = self.request.GET.get("search")
    min_price = self.request.GET.get("min_price")
    max_price = self.request.GET.get("max_price")

    if search:
        queryset = queryset.filter(
            name__icontains=search
        )

    if min_price:
        queryset = queryset.filter(
            price__gte=min_price
        )

    if max_price:
        queryset = queryset.filter(
            price__lte=max_price
        )

    return queryset
```

Example:

```text
/products/?search=phone&min_price=100&max_price=500
```

---

# 49. `get_context_data()`

Use this when you need additional data in your template.

Example:

```python
class ProductListView(ListView):

    model = Product
    context_object_name = "products"

    def get_context_data(self, **kwargs):

        context = super().get_context_data(**kwargs)

        context["total_products"] = Product.objects.count()

        return context
```

Template:

```html
<p>
    Total products:
    {{ total_products }}
</p>

{% for product in products %}

    <p>{{ product.name }}</p>

{% endfor %}
```

---

# 50. CONTEXT + URL PARAMETER

Suppose:

```python
path(
    'category/<int:category_id>/',
    ProductListView.as_view()
)
```

View:

```python
class ProductListView(ListView):

    model = Product

    def get_context_data(self, **kwargs):

        context = super().get_context_data(**kwargs)

        category_id = self.kwargs["category_id"]

        context["category_id"] = category_id

        return context
```

---

# 51. DYNAMIC TEMPLATE

You can dynamically select a template:

```python
class ProductView(TemplateView):

    def get_template_names(self):

        if self.request.user.is_staff:
            return ["admin/products.html"]

        return ["products.html"]
```

---

# 52. DYNAMIC SERIALIZED/FORM CLASS

For ordinary Django forms:

```python
class ProductView(CreateView):

    model = Product

    def get_form_class(self):

        if self.request.user.is_staff:
            return AdminProductForm

        return ProductForm
```

---

# 53. `get_form_kwargs()`

This allows you to pass extra information into a form.

Form:

```python
class ProductForm(forms.ModelForm):

    def __init__(self, *args, user=None, **kwargs):

        super().__init__(*args, **kwargs)

        self.user = user

    class Meta:
        model = Product
        fields = ["name", "price"]
```

View:

```python
class ProductCreateView(CreateView):

    model = Product
    form_class = ProductForm

    def get_form_kwargs(self):

        kwargs = super().get_form_kwargs()

        kwargs["user"] = self.request.user

        return kwargs
```

Now the form knows which user submitted it.

---

# 54. `form_valid()` WITH USER

Another common pattern:

```python
class ProductCreateView(CreateView):

    model = Product
    form_class = ProductForm

    def form_valid(self, form):

        form.instance.user = self.request.user

        return super().form_valid(form)
```

This means:

```text
Form submitted
     ↓
form valid
     ↓
set user
     ↓
save
```

---

# 55. OVERRIDING `POST()`

You can override POST manually:

```python
class ProductView(View):

    def post(self, request):

        name = request.POST.get("name")

        return HttpResponse(
            f"Product: {name}"
        )
```

For most model forms, however, `CreateView` is usually cleaner.

---

# 56. FORM DATA

HTML:

```html
<form method="post">

    {% csrf_token %}

    <input
        type="text"
        name="name"
    >

    <button type="submit">
        Submit
    </button>

</form>
```

View:

```python
name = request.POST.get("name")
```

---

# 57. FILE UPLOADS

HTML:

```html
<form
    method="post"
    enctype="multipart/form-data"
>

    {% csrf_token %}

    <input
        type="file"
        name="cv"
    >

    <button type="submit">
        Upload
    </button>

</form>
```

View:

```python
cv = request.FILES.get("cv")
```

---

# 58. REQUEST USER

If authentication is enabled:

```python
request.user
```

returns the current user.

Example:

```python
class ProfileView(TemplateView):

    template_name = "profile.html"

    def get_context_data(self, **kwargs):

        context = super().get_context_data(**kwargs)

        context["user"] = self.request.user

        return context
```

In Django templates, `user` is often already available through the authentication context processor.

---

# 59. LOGIN REQUIRED MIXIN

Protect a CBV:

```python
from django.contrib.auth.mixins import LoginRequiredMixin


class ProfileView(LoginRequiredMixin, TemplateView):

    template_name = "profile.html"
```

If the user isn't authenticated, Django redirects them to the login URL.

---

# 60. `LOGIN_URL`

In settings:

```python
LOGIN_URL = "/login/"
```

You can also define:

```python
class ProfileView(LoginRequiredMixin, TemplateView):

    login_url = "/login/"

    template_name = "profile.html"
```

---

# 61. USERPASSestESTMIXIN

You can define your own access rule:

```python
from django.contrib.auth.mixins import UserPassesTestMixin


class AdminView(UserPassesTestMixin, TemplateView):

    template_name = "admin.html"

    def test_func(self):

        return self.request.user.is_staff
```

Meaning:

```text
Authenticated?
      ↓
test_func()
      ↓
is_staff?
      ↓
Yes → continue
No  → denied
```

---

# 62. PERMISSIONREQUIREDMIXIN

```python
from django.contrib.auth.mixins import PermissionRequiredMixin


class ProductCreateView(
    PermissionRequiredMixin,
    CreateView
):

    model = Product
    form_class = ProductForm

    permission_required = "products.add_product"
```

---

# 63. MIXINS

A mixin is a reusable class containing behavior.

Example:

```python
class StaffRequiredMixin:

    def dispatch(self, request, *args, **kwargs):

        if not request.user.is_staff:
            return HttpResponseForbidden()

        return super().dispatch(
            request,
            *args,
            **kwargs
        )
```

Use:

```python
class ProductView(
    StaffRequiredMixin,
    ListView
):

    model = Product
```

---

# 64. MULTIPLE INHERITANCE

Very common:

```python
class ProductCreateView(
    LoginRequiredMixin,
    PermissionRequiredMixin,
    CreateView
):
    ...
```

The inheritance order matters.

Usually:

```text
Your mixins
      ↓
Generic Django View
```

For example:

```python
class ProductCreateView(
    LoginRequiredMixin,
    CreateView
):
    ...
```

---

# 65. DISPATCH()

`dispatch()` is one of the most important methods in CBVs.

Conceptually:

```text
request
   ↓
dispatch()
   ↓
GET?     → get()
POST?    → post()
PUT?     → put()
DELETE?  → delete()
```

Example:

```python
class MyView(View):

    def dispatch(
        self,
        request,
        *args,
        **kwargs
    ):

        print("Request received")

        return super().dispatch(
            request,
            *args,
            **kwargs
        )
```

---

# 66. USING `dispatch()` FOR COMMON LOGIC

Example:

```python
class StaffOnlyView(View):

    def dispatch(
        self,
        request,
        *args,
        **kwargs
    ):

        if not request.user.is_staff:
            return HttpResponseForbidden(
                "Staff only"
            )

        return super().dispatch(
            request,
            *args,
            **kwargs
        )
```

---

# 67. `setup()`

Django's CBV lifecycle also includes `setup()`.

Conceptually:

```text
as_view()
   ↓
setup()
   ↓
dispatch()
   ↓
get()/post()/...
```

Example:

```python
class MyView(View):

    def setup(
        self,
        request,
        *args,
        **kwargs
    ):

        super().setup(
            request,
            *args,
            **kwargs
        )

        self.my_value = "Hello"
```

Then:

```python
def get(self, request):

    return HttpResponse(
        self.my_value
    )
```

---

# 68. PAGINATION WITH LISTVIEW

Django `ListView` supports pagination.

```python
class ProductListView(ListView):

    model = Product
    template_name = "products.html"
    context_object_name = "products"

    paginate_by = 10
```

Now:

```text
/products/?page=1
/products/?page=2
/products/?page=3
```

---

# 69. PAGINATION TEMPLATE

```html
{% for product in products %}

    <p>{{ product.name }}</p>

{% endfor %}


{% if is_paginated %}

    {% if page_obj.has_previous %}

        <a href="?page={{ page_obj.previous_page_number }}">
            Previous
        </a>

    {% endif %}


    Page {{ page_obj.number }}
    of {{ page_obj.paginator.num_pages }}


    {% if page_obj.has_next %}

        <a href="?page={{ page_obj.next_page_number }}">
            Next
        </a>

    {% endif %}

{% endif %}
```

---

# 70. PRESERVING SEARCH WHEN PAGINATING

Suppose:

```text
/products/?search=phone&page=2
```

You may want links that preserve:

```text
search=phone
```

This can be handled by constructing query strings carefully in your template/view.

---

# 71. MESSAGES FRAMEWORK

After creating something:

```python
from django.contrib import messages


class ProductCreateView(CreateView):

    model = Product
    form_class = ProductForm

    def form_valid(self, form):

        response = super().form_valid(form)

        messages.success(
            self.request,
            "Product created successfully."
        )

        return response
```

Template:

```html
{% if messages %}

    {% for message in messages %}

        <p>{{ message }}</p>

    {% endfor %}

{% endif %}
```

---

# 72. `HTTPRESPONSE`

Simple response:

```python
from django.http import HttpResponse


class HomeView(View):

    def get(self, request):

        return HttpResponse(
            "Hello"
        )
```

---

# 73. `JSONRESPONSE`

You can return JSON:

```python
from django.http import JsonResponse


class ProductView(View):

    def get(self, request):

        return JsonResponse({
            "message": "Success",
            "status": True
        })
```

Response:

```json
{
    "message": "Success",
    "status": true
}
```

---

# 74. `render()`

Function:

```python
return render(
    request,
    "home.html",
    {"name": "Charles"}
)
```

In CBVs, generic views usually handle rendering for you.

You can still use it:

```python
class HomeView(View):

    def get(self, request):

        return render(
            request,
            "home.html",
            {
                "name": "Charles"
            }
        )
```

---

# 75. `redirect()`

```python
from django.shortcuts import redirect


class HomeView(View):

    def post(self, request):

        return redirect(
            "product-list"
        )
```

Better than hard-coding:

```python
return redirect("/products/")
```

because URL names are easier to maintain.

---

# 76. REVERSE VS REDIRECT

`reverse()` creates a URL.

```python
from django.urls import reverse

url = reverse(
    "product-detail",
    kwargs={"pk": 10}
)
```

Result:

```text
/products/10/
```

`redirect()` actually sends the browser to that URL:

```python
return redirect(
    "product-detail",
    pk=10
)
```

---

# 77. `REVERSE_LAZY`

For class attributes:

```python
from django.urls import reverse_lazy


class ProductCreateView(CreateView):

    success_url = reverse_lazy(
        "product-list"
    )
```

Why `reverse_lazy`?

Because the URL may not be loaded yet when the class is imported.

---

# 78. HANDLING 404

Generic views normally handle missing objects for you.

For example:

```python
class ProductDetailView(DetailView):

    model = Product
```

If:

```text
/products/999999/
```

doesn't exist, Django returns:

```text
404 Not Found
```

---

# 79. CUSTOM 404

Using:

```python
from django.shortcuts import get_object_or_404


product = get_object_or_404(
    Product,
    pk=self.kwargs["pk"]
)
```

is preferable to manually doing:

```python
try:
    product = Product.objects.get(...)
except Product.DoesNotExist:
    ...
```

for common object lookup cases.

---

# 80. FORM VALIDATION

With `CreateView`:

```python
def form_valid(self, form):

    print(form.cleaned_data)

    return super().form_valid(form)
```

`cleaned_data` contains validated data.

Example:

```python
form.cleaned_data["name"]
form.cleaned_data["price"]
```

---

# 81. CUSTOM FORM VALIDATION

Forms should generally handle validation.

Example:

```python
class ProductForm(forms.ModelForm):

    def clean_price(self):

        price = self.cleaned_data["price"]

        if price < 0:
            raise forms.ValidationError(
                "Price cannot be negative."
            )

        return price
```

Then your CBV doesn't need to duplicate validation logic.

---

# 82. TRANSACTIONS

If a CBV performs several database operations that should succeed or fail together:

```python
from django.db import transaction


class OrderCreateView(CreateView):

    @transaction.atomic
    def form_valid(self, form):

        response = super().form_valid(form)

        # Additional database operations

        return response
```

Conceptually:

```text
Operation 1
Operation 2
Operation 3

All successful → COMMIT

Something fails → ROLLBACK
```

---

# 83. CACHING A CBV

For a CBV method, Django's cache decorator can be applied using `method_decorator`.

Example:

```python
from django.utils.decorators import method_decorator
from django.views.decorators.cache import cache_page


class ProductListView(ListView):

    model = Product
    template_name = "products.html"

    @method_decorator(
        cache_page(60 * 15)
    )
    def get(self, request, *args, **kwargs):

        return super().get(
            request,
            *args,
            **kwargs
        )
```

This caches the GET response.

POST isn't automatically cached.

---

# 84. CACHING THE ENTIRE CBV

You can also decorate `dispatch()`:

```python
@method_decorator(
    cache_page(60 * 15),
    name="dispatch"
)
class ProductListView(ListView):

    model = Product
    template_name = "products.html"
```

This applies the decorator at the dispatch level.

Be careful when caching views that depend on:

```text
request.user
cookies
session
permissions
personalized data
```

because you don't want one user's response served to another user.

---

# 85. CBV WITH FILE UPLOAD

Example:

```python
class CVUploadView(UpdateView):

    model = User
    form_class = CVUploadForm
    template_name = "upload_cv.html"

    def get_object(self):

        return self.request.user
```

Form:

```python
class CVUploadForm(forms.ModelForm):

    class Meta:
        model = User
        fields = ["cv"]
```

Template:

```html
<form
    method="post"
    enctype="multipart/form-data"
>

    {% csrf_token %}

    {{ form.as_p }}

    <button type="submit">
        Upload CV
    </button>

</form>
```

---

# 86. IMPORTANT DIFFERENCE: DJANGO CBV VS DRF CBV

Django:

```python
from django.views.generic import ListView
```

works mainly with:

```text
HTML
Templates
Forms
Browser requests
```

DRF:

```python
from rest_framework.generics import ListCreateAPIView
```

works mainly with:

```text
JSON
Serializers
APIs
REST
```

So:

```text
Django CBV
      ↓
HTML application


DRF GenericAPIView
      ↓
REST API
```

They are related concepts but are not the same classes.

---

# 87. COMPLETE CRUD EXAMPLE

## Model

```python
class Product(models.Model):

    name = models.CharField(
        max_length=200
    )

    price = models.DecimalField(
        max_digits=10,
        decimal_places=2
    )

    description = models.TextField(
        blank=True
    )

    def __str__(self):

        return self.name
```

---

# 88. FORM

```python
from django import forms
from .models import Product


class ProductForm(forms.ModelForm):

    class Meta:
        model = Product

        fields = [
            "name",
            "price",
            "description"
        ]
```

---

# 89. LIST VIEW

```python
class ProductListView(ListView):

    model = Product
    template_name = "products/product_list.html"
    context_object_name = "products"

    def get_queryset(self):

        return Product.objects.all().order_by(
            "name"
        )
```

---

# 90. DETAIL VIEW

```python
class ProductDetailView(DetailView):

    model = Product
    template_name = "products/product_detail.html"
    context_object_name = "product"
```

---

# 91. CREATE VIEW

```python
class ProductCreateView(CreateView):

    model = Product
    form_class = ProductForm
    template_name = "products/product_form.html"

    success_url = reverse_lazy(
        "product-list"
    )
```

---

# 92. UPDATE VIEW

```python
class ProductUpdateView(UpdateView):

    model = Product
    form_class = ProductForm
    template_name = "products/product_form.html"

    success_url = reverse_lazy(
        "product-list"
    )
```

---

# 93. DELETE VIEW

```python
class ProductDeleteView(DeleteView):

    model = Product

    template_name = (
        "products/product_confirm_delete.html"
    )

    success_url = reverse_lazy(
        "product-list"
    )
```

---

# 94. URLS

```python
from django.urls import path

from .views import (
    ProductListView,
    ProductDetailView,
    ProductCreateView,
    ProductUpdateView,
    ProductDeleteView,
)


urlpatterns = [

    path(
        "products/",
        ProductListView.as_view(),
        name="product-list"
    ),

    path(
        "products/<int:pk>/",
        ProductDetailView.as_view(),
        name="product-detail"
    ),

    path(
        "products/create/",
        ProductCreateView.as_view(),
        name="product-create"
    ),

    path(
        "products/<int:pk>/edit/",
        ProductUpdateView.as_view(),
        name="product-update"
    ),

    path(
        "products/<int:pk>/delete/",
        ProductDeleteView.as_view(),
        name="product-delete"
    ),
]
```

---

# 95. CRUD URL MAP

```text
GET
/products/
       ↓
ProductListView
       ↓
LIST


GET
/products/5/
       ↓
ProductDetailView
       ↓
DETAIL


GET
/products/create/
       ↓
ProductCreateView
       ↓
CREATE FORM

POST
/products/create/
       ↓
ProductCreateView
       ↓
CREATE


GET
/products/5/edit/
       ↓
ProductUpdateView
       ↓
UPDATE FORM

POST
/products/5/edit/
       ↓
ProductUpdateView
       ↓
UPDATE


GET
/products/5/delete/
       ↓
Delete confirmation


POST
/products/5/delete/
       ↓
DELETE
```

---

# 96. IMPORTANT GENERIC VIEW TYPES

## Display

```text
TemplateView
ListView
DetailView
```

## Editing

```text
FormView
CreateView
UpdateView
DeleteView
```

## Other

```text
View
RedirectView
```

---

# 97. WHEN TO USE EACH

## `View`

Use when you need maximum control.

```python
class MyView(View):
    ...
```

---

## `TemplateView`

Use when simply rendering a template.

```python
class AboutView(TemplateView):

    template_name = "about.html"
```

---

## `ListView`

Use when displaying multiple objects.

```python
class ProductListView(ListView):

    model = Product
```

---

## `DetailView`

Use when displaying one object.

```python
class ProductDetailView(DetailView):

    model = Product
```

---

## `CreateView`

Use for creating a model object.

```python
class ProductCreateView(CreateView):
    ...
```

---

## `UpdateView`

Use for editing an existing object.

```python
class ProductUpdateView(UpdateView):
    ...
```

---

## `DeleteView`

Use for deleting an existing object.

```python
class ProductDeleteView(DeleteView):
    ...
```

---

## `FormView`

Use when you have a form but don't necessarily have a model.

```python
class ContactView(FormView):
    ...
```

---

## `RedirectView`

Use for redirects.

```python
class RedirectToProducts(RedirectView):

    pattern_name = "product-list"
```

---

# 98. THE CBV INHERITANCE IDEA

Conceptually:

```text
View
 │
 ├── TemplateView
 │
 ├── RedirectView
 │
 └── Generic Views
       │
       ├── ListView
       ├── DetailView
       │
       └── Editing Views
             ├── FormView
             ├── CreateView
             ├── UpdateView
             └── DeleteView
```

The actual Django inheritance tree is more complex because Django uses multiple mixins.

That is why you will see methods such as:

```python
get_queryset()
get_context_data()
get_object()
get_form()
get_form_kwargs()
form_valid()
form_invalid()
get_success_url()
dispatch()
```

---

# 99. THE MOST IMPORTANT CBV METHODS

## `dispatch()`

Determines which HTTP method handler runs.

```text
GET → get()
POST → post()
```

---

## `get_queryset()`

Determines which objects are available.

```python
def get_queryset(self):
    ...
```

---

## `get_object()`

Determines which single object is being handled.

```python
def get_object(self):
    ...
```

---

## `get_context_data()`

Adds data to templates.

```python
def get_context_data(self, **kwargs):
    ...
```

---

## `get_form_class()`

Determines which form class is used.

```python
def get_form_class(self):
    ...
```

---

## `get_form_kwargs()`

Controls arguments passed to the form.

```python
def get_form_kwargs(self):
    ...
```

---

## `form_valid()`

Runs after successful form validation.

```python
def form_valid(self, form):
    ...
```

---

## `form_invalid()`

Runs after failed form validation.

```python
def form_invalid(self, form):
    ...
```

---

## `get_success_url()`

Determines where the user goes after success.

```python
def get_success_url(self):
    ...
```

---

# 100. A VERY IMPORTANT PATTERN

When overriding a Django CBV method, you will frequently write:

```python
super().method(...)
```

Example:

```python
def get_context_data(self, **kwargs):

    context = super().get_context_data(**kwargs)

    context["message"] = "Hello"

    return context
```

Why?

Because the parent class already performs important work.

You are saying:

```text
"Do Django's normal work first,
then add my custom behavior."
```

---

# 101. BAD VS GOOD `get_context_data()`

Bad:

```python
def get_context_data(self, **kwargs):

    return {
        "message": "Hello"
    }
```

This can throw away context that Django's parent class prepared.

Better:

```python
def get_context_data(self, **kwargs):

    context = super().get_context_data(**kwargs)

    context["message"] = "Hello"

    return context
```

---

# 102. SAME IDEA WITH `get_queryset()`

Usually:

```python
def get_queryset(self):

    queryset = super().get_queryset()

    return queryset.filter(
        in_stock=True
    )
```

This is often better than rebuilding everything manually.

---

# 103. SAME IDEA WITH `form_valid()`

```python
def form_valid(self, form):

    form.instance.user = self.request.user

    return super().form_valid(form)
```

The parent handles the normal save/response behavior.

You simply add:

```python
form.instance.user = self.request.user
```

---

# 104. URL PARAMETER + QUERY PARAMETER TOGETHER

URL:

```python
path(
    "categories/<int:category_id>/products/",
    ProductListView.as_view(),
    name="category-products"
)
```

Request:

```text
/categories/3/products/?search=phone
```

View:

```python
class ProductListView(ListView):

    model = Product

    def get_queryset(self):

        queryset = Product.objects.filter(
            category_id=self.kwargs["category_id"]
        )

        search = self.request.GET.get("search")

        if search:
            queryset = queryset.filter(
                name__icontains=search
            )

        return queryset
```

Here you have TWO different parameter systems:

```text
URL parameter:
self.kwargs["category_id"]

Query parameter:
self.request.GET.get("search")
```

---

# 105. IMPORTANT PARAMETER DIFFERENCE

URL:

```text
/products/10/
```

`10` is a:

```text
PATH PARAMETER
```

Access:

```python
self.kwargs["pk"]
```

URL:

```text
/products/?search=phone
```

`phone` is a:

```text
QUERY PARAMETER
```

Access:

```python
self.request.GET.get("search")
```

Remember:

```text
PATH PARAMETER
        ↓
self.kwargs


QUERY PARAMETER
        ↓
self.request.GET
```

---

# 106. COMPLETE ADVANCED EXAMPLE

Suppose we want:

```text
/products/?search=phone&in_stock=true
```

and only authenticated users can access it.

```python
from django.contrib.auth.mixins import LoginRequiredMixin
from django.views.generic import ListView

from .models import Product


class ProductListView(
    LoginRequiredMixin,
    ListView
):

    model = Product

    template_name = "products.html"

    context_object_name = "products"

    paginate_by = 10

    def get_queryset(self):

        queryset = super().get_queryset()

        search = self.request.GET.get("search")

        in_stock = self.request.GET.get("in_stock")

        if search:
            queryset = queryset.filter(
                name__icontains=search
            )

        if in_stock == "true":
            queryset = queryset.filter(
                in_stock=True
            )

        return queryset.order_by("name")

    def get_context_data(self, **kwargs):

        context = super().get_context_data(
            **kwargs
        )

        context["search"] = self.request.GET.get(
            "search",
            ""
        )

        return context
```

This single view demonstrates:

```text
LoginRequiredMixin
        +
ListView
        +
get_queryset()
        +
query parameters
        +
filtering
        +
pagination
        +
get_context_data()
```

---

# 107. HOW DJANGO PROCESSES THIS VIEW

Request:

```text
GET /products/?search=phone&in_stock=true
```

Flow:

```text
URL
 ↓
ProductListView.as_view()
 ↓
setup()
 ↓
dispatch()
 ↓
GET
 ↓
get_queryset()
 ↓
database query
 ↓
get_context_data()
 ↓
template
 ↓
HTTP response
```

This flow is extremely important for understanding CBVs.

---

# 108. CREATEVIEW FLOW

Request:

```text
GET /products/create/
```

roughly:

```text
dispatch()
 ↓
GET
 ↓
get()
 ↓
get_form()
 ↓
get_form_class()
 ↓
get_form_kwargs()
 ↓
render form
```

POST:

```text
POST
 ↓
post()
 ↓
get_form()
 ↓
is_valid()
 ↓
form_valid()
      OR
form_invalid()
```

Successful:

```text
form_valid()
 ↓
form.save()
 ↓
self.object
 ↓
get_success_url()
 ↓
redirect
```

---

# 109. UPDATEVIEW FLOW

```text
URL
 ↓
pk
 ↓
get_object()
 ↓
existing object
 ↓
form
 ↓
POST
 ↓
validation
 ↓
form_valid()
 ↓
save
 ↓
self.object
 ↓
redirect
```

---

# 110. DELETEVIEW FLOW

```text
URL
 ↓
pk
 ↓
get_object()
 ↓
confirmation page
 ↓
POST
 ↓
delete()
 ↓
success_url
```

---

# 111. COMMON CBV MISTAKES

## Mistake 1 — Forgetting `.as_view()`

Wrong:

```python
path(
    "products/",
    ProductListView,
)
```

Usually:

```python
path(
    "products/",
    ProductListView.as_view(),
)
```

---

# 112. MISTAKE 2 — Forgetting `super()`

Risky:

```python
def get_context_data(self, **kwargs):

    return {
        "name": "Charles"
    }
```

Better:

```python
def get_context_data(self, **kwargs):

    context = super().get_context_data(**kwargs)

    context["name"] = "Charles"

    return context
```

---

# 113. MISTAKE 3 — Using `request.GET` FOR PATH PARAMETERS

For:

```text
/products/10/
```

don't do:

```python
request.GET.get("pk")
```

Use:

```python
self.kwargs["pk"]
```

---

# 114. MISTAKE 4 — USING `kwargs` FOR QUERY PARAMETERS

For:

```text
/products/?search=phone
```

don't do:

```python
self.kwargs["search"]
```

Use:

```python
self.request.GET.get("search")
```

---

# 115. MISTAKE 5 — FORGETTING CSRF

For POST forms:

```html
<form method="post">

    {% csrf_token %}

    ...

</form>
```

Without it, Django's CSRF protection can reject the request.

---

# 116. MISTAKE 6 — HARD-CODING SUCCESS URL

Instead of:

```python
success_url = "/products/"
```

prefer:

```python
success_url = reverse_lazy(
    "product-list"
)
```

---

# 117. MISTAKE 7 — RETURNING THE WRONG THING

A view must return an HTTP response.

Correct:

```python
return HttpResponse("Hello")
```

or:

```python
return render(
    request,
    "home.html"
)
```

or:

```python
return redirect(
    "product-list"
)
```

---

# 118. CBV CHEAT SHEET

```text
View
→ maximum control

TemplateView
→ render template

ListView
→ display many objects

DetailView
→ display one object

CreateView
→ create model object

UpdateView
→ update model object

DeleteView
→ delete model object

FormView
→ process a form

RedirectView
→ redirect
```

---

# 119. PARAMETERS CHEAT SHEET

```text
/products/10/

self.kwargs["pk"]
```

---

```text
/products/?search=phone

self.request.GET.get("search")
```

---

```text
POST form

self.request.POST.get("name")
```

---

```text
uploaded file

self.request.FILES.get("cv")
```

---

```text
logged-in user

self.request.user
```

---

# 120. METHOD CHEAT SHEET

```text
dispatch()
    ↓
chooses HTTP method

get()
    ↓
GET requests

post()
    ↓
POST requests

get_queryset()
    ↓
which objects?

get_object()
    ↓
which single object?

get_context_data()
    ↓
extra template data

get_form_class()
    ↓
which form?

get_form_kwargs()
    ↓
form arguments

form_valid()
    ↓
valid form

form_invalid()
    ↓
invalid form

get_success_url()
    ↓
where to redirect?
```

---

# 121. THE MOST IMPORTANT MENTAL MODEL

When learning Django CBVs, think in these layers:

```text
                 URL
                  │
                  ▼
            Class-Based View
                  │
          ┌───────┴────────┐
          │                │
      URL params       Query params
      self.kwargs      request.GET
          │                │
          └───────┬────────┘
                  ▼
            Request data
                  │
                  ▼
            get_queryset()
            get_object()
            get_form()
                  │
                  ▼
              Context
                  │
                  ▼
             Template
                  │
                  ▼
              Response
```

For editing:

```text
POST
 ↓
Form
 ↓
Validation
 ↓
form_valid()
 ↓
save()
 ↓
self.object
 ↓
success URL
 ↓
redirect
```

---

# 122. CBV VS DRF GENERIC VIEWS

This distinction is especially important when working on both Django websites and your REST API.

Django:

```python
from django.views.generic import ListView
```

Example:

```python
class ProductListView(ListView):

    model = Product
```

Usually produces:

```text
HTML
```

DRF:

```python
from rest_framework.generics import ListCreateAPIView
```

Example:

```python
class ProductListCreateAPIView(
    ListCreateAPIView
):

    queryset = Product.objects.all()
    serializer_class = ProductSerializer
```

Usually produces:

```text
JSON
```

The concepts are similar:

```text
Django ListView
        ↓
List of model objects
        ↓
Template


DRF ListAPIView
        ↓
List of model objects
        ↓
Serializer
        ↓
JSON
```

But the classes and request/response mechanisms are different.

---

# 123. FINAL DECISION GUIDE

Ask yourself:

### Do I just need to display HTML?

Use:

```python
TemplateView
```

### Do I need a list of models?

Use:

```python
ListView
```

### Do I need one model object?

Use:

```python
DetailView
```

### Do I need to create a model?

Use:

```python
CreateView
```

### Do I need to edit a model?

Use:

```python
UpdateView
```

### Do I need to delete a model?

Use:

```python
DeleteView
```

### Do I need a non-model form?

Use:

```python
FormView
```

### Do I need complete control?

Use:

```python
View
```

### Do I need a REST API?

Use Django REST Framework views such as:

```python
APIView
GenericAPIView
ListAPIView
CreateAPIView
ListCreateAPIView
RetrieveUpdateDestroyAPIView
ModelViewSet
```

---

# 124. ONE-PAGE MEMORY MAP

```text
                    DJANGO CBV
                       │
                       ▼
                      View
                       │
       ┌───────────────┼────────────────┐
       │               │                │
       ▼               ▼                ▼
 TemplateView      ListView        DetailView
       │               │                │
       │               │                │
       ▼               ▼                ▼
   HTML page       Many objects      One object


                    EDITING
                       │
       ┌───────────────┼────────────────┐
       │               │                │
       ▼               ▼                ▼
   CreateView      UpdateView       DeleteView
       │               │                │
       ▼               ▼                ▼
    CREATE           UPDATE          DELETE


                   PARAMETERS
                       │
       ┌───────────────┴───────────────┐
       │                               │
       ▼                               ▼
 URL/path parameter               Query parameter
 self.kwargs                      self.request.GET
       │                               │
       ▼                               ▼
 /products/5/                    ?search=phone


                   CONTEXT
                       │
                       ▼
             get_context_data()
                       │
                       ▼
                    Template


                    FORMS
                       │
                       ▼
                  get_form()
                       │
              ┌────────┴────────┐
              ▼                 ▼
        form_valid()      form_invalid()
              │
              ▼
            save()
              │
              ▼
       get_success_url()
              │
              ▼
           redirect()
```

# 125. THE CORE THINGS TO MASTER FIRST

Before moving to advanced CBVs, make sure you understand these:

```text
1. View
2. as_view()
3. get()
4. post()
5. request
6. self
7. self.kwargs
8. request.GET
9. request.POST
10. request.FILES
11. request.user
12. TemplateView
13. ListView
14. DetailView
15. CreateView
16. UpdateView
17. DeleteView
18. get_queryset()
19. get_object()
20. get_context_data()
21. form_valid()
22. form_invalid()
23. get_form_kwargs()
24. get_success_url()
25. dispatch()
26. mixins
27. LoginRequiredMixin
28. reverse_lazy()
29. redirect()
30. caching
```

Once these are clear, Django CBVs become much easier because most advanced CBVs are combinations of the same ideas.
