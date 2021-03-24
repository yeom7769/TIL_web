# Django DB

- Model Relationship1
- a many to one relationship
- comment



# 1. Model Relationship1

- relationship fields
  - 모델 간 관계를 나타내는 필드
  - many to one(1:N)
    - ForeignKey()
  - many to many(M:N)
    - ManyToManyField()
  - one to one(1:1)
    - OneToOneField()



1️⃣ **ForeignKey()**

- 외래 키는 참조하는 테이블에서 1개의 키에 해당하고 참조하는 측의 관계 변수는 참조되는 측의 테이블의 키를 가리킨다.
- 참조하는 테이블의 속성의 행 1개의 값은 참조되는 측 테이블의 행 값에 대응된다.
- 외래 키의 값이 반드시 부모 테이블의 키본 키 일 필요는 없지만 유일해야 함

```python
class Comment(models.Model):
    article=models.ForeignKey(Article, on_delete=models.CASCADE)
    # CASCADE : 부모객체가 삭제 됐을 때 이를 참조하는 객체도 삭제
```

> on_delete : 데이터 베이스의 기능을 보증하기 위해서



### 1.1 댓글 모델 구현

1. models.py

   ```python
   class Comment(models.Model):
       article = models.ForeignKey(Article, on_delete=models.CASCADE)
       content = models.CharField(max_length=200)
   ```
   
2. makemigrations & migrate

3. admin.py에서 admin.site.register(comment) 추가



### 1.2 역참조

1. article.comment_set.all() : 외부 참조에서 댓글 보여줄 수 있음

2. related_name : _set manager를 변경할 이름 설정. M:N 관계에서는 반드시 사용

   모델에서 작성

   ```python
   related_name = 'comments'
   ```

3. forms.py

   ```python
   class CommentForm(forms.ModelForm):
       class Meta:
           model = Comment
           # fields = '__all__'
           exclude=('article',)
   ```


#### 🔥`참조할 때는 .article 역참조할 때는 .comment_set(manager형식!)`



### 1.3 comments crud

1. create

   ```python
   path('<int:pk>/comments/', views.comments_create, name="comments_create")
   ```

2. views

   ```python
   @require_POST
   def comments_create(request, pk):
       article = get_object_or_404(Article, pk=pk)
   	comment_form = CommentForm(request.POST)
       if comment_form.is_valid():
           comment = comment_form.save(commit=False) 
           #인스턴스는 리턴해주지만 데이터베이스에 저장하지 않고 대기해줌
           comment.article = article
           # 혹은
           # comment.article_id = article.pk
           comment.save()
           return redirect('articles:detail', article.pk)
       context = {
           'comment_form' : comment_form
           'article' : article
       }
       return render(request, 'articles/detail.html', context)
       # is_valid는 에러메시지를 포함한 form을 되돌릴 수 있음
       # 에러를 redirect하게되면 에러메시지는 의미가 없어짐
   ```


3. read

   ```python
   def detail(request, pk):
       article = get_object_or_404(Article, pk=pk)
       comments = article.comment_set.all()
   ```

4. delete

   ```python
   path('<int:pk>/comments/<int:comment_pk>/delete', views.comments_delete, name="comments_delete")
   ```

5. views

   ```python
   @require_POST
   def comments_delete(request, pk, comment_pk):
       if request.user.is_authenticated:
           comment = get_object_or_404(Comment, pk=comment_pk)
           comment.delete()
       return redirect('articles:detail', article_pk)
   ```

   



## 2. DB:SQL(ORM) 실습

### 2.1 Customizing authentication

> 프로젝트의 첫 마이그래이트 전에 해줘야함!!!!

1. settings.py

   ```python
   AUTH_USER_MODEL = 'accounts.User'
   ```



2. accounts/models.py

   ```python
   from django.contrib.auth.models import AbstractUser
   
   class User(AbstractUser):
       pass 
   # 중간에 user모델이 불편하거나 마음에 안들면 커스텀을 해야하는데 그 여지를 남겨둠
   ```

   > 상속을 AbstractUser을 상속받는 이유는 베이스유저에는 항목이 적기때문이다.



2. admin.py

   ```python
   from django.contrib import admin
   from django.contrib.auth.admin import UserAdmin
   from .models import User
   
   admin.site.register(User, UserAdmin)
   ```



### 2.2 데이터 초기화

1. 모든 migrations안의 .py 파일 삭제
2. db.sqlite 삭제
3. 처음부터 makemigrations => migrate

4. accounts/forms

   ```python
   from django.contrib.auth.forms import UserCreationForm
   
   class CustomUserCreationForm(UserCreationForm):
   
       class Meta:
           model = get_user_model()
           fields = UserCreationForm.Meta.fields + ('email',)
   ```

   

- 유저 모델 참조하기
  - settings.AUTH_USER_MODEL : models.py에서 유저 모델을 참조할 때
    - user = models.ForeignKey(settings.AUTH_USER_MODEL, on-delete=models.CASCADE)
  - get_user_model() : models.py가 아닌 다른 모든 곳에서 유저 모델 참조할 때







