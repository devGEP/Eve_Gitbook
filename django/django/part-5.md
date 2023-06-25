# 첫 번째 장고 앱 작성하기, Part 5

이제 이를 위한 자동화된 테스트를 작성할 것이다.

[https://docs.djangoproject.com/ko/4.2/intro/tutorial05/](https://docs.djangoproject.com/ko/4.2/intro/tutorial05/)

## 자동화된 테스트 소개

### 자동화된 테스트란 무엇인가요?

테스트는 코드 작동을 위한 루틴이다.

테스트는 다양한 수준에서 작동한다. 일부 테스트는 작은 세부 사항에 적용될 수 있다.(특정 모델 메서드는 예상대로 값을 반환하는가?) 또 다른 테스트는 소프트웨어의 전반적인 작동을 검사한다.(사이트에서 사용자 입력 시퀀스가 원하는 결과를 생성하는가?)

자동화된 테스트에서 다른 점은 테스트 작업이 시스템에서 수행된다는 점이다. 한 번 테스트 세트를 작성한 이후에는 앱을 변경할 때 수동 테스트를 수행하지 않아도 원래 의도대로 코드가 작동하는지 확인할 수 있다.



## 테스트를 만들어야 하는 이유

### 테스트를 통해 시간을 절약할 수 있다.

특정 시점까지는 '제대로 작동하는지 확인' 하는 것이 테스트로서 충분할 수 있다. 더 정교한 앱에서는 구성 요소간에 수십 개의 복잡한 상호 작용이 있을 수 있다.

그러한 컴포넌트들 중 어느 하나에 대한 변경이 애플리케이션의 동작에 예기치 않은 결과를 초래할 수 있다. 그것이 여전히 ‘작동하는 것처럼 보인다’는 것은 당신이 무언가를 망가뜨리지 않았는지 확인하기 위해 당신의 코드 기능을 20가지의 다른 시험 데이터 변형과 함께 실행한다는 것을 의미할 수 있습니다. - 그렇게까지 할 필요는 없습니다.

이 수동 테스트 작업을 자동화된 테스트가 몇초만에 해낼수 있다면 귀한시간을 많이 아낄수 있겠죠?. 무언가가 잘못되어도 테스트를 통해 예기치 않은 동작을 일으키는 코드를 식별하는 데 도움이됩니다.

그러나 테스트를 작성하는 작업은 어플리케이션을 수동으로 테스트하거나 새로 발견된 문제의 원인을 확인하는 데 많은 시간을 투자하는 것보다 훨씬 더 효과적입니다.



### 테스트는 문제를 그저 식별하는 것이 아니라 예방한다.

테스트를 그저 개발의 부정적 측면으로 생각하는 것은 실수이다.

테스트가 없으면 어플리케이션의 목적 또는 의도 된 동작이 다소 불투명 할 수 있다. 심지어 자신의 코드 일 때도, 정확히 무엇을하고 있는지 알아 내려고 노력하게 된다.

테스트는 이 불투명함을 바꾼다. 그들은 내부에서 코드를 밝혀 내고, 어떤 것이 잘못 될 때, _그것이 잘못되었다는 것을 깨닫지 못했다고 할지라도, 잘못된 부분에 빛을 집중시킨다_.



### **테스트가 코드를 더 매력적으로 만듭니다.**

테스트 작성을 시작해야하는 또다른 이유는 다른 개발자들이 당신의 소프트웨어를 사용하는것을 진지하게 고려하기 전에 테스트 코드를 보기를 원하기 때문이다.



### **테스트는 팀이 함께 일하는것을 돕습니다**[**¶**](https://docs.djangoproject.com/ko/4.2/intro/tutorial05/#tests-help-teams-work-together)

복잡한 애플리케이션은 팀별로 유지 관리된다. 테스트는 동료가 실수로 코드를 손상시키지 않는다는 것을 보증한다. (그리고 당신이 동료의 코드를 모르는새에 망가트리는것도). 장고 프로그래머로서 생계를 꾸려 나가려면 테스트를 잘해야한다!



## 첫 번째 테스트 작성하기

### 버그 식별하기

`Question.was_published_recently()` 메소드는 `Question`이 어제 게시된 경우 `True`를 반환(올바른 동작)할 뿐만 아니라 `Question`의 `pub_date` 필드가 미래로 설정되어 있을 때도 그렇습니다(틀린 동작).

[`shell`](https://docs.djangoproject.com/ko/4.2/ref/django-admin/#django-admin-shell)을 사용해 미래의 날짜로 메소드를 실행해 버그를 확인합니다.

```sh
$ pytho manage.py shell
```

```sh
In [1]: import datetime

In [2]: from django.utils import timezone

In [3]: from polls.models import Question

In [4]: future_question = Question(pub_date = timezone.now() + datetime.timedelta(days=30))

In [5]: future_question.was_published_recently()
Out[5]: True
```

미래는 ‘최근(recent)’이 아니기 때문에 이는 분명히 잘못된 것입니다.



## 버그를 노출하는 테스트 만들기

문제를 테스트하기 위해 [`shell`](https://docs.djangoproject.com/ko/4.2/ref/django-admin/#django-admin-shell) 에서 방금 수행한 작업은 자동화된 테스트에서 수행할 수 있는 작업이므로 자동화된 테스트로 바꾸도록 합시다.

애플리케이션 테스트는 일반적으로 애플리케이션의 `tests.py` 파일에 있습니다. 테스트 시스템은 `test` 로 시작하는 파일에서 테스트를 자동으로 찾습니다.

`polls` 어플리케이션의 `tests.py` 파일에 다음을 입력하십시오

```python
import datetime

from django.test import TestCase
from django.utils import timezone

from .models import Question

# Create your tests here.
class QuestionModelTests(TestCase):
    def test_was_published_recently_with_future_question(self):
        time = timezone.now() + datetime.timedelta(days=30)
        future_question = Question(pub_date=time)
        self.assertIs(future_question.was_published_recently(), False)
```



## 테스트 실행

```sh
$ python manage.py test polls

Found 1 test(s).
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
F
======================================================================
FAIL: test_was_published_recently_with_future_question (polls.tests.QuestionModelTests)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/Users/gyeoeul/Desktop/django_practice/mysite/polls/tests.py", line 13, in test_was_published_recently_with_future_question
    self.assertIs(future_question.was_published_recently(), False)
AssertionError: True is not False

----------------------------------------------------------------------
Ran 1 test in 0.000s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

`assertIs()` 메소드를 사용하여, 우리가 `False`가 반환되기를 원함에도 불구하고 `was_published_recently()` 가 `True`를 반환한다는 것을 발견했습니다.



## 버그 수정

우리는 이미 문제가 무엇인지 알고 있습니다: `Question.was_published_recently()`는 `pub_date`가 미래에 있다면 `False`를 반환해야 합니다. `models.py`에서 날짜가 과거에 있을 때에만 `True`를 반환하도록 메소드를 수정하십시오.

```python
def was_published_recently(self):
    now = timezone.now()
    return now - datetime.timedelta(days=1) <= self.pub_date <= nowpy
```

그리고 다시 실행해보면,

```
Found 1 test(s).
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
.
----------------------------------------------------------------------
Ran 1 test in 0.000s

OK
Destroying test database for alias 'default'...
```



## 보다 포괄적인 테스트

우리가 여기 있는 동안, 우리는 was\_published\_recently() 메소드를 고정하는 것 이상을 할 수 있다. 사실 하나의 버그를 고치면서 다른 새로운 버그를 만들어 낸다면 곤란할 것이다.

메소드의 동작을 보다 포괄적으로 테스트 하기 위해 동일한 클래스에 두 가지 테스트 메소드를 추가해라.

```python
import datetime

from django.test import TestCase
from django.utils import timezone

from .models import Question

# Create your tests here.
class QuestionModelTests(TestCase):
    def test_was_published_recently_with_future_question(self):
        time = timezone.now() + datetime.timedelta(days=30)
        future_question = Question(pub_date=time)
        self.assertIs(future_question.was_published_recently(), False)

    def test_was_published_recently_with_old_question(self):
        time = timezone.now() - datetime.timedelta(days=1, seconds=1)
        old_question = Question(pub_date=time)

        self.assertIs(old_question.was_published_recently(), False)

    def test_was_published_recently_with_recent_question(self):
        time = timezone.now() - datetime.timedelta(hours=23, minutes=59, seconds=59)
        recent_question = Question(pub_date=time)

        self.assertIs(recent_question.was_published_recently(), True)
```

이제 우리는 `Question.was_published_recently()`가 과거, 최근, 미래의 질문에 대해 올바른 값을 반환한다는걸 확인시켜주는 세가지 테스트를 가졌습니다.

다시 말해, `polls` 는 조그만 애플리케이션이지만, 미래에 그것이 얼마나 복잡해지든 그리고 그것이 다른 코드와 상호 작용하든 간에, 테스트를 한 메서드가 예상대로 동작할 것이라는 것을 어느 정도 보장하고 있습니다.



## 뷰 테스트

설문조사 앱은 상당히 대충대충 만들어져 있다. 이 앱은 pub\_date 필드가 미래에 있는 질문까지도 포함하여 게시한다. 이것을 개선해야 한다. 미래로 pub\_date를 설정하는 것은 그 시기가 되면 질문이 게시되지만 그때까지는 보이지 않는 것을 의미한다.









