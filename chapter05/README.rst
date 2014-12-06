Writing your first Django app, part 3
=====================================

Write your first view
---------------------

1. In Django, web pages and other content are delivered by views. Each view is represented by a simple Python function (or method, in the case of class-based views). Django will choose a view by examining the URL that’s requested.

Let’s write the first view. Open the file polls/views.py and put the following Python code in it:

::

  from django.http import HttpResponse

  def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")

2. This is the simplest view in Django. To call the view, we need to map it to a URL - and for this we need a URLconf. Create a file called urls.py in polls directory. In the polls/urls.py file include the following code:

::

  from django.conf.urls import patterns, url

  from polls import views

  urlpatterns = patterns('',
     url(r'^$', views.index, name='index'),
  )

The url() function is passed four arguments, two required: regex and view, and two optional: kwargs, and name. At this point, it’s worth reviewing what these arguments are for:

a. regex : The term “regex” is a commonly used short form meaning “regular expression”, which is a syntax for matching patterns in strings, or in this case, url patterns

b. view: When Django finds a regular expression match, Django calls the specified view function, with an HttpRequest object as the first argument and any “captured” values from the regular expression as other arguments. If the regex uses simple captures, values are passed as positional arguments; if it uses named captures, values are passed as keyword arguments. We’ll give an example of this in a bit.

c. kwargs: Arbitrary keyword arguments can be passed in a dictionary to the target view. We aren’t going to use this feature of Django in the tutorial.

d. name: Naming your URL lets you refer to it unambiguously from elsewhere in Django especially templates. This powerful feature allows you to make global changes to the url patterns of your project while only touching a single file.

3. The next step is to point the root URLconf at the polls.urls module. In learningdjango/urls.py insert an include(), leaving you with:

::

  from django.conf.urls import patterns, include, url
  from django.contrib import admin

  urlpatterns = patterns('',
    url(r'^polls/', include('polls.urls')),
    url(r'^admin/', include(admin.site.urls)),
  )

You have now wired an index view into the URLconf. Go to http://localhost:8000/polls/ in your browser, and you should see the text “Hello, world. You’re at the polls index.”, which you defined in the index view.

Use the template system
-----------------------
1. Put the following code in template: polls/templates/polls/index.html:

::

    {% if question_list %}
      <ul>
         {% for question in question_list %}
            <li>{{ question.question_text }}</a></li>
         {% endfor %}
      </ul>
    {% else %}
        <p>No polls are available.</p>
    {% endif %}

2. Update polls/view.py to make it like bellow:

::

    from django.http import HttpResponse
    from django.template import RequestContext, loader
    from polls.models import Question

    def index(request):
        question_list = Question.objects.all()
        template = loader.get_template('polls/index.html')
        context = RequestContext(request, {
        'question_list': question_list,
    })
    return HttpResponse(template.render(context))
        return render(request, "polls/index.html", {'question_list': question_list})

Go to http://localhost:8000/polls/ to see the result, if you add question in part2, you'll see question list.

Note:
     Django knows how to find the template. Django’s TEMPLATE_LOADERS setting contains a list of callables that know how to import templates from various sources. One of the defaults is django.template.loaders.app_directories.Loader which looks for a “templates” subdirectory in each of the INSTALLED_APPS - this is how Django knows to find the polls templates even though we didn’t modify TEMPLATE_DIRS.

3. A shotcut: render()

It’s a very common idiom to load a template, fill a context and return an HttpResponse object with the result of the rendered template. Django provides a shortcut: render. So rewritten index view like:

::

    from django.shortcuts import render

    from polls.models import Question


    def index(request):
        question_list = Question.objects.all()
        context = {'latest_question_list': latest_question_list}
        return render(request, 'polls/index.html', context)

Write more views
----------------

1. Let's write a detail view for question like bellow in polls/view.py:

::

    from django.shortcuts import get_object_or_404, render

    from polls.models import Question


    def detail(request, question_id):
        question = get_object_or_404(Question, pk=question_id)
        return render(request, 'polls/detail.html', {'question': question})

2. Wire the new views into the polls/urls.py module by adding the following url() calls:

::

    urlpatterns = patterns('',
    # ex: /polls/
    url(r'^$', views.index, name='index'),
    # ex: /polls/5/
    url(r'^(?P<question_id>\d+)/$', views.detail, name='detail'),
    )

3. Add the template polls/detail.html:

::

    <h1>{{ question.question_text }}</h1>
    <ul>
        {% for choice in question.choice_set.all %}
            <li>{{ choice.choice_text }}</li>
        {% endfor %}
    </ul>

4. Open browser with: http://localhost:8000/polls/1/, you'll see the question. If 404 shows, that means there isn't any Question in your data. you can add by shell or by admin interface.

5. Update the polls/index.html to link to detail page:

::

    {% if question_list %}
         <ul>
            {% for question in question_list %}
               <li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
            {% endfor %}
         </ul>
    {% else %}
           <p>No polls are available.</p>
    {% endif %}

Note: here we use "{% url %}" template tag, this works by looking up the URL definition as specified in
the polls.urls module. You can see exactly where the URL name of ‘detail’ is defined below

::
    url(r'^(?P<question_id>\d+)/$', views.detail, name='detail')

here we define a name "detail", we use this for reverse, and even later you change the url to other urls like "r'^specifics/(?P<question_id>\d+)/$", since the name doesn't change, you don't need to update the url in template.

Summary
-------

Now we have know the most three important part in django: view, models, template. see detail about `Django MTV <https://docs.djangoproject.com/en/1.7/faq/general/#django-appears-to-be-a-mvc-framework-but-you-call-the-controller-the-view-and-the-view-the-template-how-come-you-don-t-use-the-standard-names>`_
