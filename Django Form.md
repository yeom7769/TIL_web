# Django Form

> user에게 에러를 보여주는 기능



## 1. Form 기본 작성

1. app폴더 안에 **`forms.py`** 생성

2. django에서 제공하는 form 사용

   ```python
   from django import forms
   
   class Articleform(forms.Form):
       title = forms.CharField(max_length=10)
       content = forms.CharField(widget=forms.Textarea)
       # textfield 존재 x. 대신 widget 사용
   ```

3. views.py

   ```python
   from django.shortcuts import render, redirect
   from .models import Article
   from .forms import Articleform
   
   def new(request):
       form = Articleform()
       context = {
           'form' : form,
       }
       return render(request, 'articles/new.html', context)
   
   def create(request):
       # 값을 받아서 인스턴스 생성
       form = Articleform(request.POST)
       print('='*30)
       print(form)
   
       # 유효성 검사
       if form.is_valid(): # 설정한 조건들에 부합하는지 확인
           # DB에 저장
           title = form.cleaned_data.get('title')
           content = form.cleaned_data.get('content')
           article = Article(title=title, content=content)
           article.save()
           return redirect('articles:index')
   
       #만약 유효성 검사 통과 못하면 실패에러메세지 보여줌
       context = {
           'form' : form,
       }
       return render(request, 'articles/new.html', context)
   
   
   ```

4. new.html

   ```html
   {% extends 'base.html' %}
   {% block content %}
   <h2>NEW</h2>
   <form action="" method='POST'>
     {% csrf_token %}
     <!--as_p : 태그처럼 보여준다-->
     {{form.as_p}}
     <button>글작성</button>
   </form>
   {% endblock content %}
   ```

   

## 2. ModelForm

>  하나하나 필드를 지정해야함 => 해결하기위해  modelform사용

1. forms.py

   ```python
   class Articleform(forms.ModelForm):
       class Meta: # Meta데이터 저장
           # 세부(모델, 필드)정보 넣음
           model = Article
           #fields = ('title', 'content')
           # 모든 필드 입력받고 싶을 때
           fields = '__all__'
   ```

2. views.py

   ```python
   def create(request):
       # 값을 받아서 인스턴스 생성
       form = Articleform(request.POST)
   
       # 유효성 검사
       if form.is_valid(): # 설정한 조건들에 부합하는지 확인
           # DB에 저장(ModelForm으로 작성했을 때)
           article = form.save() # return은 저장된 data의 인스턴스
           return redirect('articles:index')
   
       #만약 유효성 검사 통과 못하면 실패에러메세지 보여줌
       context = {
           'form' : form,  #form에 에러 내용도 담겨져서 전달됨
       }
       return render(request, 'articles/new.html', context)
   ```

   > 나머지는 위와 동일



## 3. ✨new와 create 합치기

- 링크/주소줄 요청은 모두 **`method='GET'`**
- 나머지는 **`POST`**



1. urls 변경

   ```python
   urlpatterns = [
       path('index/', views.index, name='index'),
       path('create/', views.create, name='create'),
   ]
   ```

2. views.py 에서 new와 create 합치기

   ```python
   def create(request):
       if request.method == 'POST':
           # def create : DB에 저장
           form = Articleform(request.POST)
           if form.is_valid():
               article = form.save()
               return redirect('articles:index')
       else:
           # def new : page를 보여주는
           form = Articleform()
       
       context = {
           'form' : form,
       }
       return render(request, 'articles/new.html', context)
   ```

   

## 4. update, delete

> form 활용하기



1. views.py

   ```python
   def edit(request, pk):
       if request.method == 'POST':
           article = Article.objects.get(id=pk)
           form = Articleform(request.POST, instance=article) # 만약 instance가 없다면 (수정이 아니라)생성이 되기 떄문에
           # 기존의 값이 존재함을 알려줘야지 update를 해줌
           if form.is_valid():
               article = form.save()
               return redirect('articles:detail', article.pk)
       else:
           article = Article.objects.get(id=pk)
           #form = Articleform() 비어있는 form 작성
           form = Articleform(instance=article) #article의 정보를 가져온다.
       context = {
           'article' : article,
           'form' : form,
       }
       return render(request, 'articles/edit.html', context)
   ```

   🙄 **`instance`** 부분 주의!!



2. delete의 기존 방법 문제점

   ```html
   <a href=" {% url 'articles:delete' article.id %}">DELETE</a>
   ```

   > 링크로 연결하는 것은 `GET`방식이므로 데이터를 삭제하는 방식인 `POST`방식과 다름
   >
   > (주소 줄에 요청하면 데이터가 삭제됨 => 말도 안됨)

   html문서에서 아래와 같이 수정!

   ```html
   <form action=" {% url 'articles:delete' article.id %} " method='POST'>
     {% csrf_token %}
     <button>삭제</button>
   </form>
   ```

   

3. delete 함수(views.py)

   ```python
   def delete(request, pk):
       article = Article.objects.get(id=pk)
       if request.method == 'POST':
           article.delete()
           return redirect('articles:index')
       return redirect('articles:detail', article.id)
   ```

   

## 5. form의 action

action에 아무것도 지정하지 않으면 현재위치로 전송하므로

`<form action="" method='POST'>`

로 사용가능함! => 문서가 더욱 간결해지는 장점이 있음

