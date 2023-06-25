# 첫 번째 장고 앱 작성하기, Part 4

## 간단한 폼 쓰기

part3의 투표 상세 템플릿("polls/detail.html")을 수정하여, 템플릿에 HTML\<form> 요소를 포함시켜 보자.

{% code title="polls/templates/polls/detail.html" %}
```html
<form action="{% raw %}
{% url 'polls:vote' question.id %}" method="post">
{% csrf_token %}
<fieldset>
    <legend><h1>{{ question.question_text }}</h1></legend>
    {% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}
    {% for choice in question.choice_set.all %}
        <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}">
        <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br>
    {% endfor %}
{% endraw %}
</fieldset>
<input type="submit" value="Vote">
</form>
```
{% endcode %}

* forloop.counter는 for 태그가 반복을 한 횟수를 나타낸다.
* POST 양식을 만들고 있기 떄문에(데이터를 수정하는 효과가 있을 수 있음), 교차 사이트 요청 위조(CSRF)에 대해 걱정해야 한다. 이 Django에서는 그것으로부터 보호하는 유용한 시스템을 가지고 있다. 간단히 말하자면, 내부 URL을 대상으로 하는 모든 POST 양식은 \{% csrf\_token %\} 템플릿 태그를 사용해야 한다.



이제 제출된 데이터를 처리하고 그 데이터로 무언가를 수행하는 Django 뷰를 작성해보자. 설문조사 앱을 위해 아래에 나와있는 코드를 포함하는 URLconf를 만들었다.

{% code title="polls/urls.py" %}
```python
path("<int:question_id>/vote/", views.vote, name="vote"),
```
{% endcode %}

또, 우리는 vote() 함수를 가상으로 만들었다. 실제로 구현해보자.

**`polls/views.py`**에 다음을 추가하자.

```python
from django.http import HttpResponse, HttpResponseRedirect
from django.shortcuts import get_object_or_404, render
from django.urls import reverse

from .models import Choice, Question

def vote(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    try:
        selected_choice = question.choice_set.get(pk=request.POST["choice"])
    except (KeyError, Choice.DoesNotExist):
        #Redisplay the question voting form.
        return render(
            request, "polls/detail.html",
            {
                "question": question,
                "error_message": "You didn't select a choice",
            },
        )
    else:
        selected_choice.votes += 1
        selected_choice.save()
        
        return HttpResponseRedirect(reverse("polls:results", args=(question_id)))

```



request는 HttpRequest 개체이다.

어떤 이가 설문조사에 설문을 하고 난 뒤에는, vote() 뷰는 설문조사 결과 페이지로 리다이렉트한다. 그 뷰를 작성해보자.

{% code title="polls/views.py" %}
```python
from django.shortcuts import get_object_or_404, render


def results(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, "polls/results.html", {"question": question})
```
{% endcode %}



이제, **`polls/results.html`** 템플릿을 만든다.

```html
<h1>{{ question.question_text }}</h1>

<ul>
{% raw %}
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
{% endfor %}
</ul>

<a href="{% url 'polls:detail' question.id %}
{% endraw %}">Vote again?</a>
```



## 제너릭 뷰 사용하기: 적은 코드가 더 좋다.

detail()과 result()의 view들은 매우 간단하며, 위에서 언급한 바와 같이 중복된다. 여론조사 목록을 보여주는 index() view도 비슷하다.

이러한 보기는 URL에 전달된 매개 변수에 따라 데이터베이스에서 데이터를 가져오고, 템플릿을 로드하고, 렌더링된 템플릿을 반환하는 기본 웹 개발의 일반적인 경우를 나타낸다. 이것이 매우 흔하기 때문에, Django는 'generic views' 시스템이라고 불리는 단축키를 제공한다.

제너릭 뷰는 일반적인 패턴을 추상화여 앱을 작성하기 위해 Python 코드를 작성하지 않아도 된다.

우리 설문조사 앱을 제너릭 뷰 시스템으로 변환해서 우리의 코드를 많이 삭제하도록 하자. 이러한 전환을 하려면 다음과 같이 몇 가지 단계만 거치면 된다.

1. URLconf를 변환해라.
2. 불필요한 오래된보기 중 일부를 삭제해라.
3. Django의 제너릭 뷰를 기반으로 새로운 뷰를 도입해라.



## URLconf 수정

먼저, poll/urls.py URLconf를 열어 다음과 같이 변경해라.

```python
from django.urls import path
from . import views

app_name = "polls"
urlpatterns = [
    #ex /polls/
    path("", views.IndexView.as_view(), name="index"),
    #ex /polls/5/
    path("<int:pk>/", views.DetailView.as_view(), name="detail"),
    #ex /polls/5/results/
    path("<int:pk>/results/", views.ResultView.as_view(), name="results"),
    #ex /polls/5/vote/
    path("<int:question_id>/vote/", views.vote, name="vote"),
]
```

두 번째와 세 번째 패턴의 경로 문자열에서 일치하는 패턴들의 이름이 \<question\_id>에서 \<pk>로 변경되었다.



## views 수정

다음으로 이전의 index, detail, results 뷰를 제거하고 장고의 일반적인 뷰를 대신 사용하자. 그렇게 하려면 polls/views.py 파일을 열고 다음과 같이 변경해라.

```python
from django.shortcuts import render, get_object_or_404
from django.http import HttpResponseRedirect
from django.urls import reverse
from django.views import generic

from .models import Choice, Question

# Create your views here.
class IndexView(generic.ListView):
    template_name = "polls/index.html"
    context_object_name = "latest_question_list"

    def get_queryset(self):
        return Question.objects.order_by("-pub_date")[:5]

class DetailView(generic.DetailView):
    model = Question
    template_name = "polls/detail.html"

class ResultView(generic.DetailView):
    model = Question
    template_name = "polls/results.html"

def vote(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    try:
        selected_choice = question.choice_set.get(pk=request.POST["choice"])
    except (KeyError, Choice.DoesNotExist):
        #Redisplay the question voting form.
        return render(
            request, "polls/detail.html",
            {
                "question": question,
                "error_message": "You didn't select a choice",
            },
        )
    else:
        selected_choice.votes += 1
        selected_choice.save()

        return HttpResponseRedirect(reverse("polls:results", args=(question.id, )))
```
