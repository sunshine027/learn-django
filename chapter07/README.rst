ManyToOne
---------
1. Model example with field **"models.ForeignKey"**:

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

2. Reverse Access with **reported.article_set** (modellowercase_set), for example:

::

    >>>r = Reporter(first_name='John', last_name='Smith')
    >>>r.save()
    >>>a = Article(id=None, headline="This is a test", pub_date=date(2005, 7, 27), reporter=r)
    >>>a.save()
    >>>r.article_set.all()
    [<Article: This is a test>]
    >>> a.reporter    # direct access just use field name
    <Reporter: John Smith>

3. Query each other with lowcasemodel

::

    >>> Article.objects.filter(reporter__first_name__icontains='john')
    [<Article: This is a test>]
    >>> Reporter.objects.filter(article__headline__contains="test")
    [<Reporter: John Smith>]

4. Use "related_name" and "related_query_name"

   Now we change ForeignKey line to bellow:

::

   reporter = models.ForeignKey(Reporter, related_name="articles", related_query_name="reporter_article")

then reverse access will use from **"repoter.article_set"** to **"reporter.articles"**, the value of "related_name" is used, for example:

::

    >>>r1= Reporter.objects.get(first_name="John")
    >>>r1.articles.all()
    [<Article: This is a test>]

then reverse query will change from **"article"** to **"reporter_article"**, the value of "related_query_name" is used, for example:

::

     >>>Reporter.objects.filter(reporter_article__headline__contains="test")
     [<Reporter: John Smith>]

**Note:**
     If you only define "related_name", not "related_query_name", then "related_query_name" is the value of "related_name" as default.

ManyToMany
----------

1. Model example with field **"models.ManyToManyField"**:

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

2. Reverse access with **"article_set"** (modellowercase_set) like "ForeignKey":

::

   >>>p1 = Publication(title='The Python Journal')
   >>>p1.save()
   >>>a1 = Article(headline='Django lets you build Web apps easily')
   >>>a1.save()
   >>>a1.publications.add(p1)
   >>>p1.article_set.all()
   [<Artic: Django lets you build Web apps easily>]

3. query each other and "related_name", "related_query_name" is the same with "ManyToOne", for example:

::

   >>> Publication.objects.filter(article__headline__icontains="django")
   [<Publication: The Python Journal>]
   >>> Article.objects.filter(publications__title__icontains="python")
   [<Article: Django lets you build Web apps easily>]

OneToOne
--------

1. Model example with field **"models.OneToOneField"**:

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

2. Access each other with **place.restaurant**(modellowercase) or **restaurant.place**, for example:

::

    >>>p1 = Place(name='Demon Dogs', address='944 W. Fullerton')
    >>>p1.save()
    >>>r = Restaurant(place=p1, serves_hot_dogs=True, serves_pizza=False)
    >>>r.save()
    >>> r.place
    <Place: Demon Dogs the place>
    >>> p1.restaurant
    <Restaurant: Demon Dogs the restaurant>

3. query each other and "related_name", "related_query_name" is the same with "ManyToOne"
