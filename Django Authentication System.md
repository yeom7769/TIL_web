# Django Authentication System

- Authentication System(인증시스템)
- login&logout
- user



## 1. Authentication System(인증시스템)

> 자신이 누구라고 주장하는 <u>사람의 신원</u>을 확인하는 것

- Authorization : 권한, 허가

##### `user object/web request`에 인증 시스템을 알아보자.

django에서는 built-in-form을 제공한다.(회원가입:UserCreationfrom, 로그인:AuthenticationForm ...)



## 2. Login&Logout

1. python manage.py accounts(앱 이름 국룰) => 앱 등록
2. path 설정



1️⃣ 🔥Authentication in Web requests

- **`request.user`**를 제공하며 현재 사용자가 로그인하지 않는 경우 **`AnonymousUser `**클래스의 인스턴스로 설정되며, 그렇지 않으면 **`User`** 클래스의 인스턴스로 설정된다.

✔ 로그인

> 로그인은 Session을 Create하는 로직과 같음

- **`login()`**
  - request 객체와 user 객체를 통해 로그인 저장.
  - 사용자의 id를 세션에 저장

✔ 로그아웃

> 로그인은 Session을 Delete하는 로직과 같음

- **`logout()`**
  - request 객체를 받으면 return이 없음
  - DB의 세션 데이터를 삭제하고 클라이언트 쿠키에서도 sessionid를 삭제



2️⃣ HTTP

- 특징
  - 비연결지향 : 서버는 응답 후 접속을 끊음
  - 무상태 : 접속이 끊어지면 클라이언트와 서버 간의 통신이 끝나며 상태를 저장하지 않음



3️⃣ 쿠키

> 서버가 사용자의 웹 브라우저에 전송하는 작은 데이터 조각

- 동일한 서버에 재 요청 시 저장된 쿠기를 함께 전송
- 클라이언트가 재요청시마다 웹 페이지 요청과 함께 쿠기 값도 같이 전송

- 사용 목적
  - 세션 관리 : 상태를 만들기 위해 작용(공지 하루 안보기, 팝업 체크, 장바구니 등)
  - 개인화 : 사용자 선호, 테마 등의 세팅
  - 트래킹 : 사용자 행동을 기록 및 분석하는 용도

- 라이프타임
  - session cookie : 현재 세션이 종료되면 삭제. 일부 브라우저는 다시 시작할 때 세션 복원을 사용해 계속 지속
  - permanent cookie :  expires 속성에 지정된 날짜 혹은 max-age 속성에 지정된 기간이 지나면 삭제



4️⃣ 세션

- 사이트와 특정 브라우저 사이의 상태를 유지시키는 것
- django 특정 session id를 포함하는 쿠키를 사용해서 각각의 브라우저와 사이트가 연결된 세션을 알아냄



5️⃣ 쿠키&세션

- 쿠키 : 클라이언트 로컬에 파일로 저장
- 세션 : 서버에 저장. 이때 서버에 저장된 세션데이터를 구별하기 위한 session id는 쿠키에 저장(장고는 db에 저장)

✨ **HTTP 쿠기는 상태가 있는 세션을 만들도록 해준다!!**



6️⃣ Login 코드 구현

1. views

   ```python
   from django.contrib.auth import login as auth_login
   from django.contrib.auth.forms import AuthenticationForm
   
   def login(request):
       if request.user.is_authenticated: #로그인된 사용자라면
           return redirect('articles:index')
       if request.method == 'POST':
           form = AuthenticationForm(request, reqest.POST)
           if form.is_valid():
               # 세션 create
               auth_login(reuqest, form.get_user())
               return redirect('articles:index')
       else:
           form = AuthenticationForm()
       context = {
           'form' : form,
       }
       return render(request, 'accounts/login.html', context)
       
   ```

2. login.html

   ```html
   <form action="{% url 'accounts:login' %}" method = "POST">
       {% csrf_token %}
       {{form.as_p}}
       <input type="submit">
   </form>
   ```

   이때, username과 password로 name 자동 지원



#DAY_IN_SECONDS = 86400

SESSION_COOKIE_AGE = DAY_IN_SECONDS



7️⃣ Logout 코드 구현

1. views

   ```python
   from django.contrib.auth import logout
   from django.views.decorators.http import require_POST
   
   @require_POST
   def logout(request):
       auth_logout(request)
       return redirect('articles:index')
   ```

   

2. base.html

   ```html
   <form action="{% url 'accounts:logout' %}" method ="POST">
       {%csrf_token%}
       <button>
           Logout
       </button>
   </form>
   ```



🙄로그인 사용자와 비로그인 사용자의 권한을 나눠줘야함!(접근제한) => 안보인다고해도 주소로 접근이 가능하므로 이를 제한해야함.

- is_authenticated attribute : user은 true, anonymoususer은 false

```html
Hello, {{request.user}}
{% if request.user.is_authenticated%}
	<form action="{% url 'accounts:logout' %}" method ="POST">
        {%csrf_token%}
        <button>
            Logout
        </button>
	</form>
{% else %}
	<a href="{% url 'accounts:login' %}">Login</a>
{%endif%}
```

- login_required decorator : 사용자가 로그인 했는지 확인하는 view를 위한 데코레이터
  - 로그인 된 사용자의 경우 해당 view함수 실행
  - 로그인 하지 않은 사용자는 settings.LOGIN_URL에 설정된 경로로 redirect
  - LOGIN_URL : '/accounts/login/' 기본설정!

```python
from django.contrib.auth.decorators import login_required

@login_required
```

> create, delete, update 세 함수에 적용



이를 통해 주소로 create, delete, update로 접근했을 시, 주소는 /accounts/login/?next=/articles/create/로 됨

👉 직전에 접근할려고 했던 주소가 next라는 파라미터로 표시. 이 때 로그인을 하면 next 파라미터로 redirect

👉 but 이건 우리가 설정해야함.  

```python
return redirect(request.GET.get('next') or 'articles:index')
```



만약 주소로 delete 접근하면 login창띄어지면 next파라미터 생김. 그 후 로그인하면 next파라미터(get방식으로) 요청하는데 이는 @require_POST에 어긋난다. 고로 if request.user.is_authenticated: 조건문으로 로그인 사용자인지 판별한다.

```python
@require_POST
def delete(request, pk):
	if request.user.is_authenticated:
        article = get_object_or_404(Article, pk=pk)
        article.delete()
    return redirect('articles:index')
```





## 3. 회원가입

1. url

   `path('signup/', views.signup, name='singup')`

2. views

   ```python
   from django.contrib.auth.forms import AuthenticationForm, UserCreationForm
   
   def singup(request):
       if request.method == 'POST':
           form = UserCreationForm(request.POST) # 요것은 모델폼
           if form.is_valid():
               user = form.save()
               auth_login(request, user) # 로그인한 상태로 인덱스로 감
               return redirect('accounts:index')
       else:
           form = UserCreationForm()
       context = {
           'form' : form,
       }
       return render(request, 'accounts/signup.html', context)
   ```

   

## 4. 회원탈퇴

1. url

   `path('delete/', views.delete, name='delete')`

2. views

   ```python
   @require_POST
   def delete(request):
       if request.user.is_authenticated:
           request.user.delete()
       return redirect('articles:index')
   ```

   



## 5. 회원정보 수정

1. url

   `path('update/', views.update, name='update')`

2. views

   ```python
   from django.contrib.auth.forms import AuthenticationForm, UserCreationForm, UserChangeForm
   from .forms import CustomUserChangeForm
   from django.contrib.auth.decorations import login_required
   
   @login_required
   def update(request):
       if request.method == 'POST':
           form = UserChangeForm(request.POST, instance=request.user) # 요것은 모델폼
           if form.is_valid():
               form.save()
               return redirect('accounts:index')
       else:
           form = CustomUserChangeForm(instance=request.user)
       context = {
           'form' : form,
       }
       return render(request, 'accounts/update.html', context)
   ```

3. forms.py 생성

   ```python
   from django.contrib.auth.forms import UserChangeForm
   from django.contrib.auth import get_user_model
   # user 클래스는 직접 참조하지 않는다!! get_user_model로 활성화된 user모델을 사용한다!!
   
   class CustomUserChangeForm(UserChangeForm):
       class Meta:
           model = get_user_model()
           fields = ('email', 'first_name','last_name')
   ```

   

## 6. User

1. User objects
   - django 인증 시스템에서는 오직 하나의 User Class만 존재



