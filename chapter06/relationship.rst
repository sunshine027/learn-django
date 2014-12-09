ManyToOne
^^^^^^^^^
1. Keyword: **"models.ForeignKey"**, for example:

::

    from django.db import models

    class Reporter(models.Model):
        first_name = models.CharField(max_length=30)
        last_name = models.CharField(max_length=30)

        def __str__(self):              # __unicode__ on Python 2
            return "%s %s" % (self.first_name, self.last_name)

    class Article(models.Model):
        headline = models.CharField(max_length=100)
        reporter = models.ForeignKey(Reporter)

        def __str__(self):              # __unicode__ on Python 2
            return self.headline

2. Reverse access with **reporter.article_set** (modellowercase_set), direct access just use filed name, for example:

::

    >>>r = Reporter(first_name='John', last_name='Smith')
    >>>r.save()
    >>>a = Article(headline="This is a test", reporter=r)
    >>>a.save()
    >>>r.article_set.all()  # use article_set
    [<Article: This is a test>]
    >>> a.reporter    # just use field name
    <Reporter: John Smith>
    >>>r.article_set.create( headline="headline1") #add article
    >>>a2 = Article.objects.create( headline="headline2")
    >>>r.article_set.add(a2) #add article
    >>>r.article_set = [a2] # reinit article

3. Reverse query with **article** (lowcasemodel), direct query just use fieldname:

::

    >>> Article.objects.filter(reporter__first_name__icontains='john') # use reporter
    [<Article: This is a test>]
    >>> Reporter.objects.filter(article__headline__contains="test")    # use article
    [<Reporter: John Smith>]

ManyToMany
^^^^^^^^^^

1. Keyword: **"models.ManyToManyField"**, for example:

::

    class Publication(models.Model):
        title = models.CharField(max_length=30)

        def __str__(self):              # __unicode__ on Python 2
            return self.title

    class Article(models.Model):
        headline = models.CharField(max_length=100)
        publications = models.ManyToManyField(Publication)

        def __str__(self):              # __unicode__ on Python 2
            return self.headline

2. Reverse access with **"article_set"** (modellowercase_set) like "ForeignKey", direct access just use filed name, for example:

::

   >>>p1 = Publication(title='The Python Journal')
   >>>p1.save()
   >>>a1 = Article(headline='Django lets you build Web apps easily')
   >>>a1.save()
   >>>a1.publications.add(p1)   # just use filed name: publications
   >>>p1.article_set.all()      # use article_set
   [<Artic: Django lets you build Web apps easily>]

3. Reverse query with **article** (lowcasemodel), direct query just use fieldname:

::

   >>> Publication.objects.filter(article__headline__icontains="django") # use article
   [<Publication: The Python Journal>]
   >>> Article.objects.filter(publications__title__icontains="python")   # use publications
   [<Article: Django lets you build Web apps easily>]

4. If you need to associate data with the relationship between two models, use intermediate model with **"through"**:

::

    class Person(models.Model):
        name = models.CharField(max_length=128)

        def __str__(self):              # __unicode__ on Python 2
            return self.name

    class Group(models.Model):
        name = models.CharField(max_length=128)
        members = models.ManyToManyField(Person, through='Membership')

        def __str__(self):              # __unicode__ on Python 2
            return self.name

    class Membership(models.Model):
        person = models.ForeignKey(Person)
        group = models.ForeignKey(Group)
        date_joined = models.DateField()
        invite_reason = models.CharField(max_length=64)

5. If there are more than one Foreign key to the source model in intermediate model, you must use **"through_fields"**:

::

    class Person(models.Model):
        name = models.CharField(max_length=128)

        def __str__(self):              # __unicode__ on Python 2
            return self.name

    class Group(models.Model):
        name = models.CharField(max_length=128)
        members = models.ManyToManyField(Person, through='Membership', through_fields=('group', 'person'))

        def __str__(self):              # __unicode__ on Python 2
            return self.name

    class Membership(models.Model):
        person = models.ForeignKey(Person)
        inviter = models.ForeignKey(Person, related_name="membership_invites")
        group = models.ForeignKey(Group)
        date_joined = models.DateField()
        invite_reason = models.CharField(max_length=64)

Note:
    if you use "intermediate model", you can't use group.memebers.add(), group.memebers.create(), group.members=[], because you need to specify all the detail for the
    relationship required by the Membership model.

OneToOne
^^^^^^^^

1. Keyworkd: **"models.OneToOneField"**, for example:

::

    from django.db import models

    class Place(models.Model):
        name = models.CharField(max_length=50)
        address = models.CharField(max_length=80)

        def __str__(self):              # __unicode__ on Python 2
            return "%s the place" % self.name

    class Restaurant(models.Model):
        place = models.OneToOneField(Place, primary_key=True)
        serves_hot_dogs = models.BooleanField(default=False)
        serves_pizza = models.BooleanField(default=False)

2. Reverse access with **place.restaurant** (modellowercase), direct access just use field name like **restaurant.place**, for example:

::

    >>>p1 = Place(name='Demon Dogs', address='944 W. Fullerton')
    >>>p1.save()
    >>>r = Restaurant(place=p1, serves_hot_dogs=True, serves_pizza=False)
    >>>r.save()
    >>> r.place   # use place
    <Place: Demon Dogs the place>
    >>> p1.restaurant # use resaurant
    <Restaurant: Demon Dogs the restaurant>

3. Reverse query with **resaurant** (lowcasemodel), direct query just use field name, for example:

::

    >>>Place.objects.filter(restaurant__serves_hot_dogs=True)    # use resaurant
    >>>Restaurant.objects.filter(place__name__icontains="hello") # use place

Common Usage for ForeignKey, OneToOneField, ManyToManyFiled:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
1. If you need to create a relationship on a model that has not yet been defined, use the name of the model, rather than the model object itself:

::

    from django.db import models

    class Restaurant(models.Model):
        place = models.OneToOneField('Place', primary_key=True) # with quote

    class Place(models.Model):
        name = models.CharField(max_length=50)
        address = models.CharField(max_length=80)

        def __str__(self):              # __unicode__ on Python 2
            return "%s the place" % self.name

2. To create a recursive relationship: an object that has relationship with itself, use **"self"**, for example:

::

    class Person(models.Model):
        friends = models.ManyToManyField("self")

3. Use "related_name" and "related_query_name", for example, if we change ForeignKey to:

::

    from django.db import models

    class Reporter(models.Model):
        first_name = models.CharField(max_length=30)
        last_name = models.CharField(max_length=30)

        def __str__(self):              # __unicode__ on Python 2
            return "%s %s" % (self.first_name, self.last_name)

    class Article(models.Model):
        headline = models.CharField(max_length=100)
        #reporter = models.ForeignKey(Reporter)
        reporter = models.ForeignKey(Reporter, related_name="articles", related_query_name="reporter_article")

        def __str__(self):              # __unicode__ on Python 2
            return self.headline


then reverse access will change from **"repoter.article_set"** to **"reporter.articles"**, because the value of "related_name" is used, for example:

::

    >>>r1= Reporter.objects.get(first_name="John")
    >>>r1.articles.all()   #not article_set
    [<Article: This is a test>]

when query, reverse query will change from **"article"** to **"reporter_article"**, because the value of "related_query_name" is used, for example:

::

     >>>Reporter.objects.filter(reporter_article__headline__contains="test")
     [<Reporter: John Smith>]

**Note:**
     If you only define "related_name", not "related_query_name", then "related_query_name" is the value of "related_name" as default.
