# Django decorator/static



## 1. decorators

> 함수에 기능을 추가하고 싶을 때, 해당 함수를 수정하지 않고 기능을 `연장`하게 해주는 함수



1. Allowed HTTP methods

   - 요청 메서드에 따라 view함수에 대한 엑세스를 제한.

   - 요청이 조건을 충족시키지 못하면 httpresponsenotallowed을 반환

   - require_http_methods(), require_post(), require_safe()

     

2. import(views에서 클래스 지정처럼 활용)

   **`from django.views.decorators.http import require_GET, require_POST, require_http_methods`**

   @require_http_method(['GET', 'POST'])



## 2. Static

> 웹사이트의 구성 요소 중에서 img, css, js파일과 같이 해당 내용이 고정되며 응답을 할 때 별도의 처리 없이 파일 내용을 그대로 보여주면 되는 파일
>
> 즉, 사용자의 요청에 따라 내용이 바뀌지 않음!

1. load static

   ```django
   {% load static %}
   ```

2. 기본 static 경로

   - app_name/static/

   

3.  static files in settings.py

   - STATIC_ROOT : 배포시 필요. 모든 정적 파일을 한 곳에 모아넣는 경로
   - STATIC_URL : 정적 파일을 참조 할때 사용할 URL
   - STATICFILES_DIRS : 추가적인 정적 파일 경로

4. 실습

   - base.html

     ```django
     <!DOCTYPE html>
     <html lang="en">
     <head>
       <meta charset="UTF-8">
       <meta http-equiv="X-UA-Compatible" content="IE=edge">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>Document</title>
       {% block css %}
       {% endblock css %}
     </head>
     ```

   - index.html

     ```django
     {% extends 'base.html' %}
     {% load static %}
     {% block css %}
       <link rel="stylesheet" href="{% static 'articles/css/index.css' %}">
     {% endblock css %}
     
     {% block content %}
     <h2 class="blue">INDEX</h2>
     <img src="{% static 'articles/img/캡처.PNG' %}" alt="펭수얼굴">
     <h3><a href="{% url 'articles:create' %}">NEW</a></h3>
     {% endblock content %}
     ```

   - static/articles/css/index.css 폴더와 파일 생성

     ```css
     .blue {
       color:blue
     }
     ```

     

## 3. Media

> 사용자가 웹에서 업로드 하는 정적 파일
>
> 즉, 유저가 업로드하는 파일



1. filefield, imagefield를 사용 단계

   - settings.py에서 MEDIA_TOOT, MEDIA_URL 설정

     🔥 업로드 파일은 데이터베이스에 저장하지 않음(데이터베이스에 저장되는 것은  파일의 경로)

     ```python
     MEDIA_TOOT = BASE_DIR/'media'
     MEDIA_URL = '/media/'
     ```

     - 예로 이미지 파일을 추가하면 아래와 같음. 

       📍 enctype 설정! => 이미지 파일을 받아온다.

       ```django
       <form action="" method='POST'  enctype="multipart/form-data">
         {% csrf_token %}
         {{form.as_p}}
         <button>글작성</button>
       </form>
       ```

     - views.py  에서 **`request.FILES 추가!!`**

       

   - 업로드 된 파일의 상대 URL경로를 django가 제공하는 url atribute 사용가능

     ```python
     from django.contrib import admin
     from django.urls import path, include
     from django.conf import settings
     from django.conf.urls.static import static
     
     urlpatterns = [
         path('admin/', admin.site.urls),
         path('articles/', include('articles.urls')),
     ] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
     
     ```

     

2. 팁

   - upload_to

     > `ImageField`의 기본 인자이다.
     >
     > 이를 지정하면 미디어 폴더 내부에 하위 루트를 정할 수 있음.

     - models.py

       

