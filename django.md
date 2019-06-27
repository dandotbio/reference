# django

First step while you’re editing settings.py, is to set TIME_ZONE to your time zone

## OTHER

remember to validate input from the user in the URL
It’s good practice to implement view functions such that they don’t make any assumptions about their parameters

def hours_ahead(request, offset):
    try:
        offset = int(offset)
    except ValueError:
        raise Http404()

all external services such as log calls or emails should be deferred and wrapped
open-source full-text search

you can mix keyword and positional arguments, as long as all positional arguments are listed before keyword arguments.

## TEMPLATES

you can only call methods that have no required arguments

dot lookup order

Dictionary lookup (e.g., foo["bar"])
Attribute lookup (e.g., foo.bar)
Method call (e.g., foo.bar())
List-index lookup (e.g., foo[2])

template won't execute this function
def delete(self):
    # Delete the account
    delete.alters_data = True

Generally, if a variable doesn’t exist, the template system inserts the value of the engine’s string_if_invalid configuration option, which is an empty string by default

Use of both and and or clauses within the same tag is allowed, with and having higher precedence than or

### FOR LOOPS

{% for athlete in athlete_list reversed %}

variable unpacking
{% for key, value in data.items %}
{{ key }}: {{ value }}
{% endfor %}

loop or if empty
{% for athlete in athlete_list %}
<p>{{ athlete.name }}</p>
{% empty %}
<p>There are no athletes. Only computer programmers.</p>
{% endfor %}

no support for break or continue

Within each {% for %} loop, you get access to a template variable called forloop
forloop.counter is always set to an integer representing the number of times the loop has been entered. This is one-indexed, so the first time through the loop, forloop.counter will be set to 1. Here’s an example:
  {% for item in todo_list %}

  <p>{{ forloop.counter }}: {{ item }}</p>
  {%
  endfor %}
forloop.counter0 is like forloop.counter, except it’s zero-indexed. Its value will be set to 0 the first time through the loop.
forloop.revcounter is always set to an integer representing the number of remaining items in the loop. The first time through the loop, forloop.revcounter will be set to the total number of items in the sequence you’re traversing. The last time through the loop, forloop.revcounter will be set to 1.
forloop.revcounter0 is like forloop.revcounter, except it’s zero-indexed. The first time through the loop, forloop.revcounter0 will be set to the number of elements in the sequence minus 1. The last time through the loop, it will be set to 0.
forloop.first is a Boolean value set to True if this is the first time through the loop. This is convenient for special-casing:
  {% for object in objects %}

  {% if forloop.first %}<li class="first">{% else %}<li>
      {%
      endif %}
      {{ object }}
  </li>
  {% endfor %}
forloop.last is a Boolean value set to True if this is the last time through the loop. A common use for this is to put pipe characters between a list of links:
  {% for link in links %}
  {{ link }}{% if not forloop.last %} | {% endif %}
  {% endfor %}
The above template code might output something like this:

  Link1 | Link2 | Link3 | Link4
Another common use for this is to put a comma between words in a list:

  <p>Favorite places:</p>
      {% for p in places %}{{ p }}{% if not forloop.last %}, {% endif %}
      {% endfor %}
forloop.parentloop is a reference to the forloop object for the parent loop, in case of nested loops. Here’s an example:
  {% for country in countries %}
  <table>
      {% for city in country.city_list %}
      <tr>
          <td>Country #{{ forloop.parentloop.counter }}</td>
          <td>City #{{ forloop.counter }}</td>
          <td>{{ city }}</td>
      </tr>
      {% endfor %}
  </table>
  {% endfor %}

### IFEQUAL

{% ifequal user currentuser %}
<h1>Welcome!</h1>
{% endifequal %}

{% ifequal section 'sitenews' %}
<h1>Site News</h1>
{% endifequal %}

template variables, strings, integers, and decimal numbers are allowed as arguments to {% ifequal %}
Any other types of variables, such as Python dictionaries, lists, or Booleans, can’t be hard-coded in {% ifequal %} - use 'if'

{% ifnotequal %}

### COMMENTS

{# This is a comment #}
{% comment %}
This is a
multi-line comment.
{% endcomment %}

### FILTERS

{{ name|lower }}
{{ my_list|first|upper }}
A filter argument comes after a colon and is always in double quotes
{{ bio|truncatewords:"30" }}

### SETTINGS FILE

DIRS defines a list of directories where the engine should look for template source files, in search order.
APP_DIRS tells whether the engine should look for templates inside installed applications. By convention, when APPS_DIRS is set to True, DjangoTemplates looks for a “templates” subdirectory in each of the INSTALLED_APPS. This allows the template engine to find application templates even if DIRS is empty.

### INCLUDES

Blocks are evaluated before they are included.

### INHERITANCE

base.html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN">

<html lang="en">

<head>
    <title>{% block title %}{% endblock %}</title>
</head>
<body>
    <h1>My helpful timestamp site</h1>
    {% block content %}{% endblock %}

    {% block footer %}
    <hr>
    <p>Thanks for visiting my site.</p>
    {% endblock %}
</body>
</html>

{% block %} tags do is tell the template engine that a child template may override those portions of the template

{% extends "base.html" %}

{% block title %}Future time{% endblock %}

{% block content %}
<p>
    In {{ hour_offset }} hour(s), it will be {{ next_time }}.
</p>
{% endblock %}

any template in the inheritance tree will have access to every one of your template variables from the context

If you need to get the content of the block from the parent template, use {{ block.super }}, which is a “magic” variable providing the rendered text of the parent template. This is useful if you want to add to the contents of a parent block instead of completely overriding it.

In most cases, the argument to {% extends %} will be a string, but it can also be a variable, if you don’t know the name of the parent template until runtime. This lets you do some cool, dynamic stuff.

### CONTEXTS

RequestContext adds a bunch of variables to your template context by default - things like the HttpRequest object or information about the currently logged-in user.

The render() shortcut creates a RequestContext unless it’s passed a different context instance explicitly.

Context processors let you specify a number of variables that get set in each context automatically - without you having to specify the variables in each render() call.

The catch is that you have to use RequestContext instead of Context when you render a template. The most low-level way of using context processors is to create some processors and pass them to RequestContext. Here’s how the above example could be written with context processors:

from django.template import loader, RequestContext

def custom_proc(request):
    # A context processor that provides 'app', 'user' and 'ip_address'.
    return {
        'app': 'My app',
        'user': request.user,
        'ip_address': request.META['REMOTE_ADDR']
    }

def view_1(request):
    # ...
    t = loader.get_template('template1.html')
    c = RequestContext(request,
                       {'message': 'I am view 1.'},
                       processors=[custom_proc])
    return t.render(c)

def view_2(request):
    # ...
    t = loader.get_template('template2.html')
    c = RequestContext(request,
                       {'message': 'I am the second view.'},
                       processors=[custom_proc])
    return t.render(c)

Each view still has the flexibility to introduce any custom template variables it might need. In this example, the message template variable is set differently in each view.

also:
def view_1(request):
    # ...
    return render(request, 'template1.html',
                  {'message': 'I am view 1.'},
                  context_instance=RequestContext(
                  request, processors=[custom_proc]
                  )

The context_processors setting (in your settings.py) designates which context processors should always be applied to RequestContext. This removes the need to specify processors each time you use RequestContext.

auth
django.contrib.auth.context_processors.auth

If this processor is enabled, every RequestContext will contain these variables:

user - An auth.User instance representing the currently logged-in user (or an AnonymousUser instance, if the client isn’t logged in).
perms - An instance of django.contrib.auth.context_processors.PermWrapper, representing the permissions that the currently logged-in user has.
debug
django.template.context_processors.debug

If this processor is enabled, every RequestContext will contain these two variables - but only if your DEBUG setting is set to True and the request’s IP address (request.META['REMOTE_ADDR']) is in the INTERNAL_IPS setting:

debug - True. You can use this in templates to test whether you’re in DEBUG mode.
sql_queries - A list of {'sql': ..., 'time': ...} dictionaries, representing every SQL query that has happened so far during the request and how long it took. The list is in order by query and lazily generated on access.
i18n
django.template.context_processors.i18n

If this processor is enabled, every RequestContext will contain these two variables:

LANGUAGES - The value of the LANGUAGES setting.
LANGUAGE_CODE - request.LANGUAGE_CODE, if it exists. Otherwise, the value of the LANGUAGE_CODE setting.
media
django.template.context_processors.media

If this processor is enabled, every RequestContext will contain a variable MEDIA_URL, providing the value of the MEDIA_URL setting.

static
django.template.context_processors.static

If this processor is enabled, every RequestContext will contain a variable STATIC_URL, providing the value of the STATIC_URL setting.

csrf
django.template.context_processors.csrf

This processor adds a token that is needed by the csrf_token template tag for protection against cross site request forgeries (see Chapter 19).

request
django.template.context_processors.request

If this processor is enabled, every RequestContext will contain a variable request, which is the current HttpRequest.

messages
django.contrib.messages.context_processors.messages

If this processor is enabled, every RequestContext will contain these two variables:

messages - A list of messages (as strings) that have been set via the messages framework.
DEFAULT_MESSAGE_LEVELS - A mapping of the message level names to their numeric value.

write your own
the convention is to save them in a file called context_processors.py within your app or project.

## MODELS

saving a model after changing a field will update all fields again

can filter multiple
Publisher.objects.filter(country="U.S.A.", state_province="CA")

try:
    p = Publisher.objects.get(name='Apress')
except Publisher.DoesNotExist:
    print ("Apress isn't in the database yet.")
else:
    print ("Apress is in the database.")

orderby multiple
Publisher.objects.order_by("state_province", "-address")

class Publisher(models.Model):
    name = models.CharField(max_length=30)
    address = models.CharField(max_length=50)
    city = models.CharField(max_length=60)
    state_province = models.CharField(max_length=30)
    country = models.CharField(max_length=50)
    website = models.URLField()

    def __str__(self):
        return self.name

    class Meta:
        ordering = ['name'] <- default ordering

chaining
Publisher.objects.filter(country="U.S.A.").order_by("-name")

updating
Publisher.objects.filter(id=52).update(name='Apress Publishing')
The SQL translation here is much more efficient and has no chance of race conditions:

When you access a field that’s a ForeignKey, you’ll get the related model object.
Many-to-many values work like foreign-key values, except we deal with QuerySet values instead of model instances.

### MANAGERS

There are two reasons you might want to create a custom manager: to add extra manager methods, and/or to modify the initial QuerySet the manager returns.

Adding extra manager methods is the preferred way to add table-level functionality to your models.

class BookManager(models.Manager):
    def title_count(self, keyword):
        return self.filter(title__icontains=keyword).count()

class Book(models.Model):
    title = models.CharField(max_length=100)
    authors = models.ManyToManyField(Author)
    publisher = models.ForeignKey(Publisher)
    publication_date = models.DateField()
    num_pages = models.IntegerField(blank=True, null=True)
    objects = BookManager()

We’ve created a BookManager class that extends django.db.models.Manager. This has a single method, title_count(), which does the calculation. Note that the method uses self.filter(), where self refers to the manager itself.

We’ve assigned BookManager() to the objects attribute on the model. This has the effect of replacing the “default” manager for the model, which is called objects and is automatically created if you don’t specify a custom manager. We call it objects rather than something else, so as to be consistent with automatically created managers.

For example, the following model has two managers - one that returns all objects, and one that returns only the books by Roald Dahl.

from django.db import models

 First, define the Manager subclass.
class DahlBookManager(models.Manager):
    def get_queryset(self):
        return super(DahlBookManager, self).get_queryset().filter(author='Roald Dahl')

 Then hook it into the Book model explicitly.
class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=50)
    # ...

    objects = models.Manager() # The default manager.
    dahl_objects = DahlBookManager() # The Dahl-specific manager.

def _get_full_name(self):
    # Returns the person's full name."
    return '%s %s' % (self.first_name, self.last_name)
full_name = property(_get_full_name)

A classic use-case for overriding the built-in methods is if you want something to happen whenever you save an object

def save(self, \*args, *\*kwargs):
    do_something()
    super(Blog, self).save(*args, **kwargs) Call the "real" save() method.
    do_something_else()

def save(self, \*args, *\*kwargs):
    if self.name == "Yoko Ono's blog":
        return # Yoko shall never have her own blog!
    else:
        super(Blog, self).save(*args, **kwargs) # Call the "real" save() method.

It’s important to remember to call the superclass method - that’s that super(Blog, self).save(\*args, *\*kwargs) business - to ensure that the object still gets saved into the database. If you forget to call the superclass method, the default behavior won’t happen and the database won’t get touched.

It’s also important that you pass through the arguments that can be passed to the model method - that’s what the \*args, *\*kwargs bit does. Django will, from time to time, extend the capabilities of built-in model methods, adding new arguments. If you use \*args, *\*kwargs in your method definitions, you are guaranteed that your code will automatically support those arguments when they are added.

## ADMIN SITE

verbose presentation name
email = models.EmailField(blank=True, verbose_name ='e-mail')

class AuthorAdmin(admin.ModelAdmin):
    list_display = ('first_name', 'last_name', 'email')
    search_fields = ('first_name', 'last_name')

admin.site.register(Author, AuthorAdmin)

class BookAdmin(admin.ModelAdmin):
    list_display = ('title', 'publisher', 'publication_date')
    list_filter = ('publication_date',)
    date_hierarchy = 'publication_date' <- adds date selections in the top bar
    ordering = ('-publication_date',) <- overrides the class Meta
    fields = ('title', 'authors', 'publisher', publication_date')
        ^ which fields are shown and in what order
    filter_horizontal = ('authors',)
        ^ draggable box. great for Many2Many > 10 items
    raw_id_fields = ('publisher',)
        ^ replaces dropdown with text box for entering the ID of the model
            better than requesting all objects for display

When you create a user, that user has no permissions, and it’s up to you to give the user specific permissions.

## FORMS

You should either use a try/except clause or the get() method to handle the case of undefined keys:

request.META is a Python dictionary containing all available HTTP headers for the given request
good
def ua_display_good2(request):
    ua = request.META.get('HTTP_USER_AGENT', 'unknown')
    return HttpResponse("Your browser is %s" % ua)

def search(request):
    if 'q' in request.GET and request.GET['q']:
        q = request.GET['q']
        books = Book.objects.filter(title__icontains=q)
        return render(request, 'search_results.html',
                      {'books': books, 'query': q})
    else:
        return HttpResponse('Please submit a search term.')

class ContactForm(forms.Form):
    subject = forms.CharField(max_length=100)
    email = forms.EmailField(required=False, label='Your e-mail address')
    message = forms.CharField(widget=forms.Textarea)

Each field type has a default widget, but you can easily override the default, or provide a custom widget of your own. Think of the Field classes as representing validation logic, while widgets represent presentation logic.

This default output is in the format of an HTML <table>, but there are a few other built-in outputs. li, p

The second thing Form objects can do is validate data. To validate data, create a new Form object and pass it a dictionary of data that maps field names to data:

>>> f = ContactForm({'subject': 'Hello', 'email': 'adrian@example.com', 'message': 'Nice site!'})

Once you’ve associated data with a Form instance, you’ve created a “bound” form:

>>> f.is_bound
True

Call the is_valid() method on any bound Form to find out whether its data is valid. We’ve passed a valid value for each field, so the Form in its entirety is valid:

>>> f.is_valid()
True

You can drill down to get field-specific error messages:

>>> f = ContactForm({'subject': 'Hello', 'message': ''})
>>> f['message'].errors
['This field is required.']
>>> f['subject'].errors
[]
>>> f['email'].errors
[]

Each bound Form instance has an errors attribute that gives you a dictionary mapping field names to error-message lists:

>>> f = ContactForm({'subject': 'Hello', 'message': ''})
>>> f.errors
{'message'`: ['This field is required.']}


Finally, for Form instances whose data has been found to be valid, a cleaned_data attribute is available. This is a dictionary of the submitted data, “cleaned up.” Django’s forms framework not only validates data; it cleans it up by converting values to the appropriate Python types:

>>> f = ContactForm({'subject': 'Hello', 'email': 'adrian@example.com',
'message': 'Nice site!'})
>>> f.is_valid() True
>>> f.cleaned_data
{'message': 'Nice site!', 'email': 'adrian@example.com', 'subject':
'Hello'}

Our contact form only deals with strings, which are “cleaned” into string objects - but if we were to use an IntegerField or DateField, the forms framework would ensure that cleaned_data used proper Python integers or datetime.date objects for the given fields.

 views.py

from django.shortcuts import render
from mysite.forms import ContactForm
from django.http import HttpResponseRedirect
from django.core.mail import send_mail

 ...

def contact(request):
    if request.method == 'POST':
        form = ContactForm(request.POST)
        if form.is_valid():
            cd = form.cleaned_data
            send_mail(
                cd['subject'],
                cd['message'],
                cd.get('email', 'noreply@example.com'),
                ['siteowner@example.com'],
            )
            return HttpResponseRedirect('/contact/thanks/')
    else:
        form = ContactForm(
            initial={'subject': 'I love your site!'}
        )
    return render(request, 'contact_form.html', {'form': form})

Now, the subject field will be displayed prepopulated with that kind statement. Note that there is a difference between passing initial data and passing data that binds the form. The biggest difference is that if you’re just passing initial data, then the form will be unbound, which means it won’t have any error messages.

 contact_form.html

<html>
<head>
    <title>Contact us</title>
</head>
<body>
    <h1>Contact us</h1>

    {% if form.errors %}
        <p style="color: red;">
            Please correct the error{{ form.errors|pluralize }} below.
        </p>
    {% endif %}

    <form action="" method="post">
        <table>
            {{ form.as_table }}
        </table>
        {% csrf_token %}
        <input type="submit" value="Submit">
    </form>
</body>
</html>

### CUSTOM VALIDATION RULES

We want additional validation on the message field, so we add a clean_message() method to our Form class:

def clean_message(self):
        message = self.cleaned_data['message']
        num_words = len(message.split())
        if num_words < 4:
            raise forms.ValidationError("Not enough words!")
        return message

Django’s form system automatically looks for any method whose name starts with clean_ and ends with the name of a field. If any such method exists, it’s called during validation. Specifically, the clean_message() method will be called after the default validation logic for a given field (in this case, the validation logic for a required CharField).

Because the field data has already been partially processed, we pull it out of self.cleaned_data. Also, we don’t have to worry about checking that the value exists and is non-empty; that’s done by the default validator. We naively use a combination of len() and split() to count the number of words. If the user has entered too few words, we raise a forms.ValidationError.

The string attached to this exception will be displayed to the user as an item in the error list. It’s important that we explicitly return the cleaned value for the field at the end of the method. This allows us to modify the value (or convert it to a different Python type) within our custom validation method. If we forget the return statement, then None will be returned, and the original value will be lost.

Each field’s widget (<input type="text">, <select>, <textarea>, etc.) can be rendered individually by accessing {{ form.fieldname }} in the template, and any errors associated with a field are available as {{ form.fieldname.errors }}.

With this in mind, we can construct a custom template for our contact form with the following template code:

<html>
<head>
    <title>Contact us</title>
</head>
<body>
    <h1>Contact us</h1>

    {% if form.errors %}
        <p style="color: red;">
            Please correct the error{{ form.errors|pluralize }} below.
        </p>
    {% endif %}

    <form action="" method="post">
        <div class="field">
            {{ form.subject.errors }}
            <label for="id_subject">Subject:</label>
            {{ form.subject }}
        </div>
        <div class="field">
            {{ form.email.errors }}
            <label for="id_email">Your e-mail address:</label>
            {{ form.email }}
        </div>
        <div class="field">
            {{ form.message.errors }}
            <label for="id_message">Message:</label>
            {{ form.message }}
        </div>
        {% csrf_token %}
        <input type="submit" value="Submit">
    </form>
</body>
</html>
{{ form.message.errors }} displays a <ul class="errorlist"> if errors are present and a blank string if the field is valid (or the form is unbound). We can also treat form.message.errors as a Boolean or even iterate over it as a list. For example:

<div class="field{% if form.message.errors %} errors{% endif %}">
    {% if form.message.errors %}
        <ul>
        {% for error in form.message.errors %}
            <li><strong>{{ error }}</strong></li>
        {% endfor %}
        </ul>
    {% endif %}
    <label for="id_message">Message:</label>
    {{ form.message }}
</div>
In the case of validation errors, this will add an “errors” class to the containing <div> and display the list of errors in an unordered list.

## VIEWS

able to set default values in your view def definition if necessary

you can mix keyword and positional arguments, as long as all positional arguments are listed before keyword arguments.

extra_patterns = [
    url(r'^reports/(?P<id>[0-9]+)/$', credit_views.report),
    url(r'^charge/$', credit_views.charge),
]

urlpatterns = [
    url(r'^$', main_views.homepage),
    url(r'^help/', include('apps.help.urls')),
    url(r'^credit/', include(extra_patterns)),
]

urlpatterns = [
    url(r'^(?P<page_slug>\w+)-(?P<page_id>\w+)/history/$',
        views.history),
    url(r'^(?P<page_slug>\w+)-(?P<page_id>\w+)/edit/$',
views.edit),
    url(r'^(?P<page_slug>\w+)-(?P<page_id>\w+)/discuss/$',
        views.discuss),
    url(r'^(?P<page_slug>\w+)-(?P<page_id>\w+)/permissions/$',
        views.permissions),
]
We can improve this by stating the common path prefix only once and grouping the suffixes that differ:

from django.conf.urls import include, url
from . import views

urlpatterns = [
    url(r'^(?P<page_slug>\w+)-(?P<page_id>\w+)/',
        include([
        url(r'^history/$', views.history),
        url(r'^edit/$', views.edit),
        url(r'^discuss/$', views.discuss),
        url(r'^permissions/$', views.permissions),
        ])),
]

An included URLconf receives any captured parameters from parent URLconfs

can include a dictionary in view call
urlpatterns = [
    url(r'^reviews/(?P<year>[0-9]{4})/$',
        views.year_archive,
        {'foo': 'bar'}),
]

urlpatterns = [
    #...
    url(r'^reviews/([0-9]{4})/$', views.year_archive,
        name='reviews-year-archive'),
    #...
]
<a href="{% url 'reviews-year-archive' 2012 %}">2012 Archive</a>

def redirect_to_year(request):
    # ...
    year = 2012
    # ...
    return HttpResponseRedirect(reverse('reviews-year-archive', args=(year,)))

### NAMESPACING

Namespaced URLs are specified using the “ : “ operator. For example, the main index page of the admin application is referenced using “admin:index”. This indicates a namespace of “admin”, and a named URL of “index”.

Namespaces can also be nested. The named URL members:reviews:index would look for a pattern named “index” in the namespace “reviews” that is itself defined within the top-level namespace “members”.

## AUTHENTICATION

from django.contrib.auth.decorators import login_required

@login_required
def my_view(request):
    ...

Note: The login_required decorator does NOT check the is_active flag on a user.

def my_view(request):
    if not request.user.email.endswith('@example.com'):
        return HttpResponse("You can't leave a review for this book.")
    # ...
As a shortcut, you can use the convenient user_passes_test decorator:

from django.contrib.auth.decorators import user_passes_test

def email_check(user):
    return user.email.endswith('@example.com')

@user_passes_test(email_check)
def my_view(request):
    ...

user_passes_test() takes a required argument: a callable that takes a User object and returns True if the user is allowed to view the page. Note that user_passes_test() does not automatically check that the User is not anonymous. user_passes_test() takes two optional arguments:

login_url. Lets you specify the URL that users who don’t pass the test will be redirected to. It may be a login page and defaults to LOGIN_URL if you don’t specify one.
redirect_field_name. Same as for login_required(). Setting it to None removes it from the URL, which you may want to do if you are redirecting users that don’t pass the test to a non-login page where there’s no “next page”.
For example:

@user_passes_test(email_check, login_url='/login/')
def my_view(request):
    ...

# sand

python manage.py makemigrations sand
python manage.py sqlmigrate sand 000X
python manage.py migrate

    q.choice_set.create(choice_text='Not much', votes=0)

you can add directly to the set given by another model's FK

    Choice.objects.filter(question__pub_date__year=current_year)

can follow keys through the chain for filtering
https://docs.djangoproject.com/en/2.0/ref/models/relations/
https://docs.djangoproject.com/en/2.0/topics/db/queries/#field-lookups-intro

Each view is responsible for doing one of two things: returning an HttpResponse object containing the content for the requested page, or raising an exception such as Http404.

https://docs.djangoproject.com/en/2.0/topics/http/shortcuts/#django.shortcuts.get_list_or_404

namespace the urls as in polls/urls.py

https://docs.djangoproject.com/en/2.0/ref/models/expressions/#avoiding-race-conditions-using-f

Fat Models, Utility Modules, Thin Views, Stupid Templates
Twelve-Factor App
Implement PEP-8 code style linting

Standard library imports.
Imports from core Django.
Imports from third-party apps including those unrelated to Django.
Imports from the apps that you created as part of your Django project.

Section 5 setup

Data should be calculated in views and then passed to templates for display.

=========

https://djangobook.com/django-models-basic-data-access/

Updating an object with .save() will update all fields, not just the ones you provided a new value for
to update only one field:
Publisher.objects.filter(id=1).update(name='Apress Publishing')
Publisher.objects.all().update(country='USA')

delete
Publisher.objects.filter(country='USA').delete()
Publisher.objects.all().delete()
p.delete()

==========

def ua_display_good2(request):
    ua = request.META.get('HTTP_USER_AGENT', 'unknown')
    return HttpResponse("Your browser is %s" % ua)

never trust user input, even headers!



Page 90 using Q model for multiple filters on query

model related managers should be used for table-wide queries
only functions that work on the individual model should live in the model

can filter with multiple attributes by passing in .filter('attr', 'attr2')
.order_by('attr', 'attr2')
reverse order: '-attr'

to specify default order:
    class Meta:
        ordering = ['name']

======

model definitions:

https://medium.com/@SteelKiwiDev/best-practices-working-with-django-models-in-python-b17d98ab92b

constants (for choices and other)
fields of the model
custom manager indication
meta
def __unicode__ (python 2) or def __str__ (python 3)
other special methods
def clean
def save
def get_absolute_url
other methods

=======

best practices

Using ModelName.DoesNotExist instead of ObjectDoesNotExist
http://www.effectivedjango.com/orm.html#instantiation-is-expensive

https://agiliq.com/books/djangodesignpatterns/

TODO

Meta abstract class on the parent class to avoid extra table
unique_together
forms to add and edit information on the business/project


from django.utils import timezone

models datefield (timezone.now) for timestamping now at this timezone

filter in__=[] can we pass in objects to search within?


django.views.decorators.http import require_POST
    throws 405 if view not hit by POST request

a href="{% url 'something' todo.id %}"

getting posted data
form.cleaned_data['something']

class Som(forms.ModelForm):
    class Meta:
        model = SomeModel


if newtodoform.is_valid():
    newtodoform.save()
# don't need to manually create and save as the form is based off the model so django knows how to create the object

todo_10 = Todo.objects.get(pk=10)
form = newtodoform(request.POST, instance=todo_10)
# uses the todo instance number 10 as the model to act on

appname/
    templates/
        appname/
        registration/

namespacing url patterns for use in templates so you don't have to hardcode

# urls.py
app_name = 'music'

urlpatterns = [... name='detail']

# template
{% url 'music:detail' album.id %}

get_absolute_url:
    reverse('music:detail', kwargs={'pk': self.pk})

what are generic views?1p

>>> a2 = Article(headline='NASA uses Python')
>>> a2.save()
>>> a2.publications.add(p1, p2)
>>> a2.publications.add(p3)

c1 = 'revenue model'

qgroup = QualifierGroup(category=c1)

qval1 = 'saas'
qval2 = 'annual saas'
qval3 = 'pay as you go'

qgroup.values.add(qval1, qval2)
qgroup.values.add(qval3)

qgroup.values.all()

qvalue.qgroup_set.all()
qcategory.qgroup_set.all()
