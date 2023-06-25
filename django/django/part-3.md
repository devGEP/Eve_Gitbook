# 첫 번째 장고 앱 작성하기, Part 3

이제 Web-poll 애플리케이션에 공용 인터페이스인 “views”를 만드는 데 초점을 맞출 것입니다.

* Blog 홈페이지 – 가장 최근의 항목들을 보여줍니다
* 항목 “세부”(detail) 페이지 – 하나의 항목에 연결하는 영구적인 링크(permalink)를 제공합니다.
* 년도별 축적 페이지 – 주어진 연도의 모든 월별 항목들을 표시합니다.
* 월별 축적 페이지 – 주어진 월의 날짜별 항목들을 표시합니다.
* 날짜별 축적 페이지 – 주어진 날짜의 모든 항목들을 표시합니다.
* 댓글 기능 – 특정 항목의 댓글을 다룰 수 있는 기능

우리가 만드는 poll 어플리케이션에서 다음과 같은 네개의 view 를 만들어 보겠습니다.

* 질문 “색인” 페이지 – 최근의 질문들을 표시합니다.
* 질문 “세부” 페이지 – 질문 내용과, 투표할 수 있는 서식을 표시합니다.
* 질문 “결과” 페이지 – 특정 질문에 대한 결과를 표시합니다
* 투표 기능 – 특정 질문에 대해 특정 선택을 할 수 있는 투표 기능을 제공합니다.



## 뷰 추가하기

이제, polls/views.py에 뷰를 추가해보자. 이 뷰들은 인수를 받기 때문에 조금 모양이 다르다.

```python
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.
def index(request):
    return HttpResponse("Hello, world. You're at the polls index.");

def detail(request, question_id):
    return(HttpResponse("You're looking at question %s." % question_id))

def results(request, question_id):
    response = "You're looking at the results of question %s."
    return HttpResponse(response % question_id)

def vote(request, question_id):
    return HttpResponse("You're voting on question %s." % question_id)
```



다음의 path() 호출을 추가하여 이러한 새로운 뷰를 polls.urls 모듈을 연결해라.

```python
from django.urls import path
from . import views

urlpatterns = [
    #ex /polls/
    path("", views.index, name="index"),
    #ex /polls/5/
    path("<int:question_id>/", views.detail, name="detail"),
    #ex /polls/5/results/
    path("<int:question_id>/results/", views.results, name="results"),
    #ex /polls/5/vote/
    path("<int:question_id>/vote/", views.vote, name="vote"),
]
```



## 뷰가 실제로 뭔가를 하도록 만들기

각 뷰는 두 가지 중 하나를 하도록 되어 있습니다. 요청된 페이지의 내용이 담긴 **HttpResponse** 객체를 반환하거나, 혹은 **Http404** 같은 예외를 발생하게 해야합니다. 나머지는 당신에게 달렸습니다.



새로운 `index()` 뷰 하나를 호출했을 때, 시스템에 저장된 최소한 5 개의 투표 질문이 콤마로 분리되어, 발행일에 따라 출력됩니다.

{% code title="polls/views.py" %}
```python
from django.http import HttpResponse
from .models import Question

# Create your views here.
def index(request):
    latest_question_list = Question.objects.order_by("-pub_date")[:5]
    output = ", ".join([q.question_text for q in latest_question_list])
    return HttpResponse(output)
```
{% endcode %}

여기 몇가지 문제가 있습니다. 뷰에서 페이지의 디자인이 하드코딩 되어 있다고 합시다. 만약 페이지가 보여지는 방식을 바꾸고 싶다면, 이 Python 코드를 편집해야만 할 겁니다. 그럼, 뷰에서 사용할 수 있는 템플릿을 작성하여, Python 코드로부터 디자인을 분리하도록 Django의 템플릿 시스템을 사용해 봅시다.



우선, `polls` 디렉토리에 `templates`라는 디렉토리를 만듭니다. Django는 여기서 템플릿을 찾게 될 것입니다.

\
방금 만든 `templates` 디렉터리 내에 `templates` 라는 다른 디렉터리를 만들고 그 안에 `index.html` 이라는 파일을 만듭니다. 즉, 템플릿은 `polls/templates/polls/index.html``이어야 합니다.`

{% code title="polls/templates/polls/index.html" %}
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    {% raw %}
{% if latest_question_list %}
        <ul>
        {% for question in latest_question_list %}
            <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
        {% endfor %}
        </ul>
    {% else %}
        <p>No polls are available.</p>
    {% endif %}
{% endraw %}
</body>
</html>
```
{% endcode %}



## 지름길: render()

템플릿에 context를 채워넣어 표현한 결과를 HttpResponse 객체와 함께 돌려주는 구문은 자주 쓰는 문법이다. 따라서 Django는 이런 표현을 쉽게 표현할 수 있는 단축 기능을 제공한다.

{% code title="polls/views.py" %}
```python
from django.shortcuts import render

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by("-pub_date")[:5]
    context = {"latest_question_list": latest_question_list}
    return render(request, "polls/index.html", context)
```
{% endcode %}

render() 함수는 request 객체를 첫번째 인수로 받고, 템플릿 이름을 두번째 인수로 받으며, context 사전형 객체를 세번째 선택적(optional) 인수로 받는다. 인수로 지정된 contex로 표현된 템플릿의 HttpResponse 객체가 반환된다.



## 404 에러 일으키기

이제, 질문의 상세 뷰에 태클을 걸어보겠다. 상세 뷰는 지정된 설문조사의 질문 내용을 보여준다.

{% code title="polls/views.py" %}
```python
from django.http import Http404
from django.shortcuts import render

from .models import Question


# ...
def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, "polls/detail.html", {"question": question})
```
{% endcode %}

여기 새로운 내용이 추가되었다. 뷰는 요청된 질문의 ID가 없을 경우 Http404 예외를 발생시킨다.



## 지름길: get\_object\_or\_404()

만약 객체가 존재하지 않을 때 get()을 사용하여 Http404 예외를 발생시키는 것은 자주 쓰이는 용법이다. Django에서 이 기능에 대한 단축 기능을 제공한다. detail() 뷰를 단축 기능으로 작성하면 다음과 같다.

{% code title="polls/view.py" %}
```python
from django.shortcuts import get_object_or_404, render

from .models import Question


# ...
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, "polls/detail.html", {"question": question})
```
{% endcode %}

**`get_object_or_404()`** 함수는 Django 모델을 첫번째 인자로 받고, 몇 개의 키워드 인수를 모델 관리자의 get() 함수에 넘긴다. 만약 객체가 존재하지 않을 경우, Http404 예외가 발생한다.

또한, **`get_object_or_404()`** 함수처럼 동작하는 **`get_list_or_404()`** 함수가 있다. **get()** 대신 **filter()**를 쓴다는 것이 다르다. 리스트가 비어있을 경우, **`Http404`** 예외를 발생시킨다.



## 템플릿 시스템 사용하기

투표 앱의 detail() 뷰로 되돌아가 보자. context 변수 question이 주어졌을 때, polls/detail.html이라는 템플릿이 어떻게 보이는지 보자.

{% code title="polls/templates/polls/detail.html" %}
```html
<h1>{{ question.question_text }}</h1>
<ul>
{% raw %}
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }}</li>
{% endfor %}
{% endraw %}
</ul>
```
{% endcode %}

템플릿 시스템은 변수의 속성에 접근하기 위해 점-탐색(dot-lookup) 문법을 사용한다. 예제의 \{{question.question\_text\}} 구문을 보면, Django는 먼저 question 객체에 대해 사전형으로 탐색한다. 탐색에 실패하게 되면 속성값으로 탐색한다. 만약 속성 탐색에도 실패하면 리스트의 인덱스 탐색을 시도하게 된다.

\{% for %\} 반복 구문에서 메소드 호출이 일어난다. question.choice\_set.all은 Python에서 question.choice\_set.all() 코드로 해석되는데, 이때 반환된 Choice 객체의 반복자는 \{% for %\}에서 사용하기 적당하다.



## 템플릿에서 하드코딩된 URL 제거하기

polls/index.html 템플릿에 링크를 적으면, 이 링크는 다음과 같이 부분적으로 하드코딩된다는 것을 기억해라.

```html
<li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
```

이러한 강력하게 결합되고 하드코딩된 접근방식의 문제는 수 많은 템플릿을 가진 프로젝트들의 URL을 바꾸는 게 어려운 일이 된다는 점입니다. 그러나, `polls.urls` 모듈의 [`path()`](https://docs.djangoproject.com/ko/4.2/ref/urls/#django.urls.path) 함수에서 인수의 이름을 정의했으므로, `{% url %}` template 태그를 사용하여 url 설정에 정의된 특정한 URL 경로들의 의존성을 제거할 수 있습니다.

```html
<li><a href="{% raw %}
{% url 'detail' question.id %}
{% endraw %}">{{ question.question_text }}</a></li>
```



## URL의 이름공간 정하기

튜토리얼의 프로젝트는 polls라는 앱 하나만 가지고 진행했었다. 실제 Django 프로젝트는 앱이 몇개라도 올 수 있다. Django는 이 앱들의 URL을 어떻게 구별해낼까?

예를 들어, polls라는 앱은 detail이라는 뷰를 가지고 있고, 동일한 프로젝트에 블로그를 위한 앱이 있을 수 있다. Django가 \{% url %\} 템플릿태그를 사용할 때, 어떤 앱의 뷰에서 URL을 생성할 지 알 수 있을까?

정답은 URLconf에 이름공간을 추가하는 것이다. polls/urls.py 파일에 app\_name을 추가하여 앱의 이름공간을 설정할 수 있다.

{% code title="polls/urls.py" %}
```python
from django.urls import path
from . import views

app_name = "polls"
urlpatterns = [
    #ex /polls/
    path("", views.index, name="index"),
    #ex /polls/5/
    path("<int:question_id>/", views.detail, name="detail"),
    #ex /polls/5/results/
    path("<int:question_id>/results/", views.results, name="results"),
    #ex /polls/5/vote/
    path("<int:question_id>/vote/", views.vote, name="vote"),
]
```
{% endcode %}



이제, polls/index.html 템플릿의 기존 내용에서 아래 내용을 변경해보자.

```html
<li><a href="{% raw %}
{% url 'detail' question.id %}
{% endraw %}">{{ question.question_text }}</a></li>
```

아래와 같이 이름공간으로 나눠진 상세 뷰를 가리키도록 변경하자.

```html
<li><a href="{% raw %}
{% url 'polls:detail' question.id %}
{% endraw %}">{{ question.question_text }}</a></li>
```
