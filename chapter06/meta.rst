Meta Options
^^^^^^^^^^^^
Adding class Meta to a model is completely optional. If needed, do it like so:

::

   class Person(models.Model):
       name = models.CharField(max_length=64)
       family_name = models.CharField(max_length=64)
    class Meta:
        verbose_name_plural = "people"
        index_together = [["name", "family_name"],]
        unique_together = ("name", "family_name")
        ordering = ['-name', 'family_name']

complete meta options list:

1. abstract: If abstract = True, this model will be an abstract base class
2. app_label: If a model exists outside of the standard locations (models.py or a models package in an app), the model must define which app it is part of:

::

     app_label = 'myapp'

3. db_table: the name of the database table to use for the model, default is app_model
4. db_tablespace: PostgreSQL and Oracle support tablespaces. SQLite and MySQL don’t.
5. get_latest_by: the name of an orderable field in the model, typically a DateField, DateTimeField, or IntegerField. This specifies the default field to use in your model Manager’s latest() and earliest() methods: Entry.objects.latest('pub_date')
6. managed: Defaults to True, meaning Django will create the appropriate database tables in migrate or as part of migrations and remove them as part of a flush management command. That is, Django manages the database tables’ lifecycles.
7. order_with_respect_to: this is almost always used with related objects to allow them to be ordered with respect to a parent object:
   7.1 When order_with_respect_to is set, two additional methods are provided to retrieve and to set the order of the related objects:get_RELATED_order() and set_RELATED_order(), where RELATED is the lowercased model name.
   7.2 The related objects also get two methods, get_next_in_order() and get_previous_in_order()
   7.3 for example:

::

    from django.db import models

    class Question(models.Model):
        text = models.TextField()
    # ...

    class Answer(models.Model):
        question = models.ForeignKey(Question)

        class Meta:
            order_with_respect_to = 'question'

    >>> question = Question.objects.get(id=1)
    >>> question.get_answer_order()
       [1, 2, 3]
    >>> question.set_answer_order([3, 1, 2])
    >>> answer = Answer.objects.get(id=2)
    >>> answer.get_next_in_order()
     <Answer: 3>
    >>> answer.get_previous_in_order()
      <Answer: 1>

8. ordering: the default ordering for the object, for use when obtaining lists of objects: "-" means "descending order", "?"
   means  random, no leading "-" means "ascending", see above
9. permissions:
10. default_permissions: add, change, delete
11. proxy: if proxy = True, a model which subclasses another model will be treated as a proxy model, no extra table will be created for the submodel. The submodel can't add new fields, you can just change things like the default model ordering or the default manager
12. select_on_save: usually there is no need to set this attribute. The default is False.
13. unique_together: sets of field names that, taken together, must be unique, a ManyToManyField cannot be included in unique_together. see above.
14. index_together: Sets of field names that, taken together, are indexed, see above
15. verbose_name: A human-readable name for the object, singular
16. verbose_name_plural: the plural name for the object, defaulst is verbose_name + 's'
