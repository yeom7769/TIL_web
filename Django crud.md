# Django 실습



## 1. URL namespace

urls.py 에 app_name을 지정해주면 관리하기 편해짐

```python
app_name = '어플리케이션 이름'
# {% url '별명 '%} 사용 불가!
# => {% url 'app_name:path_name' %}
```



## 2. Template namespace

1. 문제 원인
   - 장고는 INSTALLED_APPS에 등록된 순서대로 TEMPLATE 파일 목록을 찾기 때문에 어떤 어플리케이션의 template파일인지 구분 할 수 없다
   - 다른 경로로 요청을 했지만 같은 이름의 템플릿 파일의 소속 어플리케이션을 구분 할 수 없기 대문에 같은 화면이 보여지는 문제점이 발생
2. 해결 방법
   - templates 폴더 내부에 어플리케이션 이름으로 폴더를 하나 더 작성 한다.
   - views의 경로 또한 수정!!



## 3. form의 action

action 지정을 url로 해주면 해당 사이트로 이동함!

```django
{% block content %}
<form action="https://www.google.com/search" method="GET">
  <input type="text" name="q">
  <button>검색</button>
</form>
{% endblock content %}
```



## 4. redirect

views에서 주소로 return하고 싶을때

```python
from django.shortcuts import render, redirect
from .models import Article

# Create your views here.
def create(request):
    title = request.GET.get("title")
    content = request.GET.get("content")

    article = Article.objects.create(title=title, content=content)
    return redirect('articles:index')
```



## 5. http method

1. get : 데이터조회(내놓으라 하는 것)
   - 어떤 걸 주라고 클라이언트가 서버에 요청하는 것
   - 제출된 데이터를 문자열로 보으고 모아진 문자열들을 URL만들 때 사용함.
   - 시스템의 상태에 영향을 주지 않는 요청을 처리할 때만 사용
   - URL에 보여짐!!(키와 값으로) => 패스워드 폼으로 적합하지 않음.
   - 웹 검색 폼에는 적합!
2. post : 데이터 수정, 생성, 삭제(받는 것)
   - 우리가 입력받은 걸 db에 저장하는 것
   - 시스템의 상태를 바꾸기 위한 요청을 받을 때
   - 📌`{% csrf_token %}` 무조건 적어줘야함!! 해킹 방지 위해(폼테그 안에 작성)



## 6. 디테일

> variable rountiong으로 설정

```python
from django.urls import path
from . import views

app_name = 'articles'
urlpatterns = [
    path('index/', views.index, name='index'),
    path('new/', views.new, name='new'),
    path('create/', views.create, name ='create'),
    path('detail/<int:id>', views.detail, name='detail'),
]

#==============================================================
def detail(request, id):
    article=Article.objects.get(pk=id)
    context = {
        'article' : article,
    }
    return render(request, 'articles/detail.html', context)
    
#===============================================================
# index page
{% extends 'base.html' %}
{% block content %}
{% for article in articles %}
  <p>title : <a href="{% url 'articles:detail' article.id %}">{{article.title}}</a></p>
  <hr>
{% endfor %}
{% endblock content %}

# detail.html
{% extends 'base.html' %}
{% block content %}
<p>{{article.id}}번 글</p>
<p>제목 : {{article.title}}</p>
<p>내용 : {{article.content}}</p>

<a href="{% url 'articles:index' %}">목록으로</a>
{% endblock content %}
```



if python파일에서는 `,`로 구분

```python
def create(request):
    title = request.POST.get("title")
    content = request.POST.get("content")

    article = Article.objects.create(title=title, content=content)
    return redirect('articles:detail' , articles.id)
```



## 7. 수정(update)

```python
path('edit/<int:id>', views.edit, name='edit'),
path('update/<int:id>', views.update, name='update'),

#=======================================================
def edit(request, id):
    article = Article.objects.get(pk=id)
    context={
        'article' : article,
    }
    return render(request, 'articles/edit.html', context)

def update(request, id):
    article = Article.objects.get(pk=id)
    article.title = request.POST.get('title')
    article.content = request.POST.get('content')
    article.save()

    return redirect('articles:detail', article.id)
```

