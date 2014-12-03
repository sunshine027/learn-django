Getting Started
===============

Start project
-------------

::

    $ django-admin.py startproject learningdjango

Let’s look at what startproject created

::

   manage.py
   learningdjango/
        __init__.py
        settings.py
        urls.py
        wsgi.py

Create tables in the database
---------------------------------

::

  python manage.py migrate

Run server
----------

::

    $ python manage.py runserver
    Starting development server at http://127.0.0.1:8000/

    $ python manage.py runserver 0.0.0.0:8000

    $ python manage.py runserver xxx.xxx.xxx.xx:8000

Open a page
-----------

::

    Open http://localhost:8000 in browser

Start an application
--------------------

::

    $ python manage.py startapp poll

That’ll create a directory polls, which is laid out like this:

::

    polls/
        __init__.py
        admin.py
        migrations/
            __init__.py
        models.py
        tests.py
        views.py

Create Models
-------------
1. create two models: Question and Choice in polls/models.py:

::

   from django.db import models

    class Question(models.Model):
        question_text = models.CharField(max_length=200)
        pub_date = models.DateTimeField('date published')

    class Choice(models.Model):
        question = models.ForeignKey(Question)
        choice_text = models.CharField(max_length=200)
        votes = models.IntegerField(default=0)

2. Activating models
   2.1 Edit the mysite/settings.py file again, and change the INSTALLED_APPS setting to include the string 'polls'. So it’ll look like this:

::

    INSTALLED_APPS = (
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'polls',
    )

   2.2 Now Django knows to include the polls app. Let’s run another command:

::

    $ python manage.py makemigrations polls

   2.3 Now, run migrate again to create those model tables in your database:

::

    $ python manage.py migrate

Playing with the API
--------------------

::

    $ python manage.py shell

    $ python manage.py help


Project vs. application

MVC

-  Model: domain
-  View: presentation
-  Controller: interaction

MTV

Django considers the framework (itself) to be the controller

https://docs.djangoproject.com/en/1.6/faq/general/#django-appears-to-be-a-mvc-framework-but-you-call-the-controller-the-view-and-the-view-the-template-how-come-you-don-t-use-the-standard-names

-  Model: fat
-  Template: stupid
-  View: thin

