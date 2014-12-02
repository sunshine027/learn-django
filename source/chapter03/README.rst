Getting Started
===============

Start project
-------------

::

    $ django-admin.py startproject learningdjango

Django convention

-  Files & Directories layout
-  settings
-  manage.py

Run server
----------

::

    $ python manage.py runserver
    Starting development server at http://127.0.0.1:8000/

    $ python manage.py runserver 0.0.0.0:8000

Open a page
-----------

::

    Open http://localhost:8000 in browser

Manage.py
---------

::

    $ python manage.py shell

    $ python manage.py help

Start an application
--------------------

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

