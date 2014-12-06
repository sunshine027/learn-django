Writing your first Django app, part 1
=====================================

Creating a project
------------------

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

Database setup
--------------

1. set database:

    Now, edit learningdjango/settings.py. It’s a normal Python module with module-level variables representing Django settings. By default, the configuration uses
    SQLite. If you’re new to databases, or you’re just interested in trying Django, this is the easiest choice. SQLite is included in Python, so you won’t need to
    install anything else to support your database.

2. Create the tables in the database before we can use them. To do that, run the following command:

::

  python manage.py migrate

The development server
----------------------

Let’s verify your Django project works:

::

    $ python manage.py runserver
    Starting development server at http://127.0.0.1:8000/

    $ python manage.py runserver 0.0.0.0:8000

    $ python manage.py runserver xxx.xxx.xxx.xx:8000

you can check that by openning http://localhost:8000 in browser

Start an application
--------------------

::

    $ python manage.py startapp polls

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

You should see something similar to the following:

::

    Migrations for 'polls':
    0001_initial.py:
    - Create model Question
    - Create model Choice
    - Add field question to choice

2.3 Let’s see what SQL that migration would run. The sqlmigrate command takes migration names and returns their SQL:

::

    $ python manage.py sqlmigrate polls 0001

2.4 Now, run migrate again to create those model tables in your database:

::

    $ python manage.py migrate

Playing with the API
--------------------

::

    $ python manage.py shell
    >>> from polls.models import Question, Choice   # Import the model classes we just wrote.
    # No questions are in the system yet.
    >>> Question.objects.all()
    []

    # Create a new Question.
    >>> from django.utils import timezone
    >>> q = Question(question_text="What's new?", pub_date=timezone.now())

    # Save the object into the database. You have to call save() explicitly.
    >>> q.save()

    # Now it has an ID. Note that this might say "1L" instead of "1", depending
    # on which database you're using. That's no biggie; it just means your
    # database backend prefers to return integers as Python long integer
    # objects.
    >>> q.id
    1

    # Access model field values via Python attributes.
    >>> q.question_text
    "What's new?"
    >>> q.pub_date
    datetime.datetime(2012, 2, 26, 13, 0, 0, 775217, tzinfo=<UTC>)

    # Change values by changing the attributes, then calling save().
    >>> q.question_text = "What's up?"
    >>> q.save()

    # objects.all() displays all the questions in the database.
    >>> Question.objects.all()
    [<Question: Question object>]

Get help about manage.py

::

    $ python manage.py help


Project vs. application
-----------------------

    What’s the difference between a project and an app? An app is a Web application that does something – e.g., a Weblog system, a database of public records
    or a simple poll app. A project is a collection of configuration and apps for a particular Web site. A project can contain multiple apps. An app can be
    in multiple projects.
