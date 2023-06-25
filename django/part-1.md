# 첫 번째 장고 앱 작성하기, Part 1

## 프로젝트 만들기

```sh
$ django-admin startproject mysite
```



{% code title="mysite 폴더 구조" %}
```
mysite/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py
```
{% endcode %}



* **manage.py**: Django 프로젝트와 다양한 방법으로 상호작용하는 커맨드라인의 유틸리티이다.
* **mysite/ 디렉토리 내부**: 프로젝트를 위한 실제 Python 패키지들이 저장된다. 이 디렉토리 내의 이름을 이용하여, (mystie.urls와 같은 방식으로) 프로젝트의 어디에서나 Python 패키지들을 임포트할 수 있다.
* **mysite/\_\_iinit\_\_.py**: Python으로 하여금 이 디렉토리를 패키지처럼 다루라고 알려주는 용도의 단순한 빈 파일이다.&#x20;
* **mysite/settings.py**: 현재 Django 프로젝트의 환경 및 구성을 저장한다.
* **mysite/urls.py**: 현재 Django project의 URL 선언을 저장한다. Django로 작성된 사이트의 "목차"라고 할 수 있다.
* **mysite/asgi.py**: 현재 프로젝트를 서비스하기 위한 ASGI-호환 웹 서버의 진입점이다.
* **mystie/wsgi.py**: 현재 프로젝트를 서비스하기 위한 WSGI 호환 웹 서버의 진입점이다.



## 개발 서버

```sh
$ cd mysite # 프로젝트 폴더로 이동
$ python manage.py runserver
```



{% hint style="info" %}
**포트 변경하기**

기본적으로, runserver 명령은 내부 IP의 8000번 포트로 개발 서버를 띄운다.

만약 이 서버의 포트를 변경하고 싶다면, 커맨드라인에서 인수를 전달해주면 된다. 예를 들어, 이 명령은 포트를 8080으로 서버를 시작할 것이다.

```sh
 $ python manage.py runserver 8080
```



서버의 IP를 변경하려면 포트와 함께 전달한다. 예를 들어, 사용 가능한 모든 공용 IP를 청취하려면 (이는 네트워크의 다른 컴퓨터에서 작업하고 싶을 때 유용한다.) 다음을 사용한다.

```sh
$ python manage.py runserver 0.0.0.0:80000
```
{% endhint %}



{% hint style="info" %}
**runserver의 자동 변경 기능**

**개발 서버는 요청이 들어올 때마다 자동으로 Python 코드를 다시 불러온다.**

코드의 변경사항을 반영하기 위해서 굳이 서버를 재가동하지 않아도 된다. 그러나, 파일을 추가하는 등의 몇몇의 동작은 개발서버가 자동으로 인식하지 못하기 떄문에, 이런 상황에서는 서버를 재가동해야 적용된다.
{% endhint %}



## 설문조사 앱 만들기

{% hint style="info" %}
**프로젝트 vs 앱**

프로젝트와 앱은 무엇이 다를까요?

앱은 블로그 시스템, 공개 기록 데이터베이스 또는 소규모 의견조사 앱과 같은 작업을 수행하는 웹 애플리케이션이다.

프로젝트는 특정 웹 사이트에 대한 구성 및 앱의 모음이다. 한 프로젝트에 여러 개의 앱이 포함될 수 있다. 앱은 여러 프로젝트에 있을 수 있다.
{% endhint %}



```sh
$ python manage.py startapp polls
```



{% code title="polls의 디렉토리 구조" %}
```
mysite/
    polls/
        __init__.py
        admin.py
        apps.py
        migrations/
            __init__.py
        models.py
        test.py
        views.py
```
{% endcode %}



## 첫번째 뷰 작성하기

첫 번째 뷰를 작성해보자. "polls/views.py"를 열어 다음과 같은 파이썬 코드를 입력한다.

{% code title="polls/views.py" %}
```python
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world. You're at the polls index.");
```
{% endcode %}

Django에서 가장 간단한 형태의 뷰이다. 뷰를 호출하려면 이와 연결된 URL이 있어야 하는데, 이를 위해 URLconf가 사용된다.



{% code title="polls/urls.py 생성" %}
```python
from django.urls import path
from . import views

urlpatterns = [
    path("", views.index, name="index"),
]
```
{% endcode %}



다음 단계는, 최상위 URLconf에서 polls.urls 모듈을 바라보게 설정한다.

mysite/urls.py 파일을 열고, django.urls.include를 import하고, urlpatterns 리스트에 include() 함수를 다음과 같이 추가한다.

{% code title="mysite/urls.py" %}
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path("polls/", include("polls.urls")),
    path("admin/", admin.site.urls),
]
```
{% endcode %}

include() 함수는 다른 URLconf들을 참조할 수 있게 도와준다. Django가 함수 include()를 만나게 되면, URL의 그 시점까지 일치하는 부분을 잘라내고, 남은 문자열 부분을 후속 처리를 위해 include된 URLconf로 전달한다.



이제 잘 작동하는지 확인하기 위해 다음 명령을 입력해보자.

```sh
$ python manage.py runserver
```

브라우저에서 **http://localhost:8000/polls/**를 입력하면 index 뷰에 정의한 "Hello, world. You're at the polls index."가 보일 것이다.

<figure><img src="../.gitbook/assets/스크린샷 2023-06-25 오전 3.41.05.png" alt=""><figcaption></figcaption></figure>

path() 함수에는 2개의 필수 인수인 **route**와 **view**, 2개의 선택 가능한 인수로 **kwargs**와 **name**까지 모두 4개의 인수가 전달되었다. 이 시점에서, 이 인수들이 무엇인지 살펴보는 것은 의미가 있다.



### path() 인수: route

route는 URL 패턴을 가진 문자열이다. 요청이 처리될 때, Django는 urlpatterns의 첫 번째 패턴부터 시작하여, 일치하는 패턴을 찾을 때까지 요청된 URL을 각 패턴과 리스트의 순서대로 비교합니다.



패턴들은 GET이나 POST의 매개 변수들, 혹은 도메인 이름을 검색하지 않는다. 예를 들어, https://www.example.com/myapp/이 요청된 경우, URLconf는 오직 myapp/ 부분만 바라본다. https://www.example.com/myapp/?page=3, 같은 요청에도, **URLconf는 역시 myapp/ 부분만 신경쓴다.**





### path() 인수: view

Django에서 일치하는 패턴을 찾으면, HttpRequest 객체를 첫번째 인수로 하고, 경로로부터 '캡처된' 값을 키워드 인수로 하여 특정한 view 함수를 호출한다.



### path() 인수: kwargs

임의의 키워드 인수들은 목표한 view에 사전형으로 전달된다. 그러나 이 튜토리얼에서는 사용하지 않을 거다.



### path() 인수: name

URL에 이름을 지으면, 템플릿을 포함한 Django 어디에서나 명확하게 참조할 수 있다. 이 강력한 기능을 이용하여, 단 한의 파일만 수정해도 project 내의 모든 URL 패턴을 바꿀 수 있도록 도와준다.
