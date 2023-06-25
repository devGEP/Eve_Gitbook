# 첫 번째 장고 앱 작성하기, Part 2

## 데이터베이스 설치

이제, `mysite/settings.py` 파일을 열어보자. 이 파일은 Django 설정을 모듈 변수로 표현한 보통의 Python 모듈이다.



기본적으로 SQLite을 사용하도록 구성되어 있다. 그러나 실제 프로젝트를 시작할 때에는, 나중에 데이터베이스를 교체하느라 골치 아파질 일을 피하기 위해서라도 PostgreSQL 같이 좀 더 확장성 있는 데이터베이스를 사용하는 것이 좋다.

다른 데이터베이스를 사용해보고 싶다면, 적절한 [데이터베이스 바인딩](https://docs.djangoproject.com/ko/4.2/topics/install/#database-installation)을 설치하고, 데이터베이스 연결 설정과 맞게끔 [DATABASES](https://docs.djangoproject.com/ko/4.2/ref/settings/#std-setting-DATABASES) 'default' 항목의 값을 다음의 키 값으로 바꿔라.

* [ENGINE](https://docs.djangoproject.com/ko/4.2/ref/settings/#std-setting-DATABASE-ENGINE) - 'django.db.backends.sqlite3', 'django.db.backends.postgresql', 'django.db.backends.mysql', 또는 'django.db.backends.oracle'. 그 외엔 [서드파티 백엔드](https://docs.djangoproject.com/ko/4.2/ref/databases/#third-party-notes) 참조
* NAME - 데이터베이스의 이름이다. SQLite를 사용하는 경우 데이터베이스는 컴퓨터의 파일이 된다. 이 경우 :setting: 'NAME'은 파일 이름을 포함한 해당 파일의 전체 절대 경로여야 한다. 기본 값인 'BASE\_DIR'/'db.sqlite3'은 파일을 프로젝트 디렉토리에 저장한다.

SQLite를 데이터베이스로 사용하지 않는 경우, USER, PASSWORD, HOST 같은 추가 설정이 반드시 필요하다.



기본적으로는, INSTALLED\_APPS는 Django와 함께 딸려오는 다음의 앱들을 포함한다.

* django.contrib.admin - 관리용 사이트, 곧 사용하게 될거다.
* django.contrib.auth - 인증 시스템
* django.contrib.contenttypes - 컨텐츠 타입을 위한 프레임워크
* django.contrib.sessions - 세션 프레임워크
* django.contrib.messages - 메세징 프레임워크
* django.contrib.sstaticfiles - 정적 파일을 관리하는 프레임워크



이러한 기본 어플리케이션들 중 몇몇은 최소한 하나 이상의 데이터베이스 테이블을 사용하는데, 그러기 위해서는 데이터베이스에서 테이블을 미리 만들 필요가 있다. 이를 위해, 다음의 명령을 실행해보자.

```sh
$ python mangage.py migrate
```



[migrate](https://docs.djangoproject.com/ko/4.2/ref/django-admin/#django-admin-migrate) 명령은 [INSTALLED\_APPS](https://docs.djangoproject.com/ko/4.2/ref/settings/#std-setting-INSTALLED\_APPS) 의 설정을 탐색하여, `mysite/settings.py` 의 데이터베이스 설정과 app 과 함께 제공되는 database migrations(나중에 다루겠습니다) 에 따라, 필요한 데이터베이스 테이블을 생성합니다.



## 모델 만들기

이제, 모델을 정의해보자. 본질적으로, 모델이란 부가적인 메타데이터를 가진 데이터베이스의 구조(layout)을 말한다.



우리가 만들 여론조사 앱에서 **Questioin**과 **Choice**라는 두 가지 모델을 만들 것이다.

Question에는 질문과 발행일을 위한 두 개의 필드를 가진다. Choice는 선택 텍스트와 투표 집계를 위한 필드를 가진다.



이러한 개념은 Python 클래스로 표현된다. `polls/models.py` 파일을 다음과 같이 수정하자.

{% code title="polls/models.py" %}
```python
from django.db import models

# Create your models here.
class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField("date published")

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```
{% endcode %}

**데이터베이스의 각 필드는 Field 클래스의 인스턴스로서 표현**된다. CharField는 문자(Character) 필드를 표현하고, DateTimeField는 날짜와 시간(datetime) 필드를 표현한다. 이것은 각 필드가 어떤 자료형을 가질 수 있는지를 Django에게 말해준다.



**각각의 Field 인스턴스의 이름(question\_text 또는 pub\_date)**은 기계가 읽기 좋은 형식의 데이터베이스 필드 이름이다. 데이터베이스에서는 컬럼명으로 사용할 거다.



몇몇 Field 클래스들은 필수 인수가 필요하다. 예를 들어, CharField의 경우 max\_length를 입력해주어야 한다. 이것은 데이터베이스 스키마에서만 필요한 것이 아닌 값을 검증할 때도 쓰이는데, 곧 보게 될 것이다.

또한, Field는 다양한 선택적 인수들을 가질 수 있다. 이 예제에서는, default로 하여금 votes의 기본값을 0으로 설정하였다.

마지막으로, ForeignKey를 사용한 관계설정에 대해 설명하겠다. 이 예제에서는 각각의 Choice가 하나의 Question에 관계된다는 것을 Django에게 알려준다. Django는 다-대-일(many-to-one), 다-대-다(many-to-many), 일-대-일(one-to-one)과 같은 일반 데이터베이스의 관계들을 지원한다.



## 모델의 활성화

모델에 대한 이 작은 코드가, Django에게는 상당한 양의 정보를 전달한다. Django는 이 정보를 가지고 다음곽 같은 일을 할 수 있다.

* 이 앱을 위한 데이터베이스 스키마 생성(CREATE TABLE문)
* Question과 Choice 객체에 접근하기 위한 Python 데이터베이스의 접근 API를 생성



그러나, 가장 먼저 현재 프로젝트에게 **polls** 앱이 설치되어 있다는 것을 알려야 한다.

앱을 현재의 프로젝트에 포함시키기 위해서는, 앱의 구성 클래스에 대한 참조를 **INSTALLED\_APPS**에 설정에 추가해야 한다. **PollsConfig 클래스**는 `polls/app.py` 파일 내에 존재한다. 따라서, 점으로 구분된 경로는 **'polls.apps.PollsConfig'**가 된다. 이 점으로 구분된 경로를, `mysite/settings.py` 파일을 편집하여 **INSTALLED\_APPS** 설정에 추가하면 된다. 이는 다음과 같이 보일 것이다.

{% code title="polls/settings.py" %}
```python
INSTALLED_APPS = [
    'polls.apps.PollConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```
{% endcode %}



이제, Django는 polls 앱이 포함된 것을 알게 되었다. 다른 명령을 내려보자.

```python
$ python mangae.py makemigrations polls

Migrations for 'polls':
  polls/migrations/0001_initial.py
    - Create model Question
    - Create model Choice
```



makemigrations을 실행시킴으로써, 내가 새로운 모델을 만들었다.<mark style="color:yellow;">(모델을 변경했을 때는, 모델을 변경시킨 사실)</mark> 이 변경사항을 migration으로 저장시키고 싶다는 것을 Django에게 알려준다.

Migration은 Django가 모델(즉, 당신의 데이터베이스 스키마)의 변경사항을 디스크에 저장하는 방법이다.

이를 위해 migration들을 실행시켜주고, 자동으로 데이터베이스 스키마를 관리해주는 [migrate](https://docs.djangoproject.com/ko/4.2/ref/django-admin/#django-admin-migrate) 명령어가 있습니다. 이 명령을 알아보기 전에 migration이 내부적으로 어떤 SQL 문장을 실행하는지 살펴봅시다. [sqlmigrate](https://docs.djangoproject.com/ko/4.2/ref/django-admin/#django-admin-sqlmigrate) 명령은 migration 이름을 인수로 받아, 실행하는 SQL 문장을 보여줍니다.

```python
$ python manage.py sqlmigrate polls 0001
```

{% code title="명령어 결과" %}
```sh
BEGIN;
--
-- Create model Question
--
CREATE TABLE "polls_question" (
    "id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, 
    "question_text" varchar(200) NOT NULL, 
    "pub_date" datetime NOT NULL
);
--
-- Create model Choice
--
CREATE TABLE "polls_choice" (
    "id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, 
    "choice_text" varchar(200) NOT NULL, 
    "votes" integer NOT NULL, 
    "question_id" bigint NOT NULL REFERENCES 
    "polls_question" ("id") DEFERRABLE INITIALLY DEFERRED
);
CREATE INDEX "polls_choice_question_id_c5b4b260" ON "polls_choice" ("question_id");
COMMIT;
```
{% endcode %}



관심이 있다면, `python manage.py check` 명령을 통해 마이그레이션을 수행하거나 데이터베이스를 건드리지 않고도 프로젝트의 문제를 확인할 수 있다.

이제, **migrate**를 실행시켜 데이터베이스에 모델과 관련된 테이블을 생성해보자.

```
$ python manage.py migrate

Operations to perform:
  Apply all migrations: admin, auth, contenttypes, polls, sessions
Running migrations:
  Applying polls.0001_initial... OK
```



[migrate](https://docs.djangoproject.com/ko/4.2/ref/django-admin/#django-admin-migrate) 명령은 아직 적용되지 않은 마이그레이션을 모두 수집해 이를 실행하며(Django는 **`django_migrations`** 테이블을 두어 마이그레이션 적용 여부를 추적합니다) 이 과정을 통해 모델에서의 변경 사항들과 데이터베이스의 스키마의 동기화가 이루어집니다.

모델의 변경을 만드는 세 단계의 지침을 기억하세요.

* (**models.py**에서) 모델을 변경한다.
* **`python manag.py makemigrations`** 를 통해 이 변경사항에 대한 마이그레이션을 만들어라.
* **`python manage.py migrate`** 명령을 통해 변경사항을 데이터베이스에 적용해라.



## API 가지고 놀기

Python 쉘을 실행하려면 다음의 명령을 입력한다.

```sh
$ python manage.py shell

Python 3.10.6 (v3.10.6:9c7b4bd164, Aug  1 2022, 17:13:48) [Clang 13.0.0 (clang-1300.0.29.30)]
Type 'copyright', 'credits' or 'license' for more information
IPython 8.14.0 -- An enhanced Interactive Python. Type '?' for help.

In [1]: 
```

단순히 “python”을 입력하는 대신 이것을 사용한 이유는 `manage.py` 에 설정된 `DJANGO_SETTINGS_MODULE` 환경변수 때문입니다. 이 변수는 Django에게 `mysite/settings.py` 의 Python 가져오기 경로를 제공합니다.



```sh
In [1]: from polls.models import Choice, Question # Import

In [2]: Question.objects.all()
Out[2]: <QuerySet []>

In [3]: from django.utils import timezone

In [4]: q = Question(question_text="What's new?", pub_date=timezone.now())

In [5]: q.save()

In [6]: q.id
Out[6]: 1

In [7]: q.question_text
Out[7]: "What's new?"

In [8]: q.pub_date
Out[8]: datetime.datetime(2023, 6, 25, 6, 8, 2, 915684, tzinfo=datetime.timezone.utc)

In [9]: q.question_text = "What's up?"

In [10]: q.save()

In [11]: Question.objects.all()
Out[11]: <QuerySet [<Question: Question object (1)>]>
```



(`polls/models.py` 파일의) `Question` 모델을 수정하여, [`__str__()`](https://docs.djangoproject.com/ko/4.2/ref/models/instances/#django.db.models.Model.\_\_str\_\_) 메소드를 `Question`과 `Choice`에 추가해 봅시다.

{% code title="polls/models.py" %}
```python
from django.db import models

# Create your models here.
class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField("date published")

    def __str__(self):
        return self.question_text

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)

    def __str__(self):
        return self.choice_text
```
{% endcode %}



이 모델에 커스텀 메소드 또한 추가해봅시다:

{% code title="polls/models.py" %}
```python
import datetime

from django.db import models
from django.utils import timezone


class Question(models.Model):
    # ...
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)
```
{% endcode %}

`import datetime`은 Python의 표준 모듈인 [`datetime`](https://docs.python.org/3/library/datetime.html#module-datetime) 모듈을, `from django.utils import timezone`은 Django의 시간대 관련 유틸리티인 [`django.utils.timezone`](https://docs.djangoproject.com/ko/4.2/ref/utils/#module-django.utils.timezone)을 참조하기 위해 추가한 것입니다.



자세한 설명은 아래 링크를 참조해라.

[https://docs.djangoproject.com/ko/4.2/intro/tutorial02/](https://docs.djangoproject.com/ko/4.2/intro/tutorial02/)



## 관리자 생성하기

우선, 관리 사이트에 로그인할 수 있는 사용자를 생성해보자.

```
$ python manage.py createsuperuser

Username: admin
Email address: admin@example.com
Password: **************
Password(again): **************
Superuser created successfully
```



## 개발 서버 시작

Django 관리자 사이트는 기본으로 활성화되어 있다. 개발 서버를 켜고, 탐험해보자.

http://127.0.0.1:8000/admin/으로 이동하면, 관리자의 로그인 화면이 나타난다.

<figure><img src="../.gitbook/assets/스크린샷 2023-06-25 오후 3.27.04.png" alt=""><figcaption><p>Django 관리자 로그인 페이지</p></figcaption></figure>



<figure><img src="../.gitbook/assets/스크린샷 2023-06-25 오후 3.27.50.png" alt=""><figcaption><p>로그인 완료시 나타나는 페이지</p></figcaption></figure>



그런데, poll app 이 관리 인덱스 페이지에 보이지 않는다.

한가지 더 해야 할 일은 관리자에게 "Question" 대상에는 관리 인터페이스가 있다고 알려주는 것이다. 이렇게 하려면 `polls/admin.py` 파일을 열어 다음과 같이 편집해라.

{% code title="polls/admin.py" %}
```python
from django.contrib import admin
from .models import Question

# Register your models here.
admin.site.register(Question)
```
{% endcode %}



새로고침을 하게되면, 다음과 같이 poll app이 보일 것이다.

<figure><img src="../.gitbook/assets/스크린샷 2023-06-25 오후 3.30.32.png" alt=""><figcaption></figcaption></figure>
