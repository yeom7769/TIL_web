# Django 데이터베이스 관계와 사용자(1:N)



1. 사용자 재정의

   - settings

     ```
     AUTH_USER_MODEL = 'accounts.User'
     ```

     

   - models.py

     ```python
     from django.db import models
     from django.contrib.auth.models import AbstractUser
     
     # Create your models here.
     class User(AbstractUser):
         pass
     ```

     

   - admin.py

     ```python
     from django.contrib import admin
     from django.contrib.auth.admin import Useradmin
     from .models import User
     
     admin.site.register(User, Useradmin)
     ```

     

   - forms.py

     ```python
     class CustomUserCreationForm(UsercreationForm):
         class Meta(UsercreationForm.Meta):
             model = get_user_model()
             fields = UserCreationForm.Meta.fields
     ```

     

2. 유저 모델 참조하기

   - **settings.AUTH_USER_MODEL** : models.py 일 때(return이 문자열)

   - **get_user_models()** : models.py가 아닐 때(return이 객체)

   - user = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)

     =>1번(기본값) => 1(USER 1번을 기본값이 들어감)