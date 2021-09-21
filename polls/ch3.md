# 3. 튜토리얼 따라하기(설문조사앱)

장고 공식 튜토리얼([링크](https://docs.djangoproject.com/ko/3.2/intro/tutorial01/))



## 3.2.2 웹 서버 시작하기

```bash
$ python manage.py runserver
```



### 포트 변경

```bash
$ python manage.py 8080
```

- default : 8000 포트

### I**P직접지정**

```bash
$ python manage.py 0.0.0.0:8000
```
```bash
$ python manage.py 0:8080
```

- IP주소 0은 0.0.0.0의 약어

- 해당 형태로 동작 시킬 때는 settings.py에 있는 `ALLOWED_HOSTS` 를 설정해야 함

- 이 경량 웹 서버를 이용해 실제 서버에 배포하지 않아도 웹 서비스의 변경 사항을 테스트

- 서버가 실행 중이어도 소스 코드의 변경 사항은 자동 반영

  

## 3.2.3 설문조사 앱 만들기

### 앱 생성

```bash
$ python manage.py startapp polls
```



## 3.2.4 첫 번째 뷰 만들기

```python
from django.shortcuts import HttpResponse

# Create your views here.
def index(request):
    return HttpResponse("hello, world. you're at the polls index.")

```



### polls/urls.py 생성

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

- path(route, view, kwargs, name)
  - route : 주소
  - view : route의 주소로 접근했을 때 호출할 뷰
  - kwargs : 뷰에 전달할 값들
  - name : route의 이름을 의미



### cofing/ruls.py 수정

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('polls/', include('polls.ruls')),
]

```



## 3.2.5 DB 만들기

config/settings.py 76번째 줄 : 데이터베이스 관련 설정

- ENGINE : 어떤 종류의 데이터베이스를 사용할지 설정하는 부분
- 우리는 sqlite3 사용
- sqlites, postgresql, mysql, oracle 이렇게 사용 가능



## 3.2.6 모델 만들기

- 데이터베이스의 구조도
- 데이터베이스에 **어떤 테이블을 만들고 어떤 컬럼을 갖게 할 것인지 결정**
- 해당 컬럼의 **제약 조건**까지도 모델에서 결정

### **polls/models.py**

```python
from django.db import models

class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

- 장고의 모델은 models.Model을 상속받아 만듬
- 이 부모클래스가 실제로 DB와 ORM을 이용해 동작하는 기능들을 가지고 있다
- 사람이 읽기 쉬운 형태로 사용하고 싶으면 `'date published'` 처럼 인자로 전달
- ForignKey
  - 다른 모델과의 관계를 만들기 위해서 사용
  - Choice 모델이 ForeignKey로 Question 모델을 갖는다는 것은 
    Choice 모델이 Question에 소속된다는 것을 의미

### settings.py에 앱 추가

```python
INSTALLED_APPS = [
    'polls',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```



### 데이터베이스에 적용

```bash
$ python manage.py makemigrations polls
```
- 앱의 변경사항을 추적해 DB에 적용할 내용을 만듬
- 그 결과는 polls/migrations/0001_initial.py 파일에 기록됨

```bash
$ python manage.py sqlmigrate polls 0001
```
- SQL문 확인
- 어떤 SQL을 실행할지, 문제가 있는 쿼리는 아닐지 판단가능
- 장고는 ORM을 통해 이런 쿼리문을 모르더라도 웹 구축 가능

```bash
$ python manage.py makemigrations polls
```

- 변경사항을  DB에 반영
- DB에 테이블 생성 및 초기화

## 3.2.7 모델에 함수 추가하기

모델에 `__str__` 메서드를 추가

- 관리자 화면이나 쉘에서 객체를 출력할 때 나타날 내용을 결정

```python
from django.db import models

class Question(models.Model):
 	#...
    def __str__(self):
        return self.question_text

class Choice(models.Model):
    #...
    def __str__(self):
        return self.choice_text
```



## 3.2.8 관리자 페이지 확인

관리자 페이지에서 Question 모델을 관리하려면 등록해야함

### polls/admin.py

```python
from django.contrib import admin
from .models import Question

admin.site.register(Question)
```



## 3.2.10 404오류 일으키기

- 파일이 존재하지 않을 때 발생하는 오류

```python
from django.shortcuts import HttpResponse, render, get_object_or_404

def detail(request, pk):
    question = get_object_or_404(Question, pk=pk)
    return render(request, 'polls/detail.html', {'question': question})
```

- Http404를 이용해 상세 정보를 불러올 수 없을 경우 404오류 발생시킬 수 있다.



## 3.2.12 URL Namespace 설정

- 분리된 경로를 만드는 개념

polls/urls.py

```python
app_name = 'polls'
```

html

- `polls:` 추가



## 3.2.14 제네릭 뷰

- 장고에서 미리 준비한 뷰
- 클래스형 뷰

**polls/views.py**

```python
from django.shortcuts import HttpResponse, render, get_object_or_404
from .models import Question, Choice
from django.views import generic

class IndexView(generic.ListView):
    template_name = 'polls/index.html'
    context_object_name = 'latest_question_list'

    def get_queryset(self):
        """Return the last five published questions."""
        return Question.objects.order_by('-pub_date')[:5] 
    
class DetailView(generic.DetailView):
    model = Question
    template_name = 'polls/detail.html'

class Resultsview(generic.DetailView):
    model = Question
    template_name = 'polls/results.html'
```

**polls/urls.py**

```python
from django.urls import path
from . import views

app_name = 'polls'
urlpatterns = [
    path('', views.IndexView.as_view(), name='index'),
    path('<int:pk>', views.DetailView.as_view(), name='detail'),
    path('<int:pk>/results/', views.DetailView.as_view(), name='results'),
    path('<int:pk>/vote/', views.vote, name='vote'),
]
```

- `as_view()` 를 꼭 사용해야 함
