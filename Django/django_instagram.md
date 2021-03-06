# Django Instagram

Django를 활용하여 Instagram을 만드는 과정을 적어보겠습니다.



## 초기 환경 세팅

```python
$ mkdir instagram
$ cd instagram

$ pyenv virtualenv 3.7.5 instagram-env
$ pyenv local instagram-env

$ pip install 'django<3.0'
$ pip freeze > requirements.txt

$ django-admin startproject config
$ mv config app		# 외부 config 폴더명을 app으로 변경

$ pycharm-community .	# pycharm 실행

$ cd app
$ ./manage.py startapp members
$ ./manage.py startapp posts

pycharm에서 interpreter 설정
```



```
instagram
	ㄴ app 					# 루트폴더 지정!
    						# 폴더에서 마우스 오른쪽 버튼 클릭 Mark Directory as > Source Root
		ㄴ config
		ㄴ members 			# ./manage.py startapp members
		ㄴ posts				# ./manage.py startapp posts
		manage.py
		requirements.txt
	.gitignore
```



```python
# config / settings.py 내 아래 코드 추가

AUTH_USER_MODEL = 'members.User'

INSTALLED_APPS = [
    'members.apps.MembersConfig',
    'posts.apps.PostsConfig',
]
```



## 기본 모델 구성하기

```python
# members > models.py

from django.contrib.auth.models import AbstractUser


class User(AbstractUser):
    pass
```



> **AbstractUser**  
>
> 이 함수를 사용하면 기존의 `auth_user` 테이블에 있던 열들을 전부 유지한 채 새로운 열을 추가할 수 있다. 위 코드를 통해 앞으로는 `auth_user`의 역할을 `User`가 대신하게 될 것이다.



```python
# posts > models.py

from django.db import models
from members.models import User

class Post(models.Model):
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    content = models.TextField(blank=True)
    like_users = models.ManyToManyField(User, through='PostLike', related_name='like_post_set',)
    created = models.DateTimeField(auto_now_add=True)
    
class PostImage(models.Model):
    post = models.ForeignKey(Post, on_delete=models.CASCADE)
    image = models.ImageField()
    
class PostComment(models.Model):
    post = models.ForeignKey(Post, on_delete=models.CASCADE)
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    content = models.TextField()
    
class PostLike(models.Model):
    post = models.ForeignKey(Post, on_delete=models.CASCADE)
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    created = models.DateTimeField(auto_now_add=True)
```

```python
$ ./manage.py makemigrations
$ ./manage.py migrate
```



### Post 모델 admin 등록

```python
# members > admin.py

from django.contrib import admin

from posts.models import Post, PostImage, PostComment, PostLike

@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    pass

@admin.register(PostImage)
class PostImageAdmin(admin.ModelAdmin):
    pass

@admin.register(PostComment)
class PostCommentAdmin(admin.ModelAdmin):
    pass

@admin.register(PostLike)
class PostLikeAdmin(admin.ModelAdmin):
    pass
```



### 관리자 계정 생성하기

```python
$ ./manage.py createsuperuser
$ ./manage.py runserver
```

`localhost:8000/admin` 접속 후 admin 로그인 한 뒤 우리가 바로 위에서 추가한 Post, PostImage, PostComment,PostLike가 목록에 출력되는지 확인하기





## 이미지 파일 경로 설정하기



### 이미지 추가하기

`localhost:8000/admin` 접속 후 `Post images`에 add하여 이미지 추가해보면 소스폴더 근처 어딘가에 이미지가 추가된 것이 보일텐데 우리가 원하는 경로에 이미지를 저장해보자



### 이미지 저장경로 지정하기

```python
# posts > models.py

class PostImage(models.Model):
    post = models.ForeignKey(Post, on_delete=models.CASCADE)
    image = models.ImageField(upload_to='posts/image')			# 추가
```



```python
# config/settings.py

BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
ROOT_DIR = os.path.dirname(BASE_DIR)
MEDIA_ROOT = os.path.join(ROOT_DIR, '.media')
```

위와 같이 설정하면 이미지를 올렸을 때 app 폴더와 동일 위치에  .media 폴더가 생성되고, 우리가 upload_to에서 추가로 경로 설정해준 것과 같이 내부 폴더가 생성된 뒤 이미지가 들어간다.



```python
# config/setting.py

MEDIA_URL = '/media/'
```

> http scheme + HOST + MEDIA _URL + FileField의 실제값
>
> http:// + localhost:8000 + /media/ + posts/images/이미지명.확장자명
>
> DB엔 posts/image/sample.jpeg 들어감



하지만 위와 같이 처리해도 admin에서 image 클릭 시 image가 보여지지 않는다.



```python
# config/urls.py

from django.conf import settings
from django.conf.urls.static import static
from django.contrib import admin
from django.urls import path

urlpatterns = [
    path('admin/', admin.site.urls),
]

urlpatterns += static(
    # prefix='/media/',
    # URL 앞부분이 /media/이면
    prefix=settings.MEDIA_URL,
    # document_root위치에서 나머지 path에 해당하는 파일을 리턴
    document_root=settings.MEDIA_ROOT,
)
```

> 참고: howto/static-files/ (https://docs.djangoproject.com/ko/3.0/howto/static-files/)

위와 같이 코드를 추가해주면 admin 사이트에서 이미지를 클릭했을때 이미지가 나온다. (링크를 설정해준 것)



## Admin 개선

### 언어 및 시간 설정 변경

```python
# config/setting.py

LANGUAGE_CODE = 'ko-kr'

TIME_ZONE = 'Asia/Seoul'
```



**[미션]**

- Model : Post의 `__str__`을 적절히 작성한다.
- Admin : 작성자, 글, 작성시간이 보여지게 한다. (list_display)
- 상세화면에서 PostImage, PostComment를 바로 추가할 수 있도록 한다. (inlines, TabularInline)

참고 : https://docs.djangoproject.com/en/2.2/ref/contrib/admin/



### post화면에서 모두 수정가능하도록 화면 변경

```python
# post/admin.py 해당 영역 수정 및 추가

from django.contrib import admin

from .models import Post, PostImage, PostComment, PostLike

class PostImageInline(admin.TabularInline):
    model = PostImage
    extra = 1
    
class PostCommentInline(admin.TabularInline):
    model = PostComment
    extra = 1
    
@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
	list_display = ('author', 'content', 'created')
    list_display_links = ('author', 'content')
    inlines = [
        PostImageInline,
        PostCommentInline,
    ]  
```



```python
# posts/models.py 해당하는 영역 추가

class Post(models.Model):
    
    def __str__(self):
        return '{author} | {created}'.format(
        author = self.author.username,
    	created = self.created,    
	)
```



> **extra** : 여분으로 표시할 개수를 의미한다.
>
> **TabularInline** : StackedInline 대신 TabularInlined을 사용하면, 관련된 객체는 좀 더 조밀하고 테이블 기반 형식으로 표시된다.
>
> **inlines** : 동일한 페이지에서 수정할 수 있도록 한다.
>
> 기본적으로 Django는 각 객체의 str()을 표시한다. 그러나 개별 필드를 표시할 수 있는 경우 **list_display** admin 옵션을 사용해 객체의 변경 목록 페이지에서 열로 표시할 필드 이름의 튜플을 보여줄 수 있다.



### 유저모델 추가하기

```python
# members/admin.py

from django.contrib import admin

from .models import User

@admin.register(User)
class UserAdmin(admin.ModelAdmin):
    pass
```

admin 페이지 내 유저 모델 관리자에서 한명 추가하고 브라우저 보안탭에서 로그인해보면 로그인이 되지 않는다. 왜냐하면 비밀번호가 해시함수로 한번 처리 되지 않았기 때문이다. (터미널에서 createsuperuser를 사용하면 해시함수로 비밀번호가 처리되는데, 유저 모델 관리자에서는 해당 작업이 자동으로 처리되지 않기 때문이다.)

이 문제를 해결하기 위해서는 makepassword 내장함수를 사용해서 해시함수 처리를 한 뒤 다시 유저 모델 관리자에서 비밀번호를 변경해주어야 로그인이 가능해진다.



> 참고
>
> https://docs.djangoproject.com/en/2.2/topics/auth/
>
> https://docs.djangoproject.com/en/2.2/topics/auth/default/
>



> **쿠키 기반 사용자 세션**
>
> 쿠키 기반으로 사용자의 <u>연결을 유지하는 것</u>(세션)을 말한다. 다시말해, 세션을 유지하기 위해 쿠키라는 저장공간을 사용하는 것을 말한다.
>
> client는 server에 요청을 보내면, server는 client에 응답을 보내는데 이는 1회성으로 응답이 끝나면 사라진다.



> **쿠키(Cookie)의 특징**
>
> - 쿠키는 도메인마다 다르게 가지고 있으며, 키와 값으로 구성되어있다.
> - 쿠키에 담긴 값은 client가 server에 요청 시 무조건 담아서 server에 보낸다.
> - 브라우저에만 존재한다.



> **인증과정**
>
> 1) client가 server에 ID/PW를 보낸다.
>
> 2) 받은 ID/PW를 가지고 USER를 찾는다. (server에는 Usertable에 유저가 저장되어있다.)
>
> 3) 만약 2번에서 받은 ID/PW가 정상적인 정보 전달이면 <u>세션을 유지할 테이블</u>을 만들어준다.
>
> 4) 응답에 세션 value를 보내준다. 이는 client 쿠키에 키와 값으로 저장된다.
>
> 위 과정이 끝나고 난 뒤 client에서 server에 요청을 보낼때는 쿠키의  키:값도 함께 보낸다. 보내는 중간에 인증검사가 이루어진다. sessionId가 일치하면 해당하는 value가 세션테이블에 있는지 확인한다. 있다면 로그인 정보를 유지한다. 그래서 네이버에서 다른 화면으로 가도 로그인 정보가 유지될 수 있는 것이다.



> **추가) 자동로그인 정보**
>
> 로그인 상태 유지 혹은 자동로그인에 체크를 하게되면, ID/PW + 자동로그인할 것인지에 대한 정보도 같이 보내지게 된다. 이는 expire Date를 넘겨주어 timeout이 지정된다.



기타적으로 로그인 인증과 관련된 django 내부 코드에 *`**credentials`*로  적힌 이유는 인증 방법이 여러개이기 때문이다.



## index, login_view의 url, template, view 연결



[미션]

- settings.TEMPLATES의 DIRS에 instagram/app/templates 경로를 추가

- Template: templates/index.html <h1>Index!</h1> URL: '/' , name='index'
- Template: templates/members/login.html POST요청을 처리하는 form 내부에는 input 2개를 가지며, 각각 username, password로 name을 가짐
- URL: /members/login/  (members.urls를 사용, config.urls에 include하여 사용) 
- name: members:login (url namespace를 사용) 
- POST요청시, 예제를 보고 적절히 로그인 처리한 후, index로 돌아갈 수 있도록 한다
- 로그인에 실패하면 다시 로그인페이지로 이동



**settings.TEMPLATES의 DIRS에 instagram/app/templates 경로추가**

app/templates 폴더를 생성합니다.

다음으로는 해당 경로를 연결해주기 위해 config > settings.py로 이동합니다.

```python
# config/settings.py

TEMPLATES_DIR = os.path.join(BASE_DIR, 'templates')

# 아래 코드 부분에 
TEMPLATES = [
	{
        'DIRS': ['TEMPLATES_DIR'],
    }
]
```



**Template: templates/index.html에 <h1>Index</h1> 코드 추가**

```html
<!-- templates/index.html -->

<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h1>INDEX!</h1>
</body>
</html>
```



**URL: '/', name='index' 설정**

```python
# config/urls.py

urlpatterns = [
    path('', index, name='index')
]
```



> **왜 URL은 '/'인데, path에는 ''인가요?**
>
> path('/')로 입력하게되면, localhost:8000//으로 접속하겠다고 설정하는 것과 같습니다.



```python
# config > views.py

from django.shortcuts import render

def index(request):
    return render(request, 'index.html')
```



### Login_view 기능 구현



**Template: templates/members/login.html 만들기**

위에 만들어 놓은 templates 폴더에 members 폴더를 생성하고 그 내부에 login.html을 만들어 줍니다.



**POST요청을 처리하는 form, 내부에는 input 2개를 가지며, 각각username, password로 name을 가짐**

```html
# templates > index.html

<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h1>LOGIN!</h1>
    <form method="POST">
        {% csrf_token %}
        <input type="text" name="username">
        <input type="password" name="password">
        <button type="submit">로그인</button>
    </form>
</body>
</html>
```



**URL: /members/login/ (members.url 사용, config.urls에 include하여 사용)**

```python
# config > urls.py 아래 항목 추가

urlpatterns = [
    path('members/', include('members.urls')),
]
```

> include 함수를 사용하여, config > urls.py와 members > urls.py와 연결시켜준다.



**name: members:login (url namespace를 사용)**

```python
from django.urls import path

from members.views import login_view

app_name = 'members'

urlpatterns = [
    path('login/',login_view, name='login')
]
```



**POST 요청시, 예제를 보고 적절히 로그인 처리 한 후, index로 돌아갈 수 있도록 한다.**

우선 해결에 앞서 `localhost:8000/members/login`에 접속하면 login.html을 render하도록 해보자.

```python
# members > views.py

from django.contrib.auth import authenticate, login
from django.shortcuts import render, redirect


def login_view(request):
    return render(request, 'members/login.html')
```



이제 확인했으니 POST로 받은 정보를 넘겨주어 login 되도록 처리해보자.

```python
# members > views.py

from django.contrib.auth import authenticate, login
from django.shortcuts import render, redirect


def login_view(request):
    if request.method == 'POST':
        username = request.POST['username']
        password = request.POST['password']

        user = authenticate(request, username=username, password=password)

        if user:
            login(request, user)
            return redirect('index')

        else:
            return redirect('index')

    else:
        return render(request, 'members/login.html')
```



```python
# templates/index.html

<h1>Index!</h1>
<div>{{ request.user }}</div>
<div>{{ request.user.is_authenticated }}</div> 
```



```python
# templates/members/login.html

<!doctype html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <h1>Login</h1>
    <form action="" method="POST">
        {% csrf_token %}
        <input name="username" type="text">
        <input name="password" type="password">
        <button type="submit">로그인</button>
    </form>
</body>
</html> 
```



## index 및 login 페이지 레이아웃 및 CSS 구현 (Bootstrap활용)



### Bootstrap 다운로드 및 필요 파일 추가

https://getbootstrap.com/docs/4.3/getting-started/download/ 에서 다운로드

static 폴더 생성 > static 폴더 내 bootstrap 폴더 생성 > bootstrap 폴더 내 `bootstrap.css`와 `bootstrap.css.map` 파일 추가



### static 폴더 연결

```python
# config/settings.py

STATIC_DIR = os.path.join(BASE_DIR, 'static')
STATICFILES_DIRS = [
    STATIC_DIR,
]
```



### base.html 생성하여 템플릿 분리

[미션]

- base.html추가 상단에 {% load static %}
- 정적파일 불러올 때 {% static '경로' %}로 불러옴
- index.html과 login.html이 base.html을 extends하도록 함



```html
# templates/base.html

{% load static %}
<!doctype html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <link rel="stylesheet" href="{% static 'bootstrap/bootstrap.css' %}">
    <title>Instagram</title>
    {% block head %}{% endblock %}
</head>
<body>
    <div id="wrap">
        {% block content %}
        {% endblock %}
    </div>
</body>
</html> 
```



> **{% load static %}** : 템플릿 태그 모듈을 가져온다. 이미 django 내 만들어져있지만, 사용하려고 한다면 적어주어야한다.



```python
# templates/index.html

{% extends 'base.html' %}
{% load static %}

{% block head %}
    <link rel="stylesheet" href="{% static 'css/index.css' %}">
{% endblock %}

{% block content %}
    <div class="container-fluid">
        <div class="mr-auto ml-auto mt-3 col-lg-4 col-md-6 col-sm-8 col-10">
            <div class="card text-center p-4">
                <h1>Instagram</h1>
                <p class="text-secondary">친구들의 사진과 동영상을 보려면 가입하세요</p>
                <button class="btn btn-primary btn-block">Facebook으로 로그인</button>
                <hr>
                <form action="">
                    <div class="form-group">
                        <input type="text" class="form-control" placeholder="휴대폰 번호 또는 이메일 주소">
                    </div>
                    <div class="form-group">
                        <input type="text" class="form-control" placeholder="성명">
                    </div>
                    <div class="form-group">
                        <input type="text" class="form-control" placeholder="사용자 이름">
                    </div>
                    <div class="form-group">
                        <input type="text" class="form-control" placeholder="비밀번호">
                    </div>
                    <button class="btn btn-primary btn-block">가입</button>
                </form>
                <p class="text-secondary">가입하면 Instagram의 약관 데이터 정책 및 쿠키 정책에 동의하게 됩니다</p>
            </div>

            <div class="card text-center p-4 mt-3 mb-3">
                <span>계정이 있으신가요?</span>
                <a href="">로그인</a>
            </div>
        </div>
    </div>
{% endblock %} 
```



```css
# static/css/index.css

body {
    background-color: rgb(250, 250, 250);
} 
```



### login 페이지 레이아웃 및 CSS 구현

```html
# members/login.html

{% extends 'base.html' %}
{% load static %}

{% block head %}
    <link rel="stylesheet" href="{% static 'css/index.css' %}">
{% endblock %}

{% block content %}
    <div class="container-fluid">
        <div class="mr-auto ml-auto mt-3 col-lg-4 col-md-6 col-sm-8 col-10">
            <div class="card text-center p-4">
                <h1>Instagram</h1>
                <form action="">
                    <div class="form-group">
                        <input type="text" class="form-control" placeholder="휴대폰 번호 또는 이메일 주소">
                    </div>
                    <div class="form-group">
                        <input type="text" class="form-control" placeholder="비밀번호">
                    </div>
                    <button class="btn btn-primary btn-block">로그인</button>
                </form>
                <hr>
                <button class="btn btn-block btn-outline-primary mb-3">Facebook으로 로그인</button>
                <a href="">비밀번호를 잊으셨나요?</a>
            </div>

            <div class="card text-center p-4 mt-3 mb-3">
                <span>계정이 없으신가요?</span>
                <a href="">가입하기</a>
            </div>
        </div>
    </div>
{% endblock %} 
```



## index와 login 간 링크 구현



### 로그인 버튼 클릭 시 로그인 페이지로 이동

```python
# templates/index.html

<a href="{% url 'members:login' %}">로그인</a>
```



### 가입하기 버튼 클릭 시 index 페이지로 이동

```python
# members/login.html

<a href="{%url 'index' %}">가입하기</a>
```





## Login form 동작 fix

form에 입력한 값을 보내도록 처리하가 위해, method="POST"로 변경 후 각 항목에 name값 지정

```python
# templates/members/login.html

<form method="POST">
	{% csrf_token %}
    <div class="form-group">
        <input name="username" type="text" class="form-control" placeholder="이메일 주소">
	</div>
    <div class="form-group">
    	<input name="password" type="password" class="form-control" placeholder="비밀번호">
	</div>
</form>
```



확인을 위해 request.user 출력하도록 처리

```python
# templates/index.html

	<div>{{request.user}}</div>
	<div>{{request.user.is_authenticated}}</div>
```





## Post_list url, view, template연결

[미션]

- 로그인 완료 후 이 페이지로 이동하도록 함 
- index에 접근할 때 로그인이 되어 있다면, 이 페이지로 이동하도록 함 
- 로그인이 되어있는지 확인: 
  - request.user.is_authenticated가 True인지 체크 
  - URL:      /posts/  (posts.urls를 사용, config.urls에서 include) 
  - ​              app_name: 'posts' 
  - ​               url name: 'post-list' 
  - ​              -> posts:post-list 
- Template: templates/posts/post-list.html   <h1>Post List</h1>



```python
# config/urls.py path 추가
	path('post/', include('posts.urls')) 
```



```python
# posts/urls.py
from django.urls import path

from . import views

app_name = 'posts'

urlpatterns = [
    path('', views.post_list, name="post-list"),
]
```



```html
# templates/posts/post-list.html

{% extends 'base.html' %}

{% block content %}
	<h1>Post List</h1>
{% endblock %}
```



```python
# posts/views.py

def post_list(request):
    return render(request, 'posts/post-list.html')
```



## 로그인이 되어있는 상태라면 바로 post_list로 이동하도록 처리



```python
# config/views.py

from django.shortcuts import render, redirect

def index(request):
    if request.user.is_authenticated:
        return redirect('posts:post-list')
    return render(request, 'index.html')
```



```python
# members/views.py 아래 부분 수정

	if user:
        login(request, user)
        return redirect('posts:post-list')
    else:
        return redirect('members:login')
```



## post_list에 QuerySet 전달 후 출력



```python
# posts/views.py

from .models import Post

def post_list(request):
    posts = Post.objects.order_by('-pk')
    context = {
        'posts': posts,
    }
    
    return render(request, 'posts/post-list.html', context)
```



```html
<!-- templates/posts/post-list.html -->

{% extends 'base.html' %}

{% block content %}
    <h1>Post List</h1>
    {% for post in posts %}
        <div>{{ post.author }}</div>
        <div>{{ post.created }}</div>
    {% endfor %}
{% endblock %} 
```



## PostLike 기능 구현

**[주어진 조건]**

pk가 pk인 Post와 (변수명 post사용)

request.user로 전달되는 User (변수명 user사용)에 대해

- PostLike(post=post, user=user)인 PostLike객체가 존재하는지 확인한다.
- 만약 해당 객체가 이미 있다면, 삭제한다.
- 만약 해당 객체가 없다면 새로 만든다.
- 완료 후 posts:post-list로 redirect한다.
- URL: /posts/<pk>/like/



```python
# posts/views.py

def post_like(request, pk):
     post = Post.objects.get(pk=pk)
     user = request.user
    
    post_like_qs = PostLike.objects.filter(post=post, user=user)
    
    if post_like_qs.exists():
        post_like_qs.delete()
	else:
        PostLike.objects.create(post=post, user=user)
	return redirect('posts:post-list')
```



**[주어진 조건]**

post_like에 POST요청을 보내는 Form구현 

- input을 갖지 않음 

- csrf_token만 추가 {# {% url %} #}

- 보내는 곳(action)에, url 태그를 적절히 활용 

  ex) /posts/1/like/ 

  ​      /posts/2/like/ 

- Post의 pk에 따라 위와 같은 URL을 동적으로 생성해야 함

- 사용 가능한 변수는 user(USER), post(POST)
- 좋아요 눌린 상태 : PostLike 객체가 존재하는 상태PostLike.objects.filter(user=user,post=post).exist()
- user와 post.like_users를 적절히 이용해서 현재 request.user가 이 Post의 like_users에 포함되는지를 검사해서 아래의 if/else/endif 구문이 동작하도록 작성해본다.
- Template에서의 메서드 콜    post.like_users.all() -> post.like_users.all    호출구문을 제외해야한다.in operator<변수> in <반복가능개체(QuerySet 등)>-> True, False를 반환



```python
# posts/urls.py에 아래 path 추가

urlpatterns = [
    path('<int:pk>/like/', post_like, name'post-like')
]
```



```python
# templates/posts/post-list.html

{% block content %}
	{% for post in posts %}
		<form action="{% url 'posts:post-like' pk=post.pk %}" method="POST">
			{% csrf_token %}
            <div>{{ post.author }}</div>
            <div>{{ post.created }}</div>
            {% if post in user.like_post_set.all %}
            	<div>좋아요 눌림</div>
            {% else %}
            	<div>좋아요 안눌림</div>
            {% endif %}
            
            <button type="submit">좋아요</button>
		</form>
	{% endfor %}
{% endblock %}
```



> {% if post in user.like_post__set.all %} 대신
>
> {% if user in post.like_users. all %}을 사용할 수 있다.



## Signup_view 구현

**[주어진 조건]**

- Template: index.html을 그대로 사용 
- action만 이쪽으로 URL: /members/signup/ 



- User에 name필드를 추가 
- email username name password 를 전달받아, 새로운 User를 생성
- 생성 시, User.objects.create_user() 메서드를 사용한다
- 이미 존재하는 username또는 email을 입력한 경우, "이미 사용중인 username/email입니다" 라는 메시지를 HttpResponse로 돌려줌
- 생성에 성공하면 로그인 처리 후 (위의 login_view를 참조) posts:post-list로 redirect처리



```python
# templates/members/index.html
# 기존 코드에 name 값 추가 / action 경로 설정 

<form action="{% url 'members:signup_view' %}" method="POST">
	{% csrf_token %}
    {{ form }}
    <div>{{request.user}}</div>
    <div>{{request.user.is_authenticated}}</div>
    <input type="email" name="email" class="form-control mt-3" placeholder="이메일 주소">
    <input type="text" name="username" class="form-control mt-3" placeholder="성명">
    <input type="text" name="name" class="form-control mt-3" placeholder="사용자 이름">
    <input type="password" name="password" class="form-control mt-3" placeholder="비밀번호">
    <button type="submit" class="btn btn-primary btn-md mt-3 btn-block">가입</button>
</form>
```

```python
# members/models.py

class User(AbstractUser):
	name = models.CharField('이름', max_length=100)
```

```python
# members/views.py

# 장고 기본유저나 Custom 유저모델 중 사용중인 User 모델을 가져옴
User = get_user_model()

def signup_view(request):

    email = request.POST['email']
    username = request.POST['username']
    name = request.POST['name']
    password = request.POST['password']
    
    
    if User.objects.filter(username=username).exists():
		return HttpResponse('이미 사용중인 username입니다.')
    if User.objects.filter(email=email).exists():
        return HttpResponse('이미 사용중인 email입니다.')
    
    user = User.objects.create_user(email=email, username=username, name=name, password=password)
    
    login(request, user)
        
    return redirect('posts:post-list')
```



로그인이 되었는지 확인하기 위해 출력

```python
# posts/post-list.html

<div>User: {{ request.user }}</div>
<div>Authenticated: {{ request.user.is_authenticated }}</div>
```



## Logout view 구현

**[주어진 조건]**

- GET 요청으로 처리함
- 요청에 있는 사용자를 logout 처리
- django.contrib.auth.logout 함수를 사용한다.



- URL: /members/logout/
- Template: 없음



참고 : https://docs.djangoproject.com/en/3.0/topics/auth/default/#django.contrib.auth.logout



```python
# templates/posts/post-list.html
<a href="{% url 'members:logout' %}">로그아웃</a>
```

```python
# members/urls.py
urlpatterns = [
    path('logout/', views.logout_view, name="logout")
]
```

```python
# members/views.py

def logout_view(request):
    logout(request)
    return redirect("members:login")
```



## Post_create 구현

### 기본구조 구현

**[주어진 조건]**

- URL: 			/posts/create/, name='post-create'
- Template:    /posts/post-create.html
- forms.          PostCreateForm을 사용



참고 : https://docs.djangoproject.com/en/2.2/topics/forms/#building-a-form



```python
# posts/urls.py
path('create/', post_create, name="post-create")
```

```python
# posts/forms.py
from django import forms

class PostCreateForm(forms.Form):
    image = forms.ImageField()
    text = forms.CharField()
```

```python
# posts/views.py
from .forms import PostCreateForm

def post_create(request):
    form = PostCreateForm()
    context = {
        'form':form
    }
    
    return render(request, 'posts/post-create.html', context)
```

```python
# posts/post-create.html

<form action="">
	{{ form }}
    <button type="submit">작성</button>
</form>
```



### 기능 구현 1) 포스트 추가 기능

기능 구현을 확인하기 위해 html내 아래 내용 추가

```python
# posts/post-list.html 내 아래 내용 추가

<a href="{% url 'posts:post-create' %}">포스트 추가</a>
...
{% for post in posts %}
	<div>{{ post.author }}</div>
    <div>{{ post.created }}</div>
    <div>{{ post.content }}</div>
```

```python
# post/views.py

def post_list(request):
    if request.method == 'POST':
        image = request.FILES['image']
        text = request.POST['text']
        
        post = Post.objects.create(
        	author=request.user,
            content=text
        )
        
        post.postimage_set.create(image=image)
        
        return redirect('posts:post-list')
    else:
        form = PostCreateForm()
        context = {
            'form':form,
        }
        return render(request, 'posts/post-create.html', context)
```

```python
# posts/post-create.html

<form action="" method="POST" enctype="multipart/form-data">
	{% csrf_token %}
	{{ form }}
    <button type="submit">작성</button>
</form>
```



> 이미지 파일을 올리고자 할 때 `entype="multipart/form-data"`을 꼭 입력해주어야한다.



### 기능 구현 2) post-list 페이지에서 Post에 연결된 PostImage 출력 기능

```python
# posts/post-list.html

<div>
	{% for post_image in post.postimage_set.all %}
    	<div>{{post_image.image.url}}</div>
        <img srt="{{post_image.image.url}}" alt="">
	{% endfor %}
<div>
```



### 기능 구현 3) 포스트 추가 시 여러장의 사진을 한 번에 올릴 수 있도록 함



참고: https://docs.djangoproject.com/en/3.0/topics/http/file-uploads/



```python
# posts/forms.py

image = forms.ImageField(
	widget=forms.ClearableFileInput(
    	attrs={
            'multiple': True
        }
    )
)
```

```python
# posts/views.py

text = request.POST['text']
images = request.FILES.getlist('image')

post = Post.objects.create(author=request.user, content=text)

for image in images:
    post.postimage_set.create(image=image)
```



> **settings.py의 ALLOWED_HOSTS 설정**
>
> 기본적으로 ALLOWED_HOSTS는 [] 로 입력이 되어있지만 기본 설정이 'localhost', '127.0.0.1'이 들어있다고 생각하면된다.
>
> 그 밖의 ip주소가 입력되길 원한다면 이곳에 추가해주면 된다.



## Comment Create 기능 구현

[주어진 조건]

- URL: /posts/<int:post_pk>/comments/create/

- Template: 없음 (post-list.html내에 Form을 구현) 

- post-list.html 내부에서, 각 Post마다 자신에게 연결된 PostComment목록을 보여주도록 함 

- 보여주는 형식은 아래와 같이 처리

  ```
  <ul>
       <li><b>작성자명</b> <span>내용</span></li>
       <li><b>작성자명</b> <span>내용</span></li>
  </ul>
  ```



```python
# posts/urls.py path추가

path('<int:post_pk>/comments/create', views.comment_create, name="comment-create"),
```

```python
# posts/views.py

def comment_create(request, post_pk):
    if request.method == 'POST':
        post = Post.objects.gets(pk=post_pk)
        content = request.POST['content']
        
        post.postcomment_set.create(
        	author=request.user,
            content=content,
        )
        
        return redirect('posts:post-list')
```

```html
# templates/posts/post-list.html

<div>
    <form action="{% url 'posts:comment-create' post_pk=post_pk %}" method="POST">
    {% csrf_token %}
	<textarea name="content" cols="30" rows="3"></textarea>
	<button type="submit">작성</button>
    </form>
</div>
```





## CommentCreateForm을 사용

[주어진 조건]

- Form 인스턴스를 만드는데, data에 request.POST로 전달된 dict를 입력 / form = CommentCreateForm(data=request.POST) 
- Form 인스턴스 생성시, 주어진 데이터가 해당 Form이 가진 Field들에 적절한 데이터인지 검증



> **form의 is_valid () 메소드 호출**
>
> is_valid ()가 True 인 경우, 이제 validate_data 속성에서 확인 된 모든 양식 데이터를 찾을 수 있습니다. 이 데이터를 사용하여 다음 경로를 알려주는 HTTP 리디렉션을 브라우저로 보내기 전에 데이터베이스를 업데이트하거나 다른 처리를 수행 할 수 있습니다.
>
> True가 아닌 경우 양식이있는 템플릿으로 돌아간다. 돌아간 템플릿 화면에서는 이전에 작성 된 데이터로 HTML 양식이 채워지며 필요에 따라 편집하고 수정할 수 있습니다.

참고 : https://docs.djangoproject.com/en/3.0/topics/forms/#building-a-form-in-django



```python
# posts/forms.py

class CommentCreateForm(forms.Form):
    content = forms.CharField(
    	max_length=10,
        widget=forms.Textarea()
    )
    
    def save(self, post, author):
        return post.postcomment_set.create(
        	author=author,
            content=self.cleaned_date['content']
        )
```

```python
# templates/posts/post-list.html

<form action="{% url 'posts:comment-create' post_pk=post_pk %}" method="POST">
	{% csrf_token %}
    <textarea name="content" cols="30" rows="3"></textarea>
    {{comment_form}}
    <button type="submit">작성</button>
</form>
```

```python
# posts/views.py

def post_list(request):
posts = Post.objects.order_by('-pk')
comment_form = CommentCreateForm()
context = {
    'posts': posts,
    'comment_form': comment_form,
}
return redner(request, 'posts/post-list.html', context)

def comment_create(request, post_pk):
    if request.method == 'POST':
        post = Post.objects.get(pk=post_pk)
        form = CommentCreateForm(data=request.POST)
        
        if form.is_valid():
            form.save(post=post, author=request.user)
        return redirect('posts:post-list')
```





## LoginForm 구현하기

### Login Form추가



#### `authenticate(request=None, **credentials)`

- authenticate()를 사용하여 자격 증명 세트를 확인하십시오.
- 자격 증명은 키워드 인수, 기본 사용자 이름 및 암호로 기본 인증 백엔드에 대해 확인하고 자격 증명이 백엔드에 유효한 경우 User 객체를 반환합니다.
- 자격 증명이 백엔드에 유효하지 않거나 백엔드가 PermissionDenied를 올리면 None을 반환합니다. 

참고 https://docs.djangoproject.com/en/3.0/topics/auth/default/#django.contrib.auth.authenticate



```python
# members/forms.py

class LoginForm(forms.Form):
    username = forms.CharField(
    	widget = forms.TextInput(
        	attrs={
                'class': 'form-control',
                'placeholder': '아이디',
            }
        )
    )
    password = forms.CharField(
    	widget = forms.PasswordInput(
        	attr={
                'class': 'form-control',
                'placeholder': '비밀번호',
            }
        )
    )
```

> **widget**은 type을 설정해주는 것이고, **attr**은 속성을 적용해주는 것이다.

```html
<!-- members/login.html
form으로 변경하기 위해, html 변경 -->

<form action="" method="POST">
    {% csrf_token %}
    // form이 가진 field들을 순회하며, 매 순회마다 .form-group 요소를 만듬
    {% for field in form %}
    	<div class="form-group">{{ field }}</div>
    {% endfor %}
    <button class="btn btn-primary btn-block">로그인</button>
</form>
```

```python
# members/views.py

def login_view(request):
    if request.method == 'POST':
        username = request.POST['username']
        password = request.POST['password']
        user = authenticate(request, username=username, password=password)
        if user:
            login(request, user)
            return redirect('posts:post-list')
        else:
            return redirect('members:login')
	form = LoginForm()
    context = {
        'form': form,
    }
    return render(request, 'members/login.html', context)
```

```python
# posts/urls.py urlpatterns에 path추가

path('<int:post_pk>/comments/create/', views.comment_create, name='comment-create')
```



### LoginForm의 login, clean 메서드 구현

#### `clean()` 

- Field 서브 클래스의 clean() 메소드는 to_python(), validate() 및 run_validators()를 올바른 순서로 실행하고 오류를 전파한다.

- 언제라도 메소드가 ValidationError를 발생시키면 유효성 검사가 중지되고 해당 오류가 발생한다.
- 이 메소드는 clean data를 return한 후 양식의 정리된 data dictionary 에 삽입된다. (Form이 가진 모든 Field들에서 return된 데이터가 key: value로 들어있음)

참고 : https://docs.djangoproject.com/en/3.0/ref/forms/validation/



```python
# members/forms.py

class LoginForm(forms.Form):
    username = forms.CharField(
    	widget = forms.TextInput(
        	attrs={
                'class': 'form-control',
                'placeholder': '아이디',
            }
        )
    )
    password = forms.CharField(
    	widget = forms.PasswordInput(
        	attr={
                'class': 'form-control',
                'placeholder': '비밀번호',
            }
        )
    )
    
    def clean(self):
        username = self.cleaned_data['username']
        password = self.cleaned_data['password']
        user = authenticate(username=username, password=password)
        if not user:
            raise ValidationError('username 또는 password가 올바르지 않습니다')
        return self.cleaned_data
    
    def login(self, request):
        username = self.cleaned_data['username']
        password = self.cleaned_data['password']
        user = authenticate(request, username=username, password=password)
        login(request, user)
```

```python
# members/views.py

def login_view(request):
    if request.method == 'POST':
        form = LoginForm(request.POST)
        if form.is_valid():
            form.login(request)
            return redirect('posts:post-list')
        else:
            return redirect('members:login')
```



## LoginForm의 errors를 템플릿에서 출력

```python
# members/views.py

def login_view(request):
    if request.method == 'POST':
        form = LoginForm(request.POST)
        if form.is_valid():
            form.login(request)
            return redirect('posts:post-list')
	else:
		form = LoginForm()
        context = {
            'form': form
        }
```

>**Rendering from error messages**
>
>참고 : https://docs.djangoproject.com/en/3.0/topics/forms/#rendering-form-error-messages

```html
<!-- members/login.html -->

{% if form.non_field_errors %}
	<ul class="list-unstyled text-danger">
        {% for error in form.non_field_errors %}
        	<li>{{ error|excape }}</li>
        {% endfor %}
    </ul>
{% endif %}
```



## index view 삭제, SignupForm의 field구현 및 HTML에 표시

**[주어진 조건]**

- Template: index.html을 복사해서 /members/signup.html
- URL:  /
- Form: members.forms.SignupForm 생성에 성공하면 로그인 처리 후 (위의 login_view를 참조) posts:post-list로 redirect처리



```python
# config/urls.py
# path('', views.index, name='index'),를 변경

path('', signup_view, name='signup')
```

```python
# members/urls.py
# path('signup/', views.signup_view, name='signup') 삭제
```

```python
# members/views.py

def signup_view(request):
    if request.method == 'POST':
        form = SignupForm(request.POST)
        if form.is_valid():
            form.save()
            return redirect('posts:post-list')
    else:
        form = SignupForm()
        
    context = {
        'form': form,
    }
    return render(request, 'members/signup.html', context
```

```python
# members/forms.py

class SignupForm(forms.Form):
	email = forms.EmailField(
    	widget=forms.EmailInput(
        	attrs={
                'class':'form-control',
                'placeholder':'이메일'
            }
        )
    )    
    name = forms.CharField(
    	widget=forms.TextInput(
        	attrs={
                'class':'form-control',
                'placeholder':'이름'
            }
        )
    )    
    username = forms.CharField(
    	widget=forms.TextInput(
        	attrs={
                'class':'form-control',
                'placeholder':'사용자명'
            }
        )
    )    
    password = forms.CharField(
    	widget=forms.PasswordInput(
        	attrs={
                'class':'form-control',
                'placeholder':'비밀번호'
            }
        )
    )    
    
    def clear(self):
        pass
    
    def save(self):
        pass
```

```html
<!-- templates/index.html을 templates/members/signup.html로 이동 -->

<form action="{% url 'signup' %}" method="POST">
    {% csrf_token %}
    {% for field in form %}
    	<div class="form-group">
        	{{ field }}    
    	</div>
    {% endfor %}
</form>
```

```html
<!-- members/login.html -->

<!-- <a href="{% url 'index' %}">가입하기</a> -->
<a href="{% url 'signup' %}">가입하기</a>
```



### 특정 필드 속성 정리를 활용하여 `def clean()` 분리 및 에러메세지 출력



어떤 필드에 양식과 관련된 유효성 검사를 하고 싶은 경우, 일반적인 Field 클래스에 넣는 것이 아닌 `def clean_recipients(self)`를 정의해 처리한다. 메소드가 바뀌지 않아도, 언제나 새로운 cleaned data를 반환한다.

```python
def clean_recipients(self):
    data = self.cleaned_data['recipients']
    if "fred@example.com" not in data:
        raise forms.ValidationError("You have forgotten about fred!")
    return data
```

참고: https://docs.djangoproject.com/en/3.0/ref/forms/validation/#cleaning-a-specific-field-attribute



```python
# members/forms.py 에서 def clean영역을 clean_username과 clean email로 분리

def clean_username(self):
    username = self.cleaned_data['username']
    if User.objects.filter(username=username).exists():
        raise ValidationError('이미 사용중인 username입니다.')
	return username
        
def clean_email(self):
    email = self.cleaned_data['email']
    if User.objects.filter(email=email).exists():
        raise ValidationError('이미 사용중인 email입니다.')
	return email

def save(self):
    # Form으로 전달받은 데이터를 사용해서 새로운 User를 생성하고 리턴
    return User.objects.create_user(
    	username = self.cleaned_data['username']
        email = self.cleaned_data['email']
        name = self.cleaned_data['name']
        password = self.cleaned_data['password']
    )
```

```python
# members/views.py

def signup_view(request):
    if request.method == 'POST'
    	form = SignupForm(request.POST)
        if form.is_valid():
            user = form.save()
            login(request, user)
            return redirect('posts:post-list')
	else:
        form = SignupForm()
	context = {
        'form': form
    }
    return render(request, 'members/signup.html', context)
```

```html
<!-- templates/members/signup.html -->

{% if field.errors %}
	<ul class="list-unstyled text-danger text-left mt-0 pt-0" style="margin-top: -15px !important"
        {% for error in field.errors %}
        	<li>{{ error }}</li>
		{% endfor %}
	</ul>
{% endif %}

{% if form.non_field_errors %}
	<ul class="list-unstyled text-danger">
        {% for error in form.non_field_errors %}
        	<li>{{ error }}</li>
        {% endfor %}
	</ul>
{% endif %}
```



> 추가학습 필요 : `field.errors`와 `non_field_errors`의 차이





## Post의 해시태그 구현

**[주어진 미션]**

- ManyToMany에서, 필드는 Post에 클래스 작성
- HashTag의 Tag를 담당
- Post입장에서 post.tags.all()로 연결된 전체 Tag를 불러올 수 있어야 한다.
- Tag 입장에서 tags.post.all()로 연결된 전체 Post를 불러올 수 있어야 한다.
- Django admin에서 결과를 볼 수 있도록 admin.py에 적절히 내용을 기록해준다.



- tag 구현하기 TagAdmin 모델 추가하기

> **readonly_fields**
>
> ModelAdmin.fieldsets를 통해 명시적 순서를 정의하지 않고 readonly_fields를 사용하는 경우 편집 가능한 모든 필드 다음에 마지막에 추가됩니다.
>
> (참고 : https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.readonly_fields)

```python
# posts/admin.py PostAdmin에 TagAdmin 추가
# readonly_fields 추가

from .models import Post, PostImage, PostComment, PostLike, Tag

@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ('author', 'content', 'created')
    list_display_links = ('author', 'content')
    inlines = [
        PostImageInline,
        PostCommentInline,
    ]
    readonly_fields = ('tags',)
    
@admin.register(Tag)
class TagAdmin(admin.ModelAdmin):
    pass
```



```python
# posts/models.py에 Post class 수정 및 Tag 클래스 생성

import re
from django.db import models
from members.models import User

class Post(models.Model):
    TAG_PATTERN = re.compile(r'#(\w+)')
    
    author = models.ForeignKey(User, on_delete=models.CASCASE)
    content = models.TextField(blank=True)
    like_users = models.ManyToManyField(
    	User, through='PostLike', related_name='like_post_set',
    )
    
    tags = models.ManyToManyField(
    	'Tag', verbose_name='해시태그 목록', related_name='posts', blank=True
    )
    created = models.DateTimeField(auto_now_add=True)
    
    def __str__(self):
        return '{author} | {created}'.format(
        	author=self.author.username,
            created=self.created,
        )
    
    def save(self,*args,**kwargs):
        super().save(*args, **kwargs)
        tag_name_list = re.findall(self.TAG_PATTERN, self.content)
        tags = [Tag.objects.get_or_create(name=tag_name)[0] for tag_name in tag_name_list]
        self.tags.set(tags)
        
class Tag(models.Model):
    name = models.CharField('태그명', max_length=100)
    
    def __str__(self):
        return self.name
```



### 정규표현식 학습하기

```python
import re
content = '''오늘은 신나는 #Django 웹프로그래밍스쿨 오는날
#WPS#웹프로그래밍스쿨#Django#Python'''

# 해시태그 목록을 문자열 리스트로 가져오기 //  ['Django', 'WPS', '웹프로그래밍스쿨', 'Python']
pattern = re.compile(r'#(\w+)')
list(set(re.findall(pattern, content)))

# 해시태그에 해당하는 문자열을 '(태그: <해당문자열>)'이라는 문자로 치환하기
# "ex: 오늘은 신나는 (태그: Django) 웹프로그래밍스쿨 오는날"
re.sub(pattern,'\g<1>' ,content)

# post에 저장해보기
post = Post.objects.first()
post.content = '#Python #Django'
post.save()

# post에 해시태그를 저장해보기
tag_name_list = re.findall(pattern, post.content)
for tag_name in tag_name_list:
    tag = Tag.objects.get_or_create(name=tag_name)[0]
    post.tags.add(tag)
    
# post에 해시태그 부분을 <a> tag로 바꾸기
post.content = '''신나는 WPS #Python #Django'''
post.save()

import re
p = re.compile(r'#(\w+)')
post.content = re.sub(p, r'<a href="/explore/tags/\g<1>/">\g<1></a>`,post.content)
post.save()          
# 출력되는 값
# '신나는WPS <a href=\"/explore/tags/Python/\">Python</a> <a href=\"/explore/tags/Django/\">Django</a>'
```



이제 위에서 적용해보며 알아본 정규 표현식을 실제 코드에 적용해보기

```python
# posts/forms.py에 text 영역을 widget으로 변경

class PostCreateForm(forms.Form):
    image = forms.ImageField(
    	widget=forms.ClearableFileInput(
            attrs={
                'class': 'form-control-file',
                'multiple': True,
            }
        )
    )
    text = forms.CharField(
        widget=forms.ClearableFileInput(
            attrs={
                'class': 'form-control',
            }
        )
    )
```

```python
# posts/models.py content_html 추가 및 def save 변경

class Post(models.Model):
    
    content = models.TextField(blank=True)
    content_html = models.TextField(blank=True)
        
    def _save_html(self):
        # content 속성의 값을 사용해서 해시태그에 해당하는 문자열을 a태그로 바꾸어준다.
        # return되는 값은 해시태그가 a태그로 변환된 html
        
        self.content_html = re.sub(
        	self.TAG_PATTERN,
            r'<a href="/explore/tags/\g<1>/">#\g<1></a>'
            self.content,
        )
	
    def _save_tags(self):
        # content에 포함된 해시태그 문자열 Tag들을 만들고 자신의 tags Many-to-many field에 추가함
        tag_name_list = re.findall(self.TAG_PATTERN, self.content)
        tags = [Tag.objects.get_or_create(name=tag_name)[0] for tag_name in tag_name_list]
        self.tags.set(tags)
        
	def save(self, *arg, **kwargs):
        self._save_html()
        super().save(*args, **kwrargs)
        self._save_tags()        
```



html코드가 문자열 그대로 출력되는 현상을 해결하기 위해 **safe**를 사용한다.

(참고: https://docs.djangoproject.com/en/3.0/ref/templates/builtins/#safe)

```html
<!-- posts/post-list.html에 글내용 부분을 html코드로 보일 수 있도록 safe 속성 적용 -->

<div>글내용 : {{ post.content_html|safe }}</div>
```



## post_list에서 tag내용으로 filter하는 기능 추가

[주어진 미션]

- Many-to-many에서 필드는 Post에 클래스에 작성
- HashTag의 Tag를 담당
- Post입장에서 post.tags.all()로 연결된 전체 Tag를 불러올 수 있어야 함
- Tag 입장에서 tag.posts.all()로 연결된 전체 Post를 불러올 수 있어야 함
- Django admin에서 결과를 볼 수 있도록 admin.py에 적절히 내용 기록
- 중계모델(Intermediate model)을 사용할 필요 없음



```python
# jupyter notebook

Post.objects.all()
# <QuerySet [<Tag: happy>, <Tag: 태그명>>과 같이 출력됨

Tag.objects.all()
# "Django: 1\n",
# "Python: 1\n" 과 같이 출력됨

for tag in Tag.objects.all():
    result = '{tag}: {posts}'.format(
    	tag=tag.name,
        posts=', '.join([str(post.pk) for post in tag.posts.all()]),
    )
    print(result)

# <QuerySet [{'pk': 1}, {'pk': 12}, {'pk': 13}]> 와 같이 출력됨

# 박보영 이라는 name을 가진 Tag를 자신의 tags 목록에 갖고있는 Post의 경우
# filter의 조건(키워드)명으로
# ManyToMany등, RelatedField의 Forward연결이라면, 해당 필드면 사용
# Backward의 경우 related_name이 지정되어있다면 사용하고, 지정되어있지 않다면 연결되는 모델의 lowercase문자열 (_set이 안붙음)

Post.objects.filter(tags_name__iexact='Django').values('pk')
```

> tags_name__iexact='Django' 는 Django 대소문자 구분없이 검사한다.
>
> tags_name='Django'는 __exact가 생략되어 있는 것으로 대소문자를 구분해 검사한다.



```python
# config/urls.py path 추가

urlpatterns = [
    path('explore/tags/<str:tag>/', post_list, name='post-list-by-tag')
]
```



tag가 들어오면 해당하는 화면으로 이동시키고, 들어오지 않으면 post-list.html로 이동하게 처리

```python
# posts/views.py

def post_list(request, tag=None):
	if tag is None:
        posts = Post.objects.order_by('-pk')
	else:
        posts = Post.objects.filter(tags__name__iexact=tag).order_by('-pk')
        
	comment_form = CommentCreateForm()
    context = {
        'posts': posts,
        'comment_form': comment_form,
    }
    return render(request, 'posts/post-list.html', context)
```

>models.save보다 form.save가 나중에 일어나서 `readonly_fields = ('tags',)`를 해줘야함





## 네이버 로그인 기능 구현

**[OAuth기능(소설로그인) 동작방법 설명]**

User/Consumer/Service Provider 세가지의 제공자가 있다.

User는 네이버로그인을 시도하는 사용자, Consumer은 네이버 아이디와 같이 간편 로그인을 지원하는 쇼핑몰 또는 웹페이지, Provider는 간편 로그인 API를 지원하는 Naver를 의미한다. 각각을 `U`/`C`/`S`라고 칭해보겠다.

1. `U`가 `C`에 `S`를 통한 로그인을 하겠다고 req를 보낸다.
2. `C`가 redirect해서 `S` 사이트로 가라고 알려준다. 이때 callbackURL을 포함한다.
3. `U`는 `S` 사이트에 로그인을 한다. 로그인이 되어있다면 `S`가 요청에 대한 확인을 받는다. 그럼 다시 callbackURl로 이동하는데 그때 callback token을 전달해준다. `S`는 `C`에게 code를 전달한다.
4. `C`는 `S`에게 request access token을 요청한다.

5. `S`는 `C`에게 response access_token을 전달(password나 id를 전달하진 않음)한다.
6. access_token을 받으면 `C`는 다시 `S`에게 유저정보를 달라고 요청하는데 access token을 함께 보낸다. 이것이 key이다. (계정정보를 요청할 수 있는 것이 access token / 매칭이 되는 unique한 값을 만들어서 access token에 담아놓는다.) 
7. `S`는 `C`에게 인증을 거쳐서 User info를 전달한다. 이때 `S`에서  unique한 id를 전달해준다. (`U`가 가진 유일한 id값을 보내주는데, 요청할 때마다 항상 같은 id라 이것으로 사용자를 구분한다. unique한 id는 `S` 에 회원가입을 할 때 이미 만들어져있는 값이다.)



unique id를 받았을 때, Django에서 처리하는 방법은 여러가지이다.

그대로 사용하거나, f_를 앞에 붙여서 저장하거나, type을 나눠 저장한다.



우리는 비밀번호 값이 없어도 네이버에서 인증된 유저라는 것을 확신하면 로그인이 되게 처리해야한다.

`AuthenticationBackend`를 Django가 지원한다. 기본적으로 지원하는 것을 model login이라고 한다.

session과는 별개이다.



> **왜 access token을 바로 주지 않을까?**
>
> ClientID: 공개값 / 네이버 로그인 버튼을 만들 때 사용 / 사용자가 OK해도 token만 돌려준다.
>
> ClientSecret: 비공개값 / oauth2.0/token / token + secret -> access_token



- https://developers.naver.com/main/ 회원가입 
- 애플리케이션 등록 

(애플리케이션 이름: 마음대로 / 사용 API: 네아로 + 제공정보 미선택 / 로그인 오픈  API 서비스 환경: PC 웹 / 서비스 URL:

 http://localhost:8000 / 네이버 아이디로 로그인 Callback URL: http://localhost:8000/members/naver-login/)



https://developers.naver.com/docs/login/api/ 참고하여 구현



페이스북 로그인 버튼을 네이버 로그인 버튼으로 변경

```html
<!-- members/login.html -->

<a href="{{ login_url }}" class="btn btn-block btn-outline-success mb-3">NAVER로 로그인</a>
```



naver login url 추가

```python
# members/urls.py naver login url 추가

urlpatterns = [
    path('naver-login/', views.naver_login, name='naver-login'),
]
```



naver login을 위한 a 태그 href 주소

```python
$ pip install requests
```

```python
# members/views.py

def login_view(request):
    if request.method == 'POST':
        form = LoginForm(request.POST)
        if form.is_valid():
            form.login(request)
            return redirect('posts:post-list')
        else:
            form = LoginForm()
            
	# naver login을 위한 a 태그 href 주소
	login_base_url = 'https://nid.naver.com/oauth2.0/authorize'
    login_params = {
        'response_type': 'code',
        'client_id': '클라이언트 id 입력',
        'redirect_uri': 'http://localhost:8000/members/naver-login/',
        'state': 'RANDOM_STATE,
    }
    login_url = '{base}?{params}'.format(
    	base=login_base_url,
        params='&'.join(f'{key}={value}' for key,value in login_params.items()])
    )
    context = {
        'form': form,
        'login_url': login_url,
    }
    return render(request, 'members/login.html', context)
```



naver login 추가

```python
def naver_login(request):
    code = request.GET.get('code')
    state = request.GET.get('staet')
    
    if not code or not state:
        return HttpResponse('code또는 state가 전달되지 않았습니다.')
    
    token_base_url = 'https://nid.naver.com/oauth2.0/token'
 	token_params = {
        'grant_type': 'authorization_code',
        'client_id': '클라이언트 아이디 입력',
        'client_secret': '클라이언트 시크릿 입력',
        'code': code,
        'state': state,
        'redirectURI': 'https://localhost:8000/members/naver-login/',
    }
    token_url = '{base}?{params}'.format(
        base=token_base_url,
        params='&'.join([f'{key}={value}' for key, value in token_params.items()])
    )
    response = requests.get(token_url)
    access_token = response.json()['access_token']
    print(access_token)

    me_url = 'https://openapi.naver.com/v1/nid/me'
    me_headers = {
        'Authorization': f'Bearer {access_token}',
    }
    me_response = requests.get(me_url, headers=me_headers)
    me_response_data = me_response.json()
    print(me_response_data)

    unique_id = me_response_data['response']['id']
    print(unique_id)

    # n_{unique_id} 의 username을 갖는 새로운 User를 생성
    #  ex) n_11041464
    # 생성한 유저를 login시킴
    # posts:post-list로 이동시킴
    
    naver_username = f'n_{unique_id}'
    if not User.objects.filter(username=naver_username).exists():
        user = User.objects.create_user(username=naver_username)
    else:
        user = User.objects.get(username=naver_username)
    login(request, user)
    return redirect('posts:post-list')
```

>`'state': request.GET['state']`와 `'state': request.GET.get('state')`는 동일한 결과를 반환





## + Cron

특정 작업을 고정된 시간, 날짜, 간격에 주기적으로 실행항 수 있도록 스케줄링하기 위해 cron을 사용한다.

(참고 : https://ko.wikipedia.org/wiki/Cron)



`~/projects/wps12th/instagram/app/manage.py stats`을 터미널에서 입력했을때 우리가 원하는 명령이 실행되도록 만들어보자.

./manage.py 명령어 추가하기 : 우리가 원하는 명령어는 instagram/.media/now.txt를 생성하고, 1분마다 현재시간을 기록한다. 만약 파일이 이미 있다면 다음줄에 기록하고, 파일이 없다면 파일을 생성하고 기록한다.



```python
$ crontab -l	# 작업목록 확인
$ crontab -e	# 수동등록

$ * * * * * ~/.pyenv/versions/wps-instagram-env/bin/python ~/projects/wps12th/instagram/app/manage.py stats
```

> cron을 사용할 때는 전체 경로를 입력해주어야한다.



Django에 맞게 해당 명령어 입력시  실행할 수 있도록 편집해보자

```python
def handle(self, *args, **options):
        now = timezone.now()

        with open(os.path.join(MEDIA_ROOT, 'now.txt'), 'at') as f:
            time_str = f'Now: + {timezone.localtime(now).strftime("%Y-%m-%d %H:%M:%S")}\n'
            f.write(time_str)
```



변경사항 출력해보기

```python
$ tail -f
$ tail -f ../media/now.txt
```



## +. Migrate 

`manage.py makemigrations <app>`

모델 클래스의 변경사항을 migration으로 생성



`manage.py migrate <app> <migration>`

migration에 있는 변경사항을 실제 DB에 적용

migration을 적어줘서 뒤로 돌아가거나 앞으로도 갈 수 있다고 함

단, 뒤로 돌릴 때 데이터도 모두 사라짐에 주의 



`./manage.py showmigrations`

migration 적용 사항 확인