Installtion
============

Install Dependencies
--------------------

1. install python
   Being a Python Web framework, Django requires Python. It works with Python 2.7, 3.2, 3.3, or 3.4. All these versions of Python include a lightweight database called SQLite so you won’t need to set up a database just yet

2. install database
   This step is only necessary if you’d like to work with a “large” database engine like PostgreSQL, MySQL, or Oracle. To install such a database, consult the `database installation information <https://docs.djangoproject.com/en/1.7/topics/install/#database-installation>`_

Install Django
--------------

1. Remove any old versions of Django
   If you are upgrading your installation of Django from a previous version, you will need to `uninstall the old Django version <https://docs.djangoproject.com/en/1.7/topics/install/#removing-old-versions-of-django>`_ before installing the new version.

2. Install django
   a. Install a version of Django provided by your operating system distribution.

      $sudo apt-get install python-django

   b. Install an official release

      $pip install django

   c. Installing the development version

      $git clone git://github.com/django/django.git django-trunk

      $ sudo python setup.py install or sudo pip install -e django-trunk/
