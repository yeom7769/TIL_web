# Django : Model



## 1. DB(DataBase)

> 단일한 데이터에 대한 정보를 가짐 : 사용자가 저장하는 데이터들의 필수적인 필드드로가 동작들을 포함

저장된 **`데이터베이스`**<u>(체계화된 데이터의 모임)</u> 레이아웃(구조)

쿼리 : 데이터를 조회하기 위한 명령어



1. 데이터베이스의 기본 구조
   - 스키마(schema) : 데이터베이스에서 자료의 구조, 표현방법, 관계 등을 정의한 구조
   - 테이블(table)
     - `필드`/컬럼/속성 ->열
     - `레코드`/행/튜플 -> 행
     - **PK(기본키)** : 각 행의 고유값으로 Primary Key로 불린다. 반드시 설정!



2. Model : 웹 어플리케이션의 데이터를 구조화하고 조작하기 위한 도구



## 2. ORM

> 'object-relational-mapping'은 객체 지향 프로그래밍 언어를 사용하여 호환되지 않는 유형의 시스템간의 데이터를 변환하는 프로그래밍 기술. 
>
> 이것은 프로그래밍 언어에서 사용할 수 있는 '가상 객체 데이터베이스'를 만들어 사용함.

🔥 ORM을 사용하는 이유? **`DB를 객체로 조작하기 위해` **



## 3. Model 구현

1. app 폴더 -> models.py 접근

   ```python
   # 모델을 하나의 클래스로 만든다
   from django.db import models
   
   class Article(models.Model):
       # 스키마
       # 클래스 인스턴스 변수 = madols.XXField()
       # 필드명 = 필드타입
       title = models.CharField(max_length=10)
       # max_length : 길이 제한
       content = models.TextField()
       # CharField : 짧은 글자
       # TextField : 큰 글 뭉치들
   ```



2. ✨**Migrations**

   > django가 model에 생긴 변화를 반영하는 방법

   - 명령어
     - 📍`makemigrations` : model을 변경한 것에 기반한 새로운 설계도를 만들 때 사용
     - 📍`migrate` : 마이그레이션을 db에 반영하기 위해 사용. 모델에서의 변경 사항들과 db의 스키마가 동기화를 이룸.
     - sqlmigrate : SQL구문을 보기 위해 사용
     - showmigrations : 프로젝트 전체의 마이그레이션 상태를 확인하기 위해 사용

   ```python
   # 터미널 창에 입력
   $ python manage.py makemigrations
   # migrations 폴더 -> initial.py
   # 확인하기
   
   $ python manage.py migrate
   # db.sqlite -> 오른쪽클릭 -> open database
   ```




3. 생성, 수정 시간 

   ```python
   # 모델을 하나의 클래스로 만든다
   from django.db import models
   
   class Article(models.Model):
       # 스키마
       title = models.CharField(max_length=10)
       content = models.TextField()
       
       # 생성, 수정 시간
       created_at = models.DataTimeField(auto_now_add = True)
       updated_at = models.DataTimeField(auto_now = True)
   ```



✨ 3단계 기억!

1) models.py : model 변경사항 발생

2) python manage.py makemigrations : migrations 파일 생성

3) python manage.py migrate : db적용



## 4. Database API

1. DB API

   > DB를 조작(소통)하기 위한 도구. 사용자가 orm으로 요청

2. 구문

   **`Article.objects.all()`** => class name+manager+queryset api

   - manager : 장고 모델 클래스에 objects라는 manager를 추가
   - queryset : 데이터베이스로부터 전달받은 객체 목록. 데이터베이스로부터 조회, 필터, 정렬 등을 수행할 수 있음(orm에게서 받음)

3. pip install django_extensions (한번만!)

    => setting.py에서의 app_installed에 django_extensions 추가(프로젝트 만들때마다)

4. python manage.py shell / `python manage.py shell_plus`



## 5. CRUD

> 대부분의 컴퓨터 소프트웨어가 가지는 기본적인 데이터 처리 기능인 `Creat, Read, Update, Delete`를 묶어서 일컫는 말

queryset은 리스트처럼 조작가능



1. Creat

   ```python
   article = Article() # 인스턴스 생성
   article.title = 'first'
   article.content = 'django!'
   # 작성버튼 눌러야함
   article.save()
   
   article = Article(title='second', content='django!!')
   article.save()
   
   Article.objects.creat(title='third', content='django!!!')
   # 인스턴스 변수 생성없이 creat 가능 -> save 필요 x
   # 러턴값은 방금저장된 data 인스턴스
   ```



2. Read

    - **`Article.objects.all()`** : 모든 데이터 출력

    - **`Article.objects.get()`** : 무언가를 수정하거나 삭제할 때, 한 값을 가져오는 것

      ```python
      class Article(models.Model):
          title = models.CharField(max_length=10)
          content = models.TextField()
          created_at = models.DataTimeField(auto_now_add = True)
          updated_at = models.DataTimeField(auto_now = True)
      
          def __str__(self):
              return self.title
      
          Article.objects.get(pk=1)
          # first
          # 위의 타이틀을 리턴하기 때문
          # 만약 객체가 없으면 DoesNotExist 에러 발생
          # 만약 객체가 여러개일 경우 MultipleObjectsReturned 에러 발생
          # 즉, pk 조회만 사용해라!!
      ```

   - filter() : 지정된 조건에 포함된 모든 정보를 출력

     ```python
     Article.objects.filter(content = 'django!')
     
     # 조건
     Article.objects.filter(content__contains='!') # !가 포함되어 있는 content만 출력
     Article.objects.filter(pk__gt=1) # pk가 1보다 크거나 같은 것만 출력
     ```

     

3. update : 수정

   ```python
   article = Article.objects.get(pk=1)
   article.title
   #'first'
   
   article.title = 'byebye'
   article.title
   #'byebye'
   
   article.save() # 저장 꼭 해줘야함!!
   ```

   

4. delete : 삭제

   ```python
   article.delete()
   # 첫번째 요소 삭제
   # 삭제된 상태에서 새로운 요소를 추가하면 어디에 추가 될까? => 마지막에 추가!!
   ```

   

## 6. Admin site

1. Automatic admin interface

   사용자가 아닌 서버의 관리자가 활용하기 위한 페이지

   django.contrib.auth 모듈에서 제공

   record 생성 여부 호가인에 매우 유용함.



2. 방법

   - admin.py -> 모델 등록

     ```python
     from .models import Article
     
     # admin 사이트에 등록하겠다.
     admin.site.register(Article)
     ```

   - python manage.py createsuperuser -> password 설정

   - 커스텀하기

     ```python
     from .models import Article
     
     class ArticleAdmin(admin.ModelAdmin):
         list_display=('pk', 'tilte', 'content','created_at', 'updated_at')
     
     admin.site.register(Article, ArticleAdmin)
     ```

     

## 7. DB를 서버에 출력하기

1. view.py

   ```python
   from . models import Article
   
   def index(request):
       # 모든 게시글 조회
       articles = Article.objects.all()
       context ={
           'articles' : articles,
       }
       return render(request, 'index.html', context)
   ```

   

2. index.html

   ```django
   {%article for in articles %}
   	{{ article.pk }}
   	{{ article.title }}
   	{{ article.content }}
   ```

   