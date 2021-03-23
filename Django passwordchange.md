## Django passwordchange



## 1. 비민번호 변경

1. urls.py

   `path('password/', views.change_pw, name='change_pw')`



2. views

   ```python
   from django.contrib.auth.forms import PasswordChangeForm
   from django.contrib.auth import update_session_auth_hash
   # 비밀번호 변경시 세션 무효화를 방지하지 위해서!!
   
   def change_pw(request):
       if request.method == 'POST':
           form = PasswordChangeForm(request.user, request.POST)
           if form.is_valid():
               user = form.save()
               update_session_auth_hash(request, user)
               return redirect('accoutns:update')
       else:
           form = PasswordChangeForm(request.user) # 유저정보는 request.user에 저장되어있음
       context = {
           'form':form,
       }
       return render(request, 'accounts/password.html', context)
   ```

   

3. html

   ```html
   <form action="" method='POST'>
   {%csrf_token%}
   {{form.as_p}}
   <button>비밀번호 수정</button>
   </form>
   ```



## 2. snippet 설정

file-> preferences->usersnippets

new global ...

file 이름 설정 => django.json

```
{
	"BASE HTML": {
    "scope": "django-html,html",
    "prefix": "bh",
    "body": [
    
		],
		"description" : ""
	},
}
```



