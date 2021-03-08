# Django : urls.py, View



## 1. django

> 파이썬 웹 프레임워크

- Dynamic web : 사용자의 **`요청`**에 따라 서버는 추가적으로 일을해서 client에게 서비스(**`응답`**) 
  - static web : 미리 저장된 정적파일(HTML, CSS, JS)을 제공

- web framework : 기본적인 구조, 필요한 코드들은 제공. 개발자달은 좋은 웹 서비스를 만드는 데 집중

- 파이썬으로 작성된 오픈소스 웹 어플리케이션로, **`모델-뷰-컨트롤러`** 모델 패턴을 따르고 있다.

  - 모델-뷰-컨트롤러(MVC) : 소프트웨어 디자인 패턴

  - but, django는 🔥**`MTV(모델 템플릿 뷰)`**



## 2. django 기초

1. 프로젝트 생성 : **`django-admin startproject firstpjt(프로젝트이름)`**

2. 서버 실행 : **`python manage.py runserver`** 

   🙄 manage.py가 있는 폴더로 들어가서 실행시켜야함!!

3. 앱 생성 : **`python manage.py startapp articles(앱이름)`**

4. settings.py에서의 installed_apps 추가

   - 🔥앱이름 추가(등록)

5. language_code : 'ko-kr'

6. time_zone : 'Asia/seoul'



## 3. django 본격

1. firstpjt -> urls.py

   ```python
   from articles import views # views 모듈 가져와
   
   urlpatterns =[
       path('admin/', admin.site.urls),
       path('index/', views.index), # views 모듈에서 index함수 가져와
   ]
   ```

2. articles -> views.py

   ```python
   def index(request): #반드시 첫번째 인자는 요청이 되어야함
       return render(request, 'index.html') #포장하기, 템플릿은 장고에서 지원x
   ```

3.  articles -> **`🔥templates 폴터 생성`** -> index.html 파일생성 -> !+tab



## 4. Template

> Django template language(DTL)

데이터 표현을 제어하는 도구이자 표현에 관련된 로직

django template에서 사용하는 vuilt-in template system

조건, 반복, 변수 치환, 필터 등의 기능을 제공

파이썬에서 실행되는 것 아님!



### 1. DTL Syntax - Variable

> {{ variable }}
>
> dot(.)을 통해 변수에 접근



1. urls.py

   ```python
   from articles import views # views 모듈 가져와
   
   urlpatterns =[
       path('admin/', admin.site.urls),
       path('index/', views.index), # views 모듈에서 index함수 가져와
       path('greeting/', views.greeting), # 새로운 페이지 생성
   ]
   ```

2. views.py

   ```python
   def greeting(request):
       foods = ['apple', 'banana', 'coconut',]
       info = {
           'name': 'harry',
       }
       context = {
           'info': info,
           'foods' : foods,
       }
       return render(request, 'greeting.html', context)
   ```

3. articles -> **`🔥templates`** -> greeting.html 파일생성 -> !+tab

   ```python
   # DTL Syntax - Variable
   # 변수의 값에 접근하기 위해서는 딕셔너리 키를 사용
   <h1> 안녕, 나는 {{ info.name }}.</h1>
   <p> 과일 : {{foods.0}}</p> #apple 출력
   ```

   

### 2. DTL Syntax - Filter

> {{ variable|filter }}
>
> 표시할 변수를 수정할 때 사용



1. urls.py

   ```python
   from articles import views # views 모듈 가져와
   
   urlpatterns =[
       path('admin/', admin.site.urls),
       path('index/', views.index), # views 모듈에서 index함수 가져와
       path('greeting/', views.greeting), # 새로운 페이지 생성
       path('dinner/', views.dinner), # 새로운 페이지 생성
   ]
   ```

2. views.py

   ```python
   import random
   
   def dinner(request):
       foods = ['족발', '피자', '햄버거', '초밥',]
       pick = random.choice(foods)
       context = {
           'pick' : pick,
           'foods' : foods,
       }
       return render(request, 'dinner.html', context)
   ```

3. articles -> **`🔥templates`** -> dinner.html 파일생성 -> !+tab

   ```python
   # DTL Syntax - filter
   # django built in filters 검색하면 나옴
   <h1> 저녁 : {{ pick }}.</h1>
   <p> {{pick}}은 {{pick|length}}글자 </p> # 저녁 글자 수 출력
   ```

   

### 3. DTL Syntax - Tags

> {% tag %}{% endtag %}
>
> 출력 텍스트 만들거나 반복 또는 논리를 수행하여 제어 흐름을 만드는 등 변수보다 복잡한 일들을 수행

1. 위의 dinner 사용

   ```python
   # DTL Syntax - tags
   <ul>
   	{% for food in foods%}
       	<li>{{food}}</li>
       {% endfor %}
   </ul>
   ```

   

### 4. DTL Syntax - Comments

> {# 주석 #} : 한줄 주석
>
> {% comment %}{% endcomment %} : 여러줄 주석



### 5. Template injeritance(상속)

> 재사용성에 초점을 맞춤
>
> 템플릿 상속을 사용하면 사이트의 모든 공통 요소를 포함
>
> {% extends %} : 자식템플릿이 부모 템플릿을 확장한다는 것을 알림. 반드시 <u>템플릿 최상단</u>에 작성!
>
> {% block %}{% endblock %}

firstpjt => base.html 생성

head와 body에 cdn 첨부

1. firstpjt => base.html 생성

   ```html
   {% block content %}
   {% endblock content %}
   ```

   > 어디에 생성하느냐(경로)에 따라 3번에서의 경로 설정 주의 



2. 상속 받을 자식에서

   ```html
   {% extends 'base.html' %}
   {% block content %}
   내용~~
   {% endblock content %}
   ```

   

3. settings.py => templates => DIRS 설정 

   `DIRS = [BASE_DIR / 'firstpjt' / 'templates']`

   *BASE_DIR : 최상위 디렉토리

   

### 6. Django template system (feat. django 설계 철학)

''표현과 로짓을 분리'' : 기본 목표를 넘어서는 기능을 지원하지 말아야한다.

''중복을 배제'' : 템플릿 상속의 기초



## 5. HTML form

> 웹에서 사용자 정보를 입력하는 여러 방식을 제공하고 사용자로부터 할당된 데이터를 서버로 전송

1. html input element

   사용자로부터 데이터를 입력 받기 위해 사용

   핵심 속성

   - name : key , value : value로 `?key=value&key=value` 형태로 전달

2. http(hypertext transfer protocol)

   웹에서 이루어지는 모든 데이터 교환의 기초

   http request method 종류 : GET, POST, PUT, DELETE...

   - GET : 정보를 조회하는데 사용

```python
urlpatterns =[
    path('throw/', views.throw), # 새로운 페이지 생성
]

#===============================================
def throw(request):
    return render(request, 'throw.html')

#==============================================
{% extends 'base.html' %}
{% block content %}
    <h1>THROW</h1>
    <form action="/catch/" method="GET">
    	<label for="message">Throw:</label>
		<input type="text" name="message">
        <input type="submit">
    </form>
{% endblock %}
```

```python
urlpatterns =[
    path('catch/', views.catch), # 새로운 페이지 생성
]

#===============================================
def catch(request):
    message = request.GET.get('massage')
    context = {
        'message':message
    }
    return render(request, 'catch.html',context)

#==============================================
{% extends 'base.html' %}
{% block content %}
    <h1>CATCH</h1>
    <h2>여기서 {{message}} 받음<h2>
{% endblock %}
```



## 6. URL

> app의 view 함수가 많아지면 유지보수가 어려움

이제 각 app에서 urls.py에서 관리하도록하자.

🔥  articles => urls.py 생성

```python
from django.urls import path
from . import views #명시적 상대경로 표현

urlpatterns =[
    path('index/', views.index), 
    path('greeting/', views.greeting), 
    path('dinner/', views.dinner), 
    path('throw/', views.throw),
    path('catch/', views.catch),
]
```



🔥 firstpjt -> urls.py 접근

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls), 
    path('articles/', imclude('articles.urls')),
]
```



- variable routing(동적 라운틴) : 주소 자체를 변수처럼 사용해서 동적으로 주소를 만드는 것

```python
from django.urls import path
from . import views #명시적 상대경로 표현

urlpatterns =[
    path('index/', views.index), 
    path('greeting/', views.greeting), 
    path('dinner/', views.dinner), 
    path('throw/', views.throw),
    path('catch/', views.catch),
    path('hello/<str:name>/', views.hello),
]

#=================================================
def hello(request, name):
    context = {
        'name' : name,
    }
    return render(request, 'hello.html', context)

#==================================================
<h1>HELLO</h1>
<h2>{{name}}님 서버</h2>
```



- naming url patterns

  > 링크에 url을 직접 작성하는 것이 아니라 path() 함수의 name 인자를 정의해서 사용

  ```python
  path('indenx/', views.index, name='index'),
  ```

  ```html
  <a href="{% url 'index' %}">메인페이지</a>
  ```

  

