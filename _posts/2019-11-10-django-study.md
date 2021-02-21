---
layout: post
title: "AI developer 의 Backend django study"
author: "Eren"
tags: Backend
---

아무래도 초기 스타트업의 경우 자신이 어떤 능력을 가지고 있고 또 그 전에 어떤 직군이었던 간에 필요한 직군이 생긴다면 그 능력을 빠르게 학습하고 키워나가는 것이 중요하다고 생각합니다. 저는 AI 학습에 필요한 data 들을 효율적으로 구축/관리 하기 위해서 `data-management server`를 처음부터 끝까지 개발해야만 하는 상황이었고 그렇기에 backend study 는 필요했습니다. 
<br>
<br>
본 포스트는 AI 밖에 개발할 줄 모르던 AI 개발자가 작성하는 django-project study 정리에 관한 글입니다. 미래에 비개발자나 django 에 익숙하지 않은 다른 개발자들에게 도움이 됐으면 하는 마음입니다!

> [django-tutorial](https://www.djangoproject.com/start/) 을 참고했습니다.

<hr>
<br>

 
## PART 1
가장 먼저 프로젝트를 만들어야겠죠? django 를 처음 사용한다면, 이러한 초기 설정에 유의해야 합니다.
{% highlight bash %}
$ django-admin startproject mysite
{% endhighlight %}

mysite 에는 다음과 같은 파일들이 생성됩니다. (상위 mysite 라는 이름은 추후 있을 path 에서 혼동이 있을 수 있기 떄문에 변경합니다.)

- manage.py : Django 프로젝트와 다양한 방법으로 상호작용하는 커맨드라인의 유틸리티 입니다.
- mysite/__init.py : Python으로 하여금 이 디렉토리를 패키지처럼 다루라고 알려주는 용도의 단순한 빈 파일입니다.
- mysite/settings.py : 현재 Django 프로젝트의 환경 및 구성을 저장합니다.
- mysite/urls.py : 현재 Django project의 URL 선언을 저장합니다. Django로 작성된 사이트의 "목차"라고 할 수 있습니다.
- mysite/wsgi.py : 현재 프로젝트를 서비스하기 위한 WSGI 호환 웹 서버의 진입점입니다.

일단 Django 가 제대로 동작하는지 확인해봅시다
{% highlight bash %}
$ python manage.py runserver (기본은 http://127.0.0.1:8000/ 입니다) 포트 변경을 위해서는 runserver 뒤에 포트번호를 붙이면 됩니다.
{% endhighlight %}

설문조사 앱을 만들기 위해 manage.py가 존재하는 디레고리에서 다음의 명령을 입력합니다. Django는 app의 기본 디렉토리 구조를 자동으로 생성할 수 있는 도구를 제공하기 때문에, 코드에만 집중이 가능합니다.
{% highlight bash %}
$ python manage.py startapp polls
{% endhighlight %}

첫 번째 views.py와 urls.py를 작성해봅시다 (polls/views.py)

{% highlight python %}
# polls/views.py
from django.http import HttpResponse

def index(request):
	return HttpResponse("Hello, world. You're at the polls index.")
{% endhighlight %}

{% highlight python %}
# polls/urls.py
from django.urls import path
from . import views

urlpatterns = [
	path('', views.index, name='index'),
]
{% endhighlight %}

## PART 2
- 데이터베이스 설치
    - 기본적으로는 SQLite을 사용하도록 구성되어 있습니다. Python에서 기본으로 제공되기 때문에 별도의 설치가 필요없습니다. 그러나 실제 프로젝트에서는 확장성 있는 데이터베이스를 사용하는 것이 좋습니다.
    - 다른 데이터베이스를 사용하는 경우 적절한 데이터베이스 바인딩을 설치하고, 데이터베이스 연결 설정과 맞게끔 DATABASES 'default' 항목의 값을 다음의 키 값으로 바꿔주세요.
    - ENGINE - 'django.db.backends.sqlite3', 'django.db.backends.postgresql', 'django.db.backends.mysql', 또는 'django.db.backends.oracle'. 그외에 서드파티 백엔드참조.
    - NAME - 데이터베이스의 이름. 만약 SQLite를 사용 중이라면, 데이터베이스는 당신의 컴퓨터의 파일로서 저장됩니다. 이 경우, NAME는 파일명을 포함한 절대경로로 지정해야 합니다. 기본값은 `os.path.join(BASE_DIR, 'db.sqlite3')`로 정의되어 있으며, 프로젝트 디렉토리 내에 `db.sqlite3` 파일로 저장됩니다.
    - SQLite를 데이터베이스로 사용하지 않는 경우, USER, PASSWORD, HOST와 같은 추가 설정이 반드시 필요하므로 자세한 내용은 Django-DataBase 를 참고해주세요.
- settings.py에 있는 INSTALLED_APPS는 Django와 함께 딸려오는 다음의 앱들을 포함합니다.
    - django.contrib.admin - 관리용 사이트. 곧 사용하게 될 겁니다.
    - django.contrib.auth - 인증 시스템.
    - django.contrib.contenttypes - 컨텐츠 타입을 위한 프레임워크.
    - django.contrib.sessions - 세션 프레임워크.
    - django.contrib.messages - 메세징 프레임워크.
    - django.contrib.staticfiles - 정적 파일을 관리하는 프레임워크.

{% highlight python %}
$ python manage.py migrate : migrate 명령은 INSTALLED_APPS 의 설정을 탐색하여

mysite/settings.py 의 데이터베이스 설정과 app과 함께 제공되는 데이터베이스 migrations에 따라 필요한 데이터베이스 테이블을 생성합니다.
{% endhighlight %}

- 모델 만들기 : models.py 란 부가적인 메타데이터를 가진 데이터베이스 구조(layout)을 말하며, 서버의 기획에서 가장 먼저 설계해야 하고 개발을 시작해야 하는 독단적인 것입니다. 먼저 우리가 만드는 쉬운 설문조사 앱(polls)를 위해 Question과 Choice라는 두 개의 모델을 만들어봅니다. Question은 CharField를 가지는 question과 DateTimeField를 가지는 발행일 두개의 필드로 구성됩니다. Choice는 선택지(choice)와 표(vote) 계산을 위한 두 개의 필드로 구성됩니다. 또한 하나의 질문에 여러 개의 선택지가 있기 때문에 Choice class 안에 Question이 ForeignKey로 연결 되어 있습니다.

{% highlight python %}
# polls/models.py
from django.db import models

class Question(models.Model):
	question_text = models.CharField(max_length=200)
	pub_date = models.DateTimeField('date published')

class Choice(models.Model):
	question = models.ForeignKey(Question, on_delete=models.CASCADE)
	choice_text = models.CharField(max_length=200)
	votes = models.IntegerField(default=0)
{% endhighlight %}

- models.py 에서 짜여진 이 작은 코드는 Django에게 상당한 양의 정보를 전달합니다. 이 앱을 위한 데이터베이스 스키마 생성(CREATE TABLE 문)과 동시에 Question과 Choice 객체에 접근하기 위한 Python 데이터베이스 접근 API를 생성합니다.

일단 settings.py에서 polls이라는 app을 INSTALLED_APPS 설정에 추가합니다.

{% highlight python %}
INSTALLED_APPS = [
	'polls.apps.PollsConfig',
	'django.contrib.admin',
	'django.contrib.auth',
	'django.contrib.contenttypes',
	'django.contrib.sessions',
	'django.contrib.messages',
	'django.contrib.staticfiles',
]

$ python manage.py makemigrations polls : 모델을 변경시킨 사실과 이 변경사항을 migration으로 저장시키고 싶다는 명령어 입니다.
Migrations for 'polls':
polls/migrations/0001_initial.py:
		- Create model Choice
		- Create model Question
		- Add field question to choice
{% endhighlight %}

추가적으로 migration이 내부적으로 어떤 SQL 문장을 실행하는지 보려면 아래의 코드를 입력하면 됩니다.

{% highlight python %}
$ python manage.py sqlmigrate polls 0001
{% endhighlight %}

이제, migrate 를 실행시켜 데이터베이스에 모델과 관련된 테이블을 생성해줍니다. (항상 변경사항을 makemigrations로 알리고 migrate로 변경사항을 적용시키세요!)

{% highlight python %}
$ python manage.py migrate
Operations to perform: Apply all migrations: admin, auth, contenttypes, polls, sessions
Running migrations: Rendering model states... DONE
Applying polls.0001_initial... OK
{% endhighlight %}


API 가지고 놀기 : 대화식 Python shell 에서 API를 자유롭게 가지고 놉시다.

{% highlight python %}
$ python manage.py shell
{% endhighlight %}

shell 진입 후 아래의 tutorial에 따라 데이터를 만들고 queryset을 확인해봅니다.

{% highlight python %}
>>> from polls.models import Choice, Question # Import the model classes we just wrote.

# No questions are in the system yet.

>>> Question.objects.all()<QuerySet []>

# Create a new Question.

# Support for time zones is enabled in the default settings file, so

# Django expects a datetime with tzinfo for pub_date. Use timezone.now()

# instead of datetime.datetime.now() and it will do the right thing.

>>> from django.utils import timezone

>>> q = Question(question_text="What's new?", pub_date=timezone.now())

# Save the object into the database. You have to call save() explicitly.

>>> q.save()

# Now it has an ID.

>>> q.id1

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

<QuerySet [<Question: Question object (1)>]>
{% endhighlight %}

여기서 <Question: Question object (1)>은 이 객체를 표현하는 데 좋지 않기 때문에 __str__() 메소드를 추가하여 객체의 표현을 바꿉니다.

{% highlight python %}
from django.db import models

class Question(models.Model):
# ...
	def __str__(self):
		return self.question_text

class Choice(models.Model):
# ...
	def __str__(self):
		return self.choice_text
{% endhighlight %}

polls/models.py 에 새로운 메소드를 추가합니다.

{% highlight python %}
import datetime

from django.db import models

from django.utils import timezone

class Question(models.Model):

# ...

def was_published_recently(self):

return self.pub_date >= timezone.now() - datetime.timedelta(days=1)

>>> from polls.models import Choice, Question

# Make sure our __str__() addition worked.

>>> Question.objects.all()

<QuerySet [<Question: What's up?>]>

# Django provides a rich database lookup API that's entirely driven by

# keyword arguments.

>>> Question.objects.filter(id=1)

<QuerySet [<Question: What's up?>]>

>>> Question.objects.filter(question_text__startswith='What')

<QuerySet [<Question: What's up?>]>

# Get the question that was published this year.

>>> from django.utils import timezone

>>> current_year = timezone.now().year

>>> Question.objects.get(pub_date__year=current_year)

<Question: What's up?>

# Request an ID that doesn't exist, this will raise an exception.

>>> Question.objects.get(id=2)

Traceback (most recent call last):

...

DoesNotExist: Question matching query does not exist.

# Lookup by a primary key is the most common case, so Django provides a

# shortcut for primary-key exact lookups.

# The following is identical to Question.objects.get(id=1).

>>> Question.objects.get(pk=1)

<Question: What's up?>

# Make sure our custom method worked.

>>> q = Question.objects.get(pk=1)

>>> q.was_published_recently()

True

# Give the Question a couple of Choices. The create call constructs a new

# Choice object, does the INSERT statement, adds the choice to the set

# of available choices and returns the new Choice object. Django creates

# a set to hold the "other side" of a ForeignKey relation

# (e.g. a question's choice) which can be accessed via the API.

>>> q = Question.objects.get(pk=1)

# Display any choices from the related object set -- none so far.

>>> q.choice_set.all()

<QuerySet []>

# Create three choices.

>>> q.choice_set.create(choice_text='Not much', votes=0)

<Choice: Not much>

>>> q.choice_set.create(choice_text='The sky', votes=0)

<Choice: The sky>

>>> c = q.choice_set.create(choice_text='Just hacking again', votes=0)

# Choice objects have API access to their related Question objects.

>>> c.question

<Question: What's up?>

# And vice versa: Question objects get access to Choice objects.

>>> q.choice_set.all()

<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>

>>> q.choice_set.count()

3

# The API automatically follows relationships as far as you need.

# Use double underscores to separate relationships.

# This works as many levels deep as you want; there's no limit.

# Find all Choices for any question whose pub_date is in this year

# (reusing the 'current_year' variable we created above).

>>> Choice.objects.filter(question__pub_date__year=current_year)

<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>

# Let's delete one of the choices. Use delete() for that.

>>> c = q.choice_set.filter(choice_text__startswith='Just hacking')

>>> c.delete()
{% endhighlight %}

관리자 생성하기

{% highlight python %}
$ python manage.py createsuperuser

Username: admin

Email address : admin@example.com

Password: mondeique

Password: mondeique

Superuser created successfully.
{% endhighlight %}

관리자 사이트에서 polls app 을 변경가능하도록 만들기 : admin.py 페이지에서 Question 객체를 등록해주면 됩니다

{% highlight python %}
from django.contrib import admin

from .models import Question

admin.site.register(Question)
{% endhighlight %}
<hr>
## PART 3 : 공개 인터페이스 "뷰(View)" 추가하기 

- poll application 에는 다음과 같은 네 개의 view 가 생성되어야 합니다.
	- 질문 "index" - 최근의 질문들을 표시합니다.
	- 질문 "detail" - 질문 내용과, 투표할 수 있는 서식을 표시합니다.
	- 질문 "result" - 특정 질문에 대한 결과를 표시합니다.
	- 투표 기능 - 특정 질문에 대해 특정 선택을 할 수 있는 투표 기능을 제공합니다.
<br>
<br>
- 뷰 추가하기

{% highlight python %}
def detail(request, question_id):
	return HttpResponse("You're looking at question %s." % question_id)

def results(request, question_id):
	response = "You're looking at the results of question %s."
	return HttpResponse(response % question_id)

def vote(request, question_id):
	return HttpResponse("You're voting on question %s." % question_id)

{% endhighlight %}

- path() 호출 추가하기

{% highlight python %}
from django.urls import path

from . import views

urlpatterns = [

	# ex: /polls/
	
	path('', views.index, name='index'),
	
	# ex: /polls/5/
	
	path('<int:question_id>/', views.detail, name='detail'),
	
	# ex: /polls/5/results/
	
	path('<int:question_id>/results/', views.results, name='results'),
	
	# ex: /polls/5/vote/
	
	path('<int:question_id>/vote/', views.vote, name='vote'),

]
{% endhighlight %}

- 뷰가 실제로 뭔가를 하도록 만들기 : render는 템플릿에 context를 채워넣어 표현한 결과를 HttpResponse 객체와 함께 돌려주는 구문입니다.

{% highlight python %}
from django.shortcuts import render

from .models import Question

def index(request):
	latest_question_list = Question.objects.order_by('-pub_date')[:5]
	context = {'latest_question_list': latest_question_list}
	return render(request, 'polls/index.html', context)
{% endhighlight %}

- 404 error 일으키기 : 만약 객체가 존재하지 않을 때 get()을 사용하여 Http404 예외를 발생시킵니다.

{% highlight python %}
from django.shortcuts import get_object_or_404, render
from .models import Question
# ...

def detail(request, question_id):
	question = get_object_or_404(Question, pk=question_id)
	return render(request, 'polls/detail.html', {'question': question})
{% endhighlight %}

- URL의 이름공간 정하기 : 튜토리얼의 프로젝트는 polls라는 앱 하나만 가지고 진행했습니다. 그러나 실제 Django project는 앱이 몇개라도 올 수 있으며 Django는 이 앱의 URL을 URLconf에 namespace를 추가하여 어플리케이션의 이름공간을 설정할 수 있습니다.

{% highlight python %}
from django.urls import path

from . import views

app_name = 'polls'

urlpatterns = [

	path('', views.index, name='index'),
	
	path('<int:question_id>/', views.detail, name='detail'),
	
	path('<int:question_id>/results/', views.results, name='results'),
	
	path('<int:question_id>/vote/', views.vote, name='vote'),
	
]
{% endhighlight %}
<hr>
## PART 4

- 실제 vote() 함수에 이제 선택할 때마다 하나씩 증가하는 코드를 짜봅니다.
- `request.POST`는 키로 전송된 자료에 접근할 수 있도록 해주는 사전과 같은 객체입니다. 이 경우, `request.POST['choice']` 는 선택된 설문의 ID를 문자열로 반환합니다. `request.POST`의 값은 항상 문자열들입니다.Django는 같은 방법으로 GET 자료에 접근하기 위해 `request.GET`를 제공합니다. 그러나 POST 요청을 통해서만 자료가 수정되게하기 위해서, 명시적으로 코드에 `request.POST`를 사용하고 있습니다.
- 만약 POST 자료에 **choice** 가 없으면, **request.POST['choice']** 는 **[KeyError](https://docs.python.org/3/library/exceptions.html#KeyError)** 가 일어납니다. 위의 코드는 **[KeyError](https://docs.python.org/3/library/exceptions.html#KeyError)** 를 체크하고, choice가 주어지지 않은 경우에는 에러 메시지와 함께 설문조사 폼을 다시보여줍니다.
- 설문지의 수가 증가한 이후에, 코드는 일반 **[HttpResponse](https://docs.djangoproject.com/ko/2.2/ref/request-response/#django.http.HttpResponse)** 가 아닌 **[HttpResponseRedirect](https://docs.djangoproject.com/ko/2.2/ref/request-response/#django.http.HttpResponseRedirect)** 를 반환하고, **[HttpResponseRedirect](https://docs.djangoproject.com/ko/2.2/ref/request-response/#django.http.HttpResponseRedirect)**는 하나의 인수를 받습니다: 그 인수는 사용자가 재전송될 URL 입니다. (이 경우에 우리가 URL을 어떻게 구성하는지 다음 항목을 보세요)
- 위의 파이썬 주석이 지적했듯이, POST 데이터를 성공적으로 처리 한 후에는 항상 **[HttpResponseRedirect](https://docs.djangoproject.com/ko/2.2/ref/request-response/#django.http.HttpResponseRedirect)** 를 반환해야합니다. 이 팁은 Django에만 국한되는것이 아닌 웹개발의 권장사항입니다.
- 우리는 이 예제에서 **[HttpResponseRedirect](https://docs.djangoproject.com/ko/2.2/ref/request-response/#django.http.HttpResponseRedirect)** 생성자 안에서 **[reverse()](https://docs.djangoproject.com/ko/2.2/ref/urlresolvers/#django.urls.reverse)** 함수를 사용하고 있습니다. 이 함수는 뷰 함수에서 URL을 하드코딩하지 않도록 도와줍니다. 제어를 전달하기 원하는 뷰의 이름을, URL패턴의 변수부분을 조합해서 해당 뷰를 가리킵니다.

{% highlight python %}
from django.http import HttpResponse, HttpResponseRedirect

from django.shortcuts import get_object_or_404, render

from django.urls import reverse

from .models import Choice, Question

# ...

def vote(request, question_id):

	question = get_object_or_404(Question, pk=question_id)
	
	try:
	
		selected_choice = question.choice_set.get(pk=request.POST['choice'])
		
		except (KeyError, Choice.DoesNotExist):
		
		# Redisplay the question voting form.
		
		return render(request, 'polls/detail.html', {
		
		'question': question,
		
		'error_message': "You didn't select a choice.",
	
	})

	else:
	
	selected_choice.votes += 1
	
	selected_choice.save()
	
	# Always return an HttpResponseRedirect after successfully dealing
	
	# with POST data. This prevents data from being posted twice if a
	
	# user hits the Back button.
	
		return HttpResponseRedirect(reverse('polls:results', args=(question.id,)))
{% endhighlight %}

- Generic View 사용하기 : 적은 코드가 더 좋다!
- detail() results() index() view 세 가지는 모두 중복되거나 비슷하기에 제너릭 뷰 시스템을 이용하는 것이 좋습니다.
일단 순서는 다음과 같습니다. URLconf 변환 ->  Django Generic View 사용
- URLconf 수정

{% highlight python %}
from django.urls import path

from . import views

app_name = 'polls'

urlpatterns = [

	path('', views.IndexView.as_view(), name='index'),
	
	path('<int:pk>/', views.DetailView.as_view(), name='detail'),
	
	path('<int:pk>/results/', views.ResultsView.as_view(), name='results'),
	
	path('<int:question_id>/vote/', views.vote, name='vote'),

]
{% endhighlight %}

- views 수정

{% highlight python %}
from django.http import HttpResponseRedirect

from django.shortcuts import get_object_or_404, render

from django.urls import reverse

from django.views import generic

from .models import Choice, Question

class IndexView(generic.ListView):

	template_name = 'polls/index.html'

	context_object_name = 'latest_question_list'

	def get_queryset(self):
	
	"""Return the last five published questions."""

		return Question.objects.order_by('-pub_date')[:5]

class DetailView(generic.DetailView):

	model = Question
	
	template_name = 'polls/detail.html'
	
class ResultsView(generic.DetailView):

	model = Question
	
	template_name = 'polls/results.html'
	
	def vote(request, question_id):

... # same as above, no changes needed.
{% endhighlight %}
<hr>
## PART 5 : testing 하기

- 버그 식별하기

{% highlight python %}
$ python manage.py shell

>>> import datetime

>>> from django.utils import timezone

>>> from polls.models import Question

>>> # create a Question instance with pub_date 30 days in the future

>>> future_question = Question(pub_date=timezone.now() + datetime.timedelta(days=30))

>>> # was it published recently?

>>> future_question.was_published_recently()

True
{% endhighlight %}

- polls/tests.py 수정하기

{% highlight python %}
import datetime

from django.test import TestCase

from django.utils import timezone

from .models import Question

class QuestionModelTests(TestCase):

def test_was_published_recently_with_future_question(self):

"""

was_published_recently() returns False for questions whose pub_date

is in the future.

"""

time = timezone.now() + datetime.timedelta(days=30)

future_question = Question(pub_date=time)

self.assertIs(future_question.was_published_recently(), False)
{% endhighlight %}

- 테스트 실행

{% highlight python %}
$ python manage.py test polls
{% endhighlight %}

- 버그 수정

{% highlight python %}
def was_published_recently(self):

	now = timezone.now()

	return now - datetime.timedelta(days=1) <= self.pub_date <= now

def test_was_published_recently_with_old_question(self):

"""

was_published_recently() returns False for questions whose pub_date

is older than 1 day.

"""

	time = timezone.now() - datetime.timedelta(days=1, seconds=1)
	
	old_question = Question(pub_date=time)
	
	self.assertIs(old_question.was_published_recently(), False)
	
def test_was_published_recently_with_recent_question(self):

	"""
	
	was_published_recently() returns True for questions whose pub_date
	
	is within the last day.
	
	"""
	
	time = timezone.now() - datetime.timedelta(hours=23, minutes=59, seconds=59)
	
	recent_question = Question(pub_date=time)
	
	self.assertIs(recent_question.was_published_recently(), True)
{% endhighlight %}

- 장고 테스트 클라이언트

{% highlight python %}
$ python manage.py shell

>>> from django.test.utils import setup_test_environment

>>> setup_test_environment()

>>> from django.test import Client

>>> # create an instance of the client for our use

>>> client = Client()

>>> # get a response from '/'

>>> response = client.get('/')

Not Found: /

>>> # we should expect a 404 from that address; if you instead see an

>>> # "Invalid HTTP_HOST header" error and a 400 response, you probably

>>> # omitted the setup_test_environment() call described earlier.

>>> response.status_code

404

>>> # on the other hand we should expect to find something at '/polls/'

>>> # we'll use 'reverse()' rather than a hardcoded URL

>>> from django.urls import reverse

>>> response = client.get(reverse('polls:index'))

>>> response.status_code

200

>>> response.contentb'\n    <ul>\n    \n        <li><a href="/polls/1/">What&#39;s up?</a></li>\n    \n    </ul>\n\n'

>>> response.context['latest_question_list']

<QuerySet [<Question: What's up?>]>
{% endhighlight %}

> 추가 view 수정과 test 는 django-practice repo에 있습니다.

<hr>
## PART 6 : 관리자 폼 커스터마이징

- polls/admin.py

{% highlight python %}
from django.contrib import admin

from .models import Question

class QuestionAdmin(admin.ModelAdmin):

fields = ['pub_date', 'question_text']

admin.site.register(Question, QuestionAdmin)
{% endhighlight %}

- polls/admin.py 좀 더 발전(fieldset 추가)

{% highlight python %}
from django.contrib import admin

from .models import Question

class QuestionAdmin(admin.ModelAdmin):

fieldsets = [

(None,               {'fields': ['question_text']}),

('Date information', {'fields': ['pub_date']}),

]

admin.site.register(Question, QuestionAdmin)
{% endhighlight %}

- 관련 객체 추가

{% highlight python %}
from django.contrib import admin

from .models import Choice, Question

# ...

admin.site.register(Choice)
{% endhighlight %}

- choice 객체를 만드는 것보다 Question 안에 choice 객체를 추가하는 것이 훨씬 좋다.

{% highlight python %}
from django.contrib import admin

from .models import Choice, Question

class ChoiceInline(admin.StackedInline):

model = Choice

extra = 3

class QuestionAdmin(admin.ModelAdmin):

fieldsets = [

(None,               {'fields': ['question_text']}),

('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),

]

inlines = [ChoiceInline]

admin.site.register(Question, QuestionAdmin)
{% endhighlight %}



