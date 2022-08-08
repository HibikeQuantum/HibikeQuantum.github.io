---
layout:	single
title:	"파이선 웹서버 뭐가 좋을까? Django vs Flask"
date:	2022-01-01
toc: true
category: Python
tags: [Language, Framework, Web, Flask, Django]
excerpt: 
---
 
## 배경
![](/assets/img/TheKOO.png){:.align-center}
지난주 데모데이를 마쳤던 퀴즈플랫폼  [Duckhoogosa🔗](http://duckhoo.site/) 프로젝트는 누구나 쉽게 자신이 좋아하는 분야에 대한 퀴즈를 만들고 풀고 그 결과를 공유할 수 있는 플랫폼이다.
컴포넌트는 레트로한 분위기의 [nes.css](https://nostalgic-css.github.io/NES.css/)를 사용했고 레이아웃은 flex로 직접 작성했다. 클라이언트와 서버 양쪽에서 AWS S3에 접근하여 유저들의 업로드를 처리했다. 또 어뷰징을 막고 유저를 구분하기 위한 인증은 OAuth 토큰과 AWS Cognito를 활용했다.

![](/assets/img/PythonBook.jpeg){:.align-center}

이 프로젝트 설계에서 첨예했던 주제는 API와 AWS 인프라를 컨트롤할 파이선 웹서버의 스택선택이었다. Flask와 Django 둘중에 하난데 뭘 결정해야할지에 대한 정보가 많이 부족했다. 인터넷에 검색하면 막연히 이런 정보는 많이 나온다.

> Django는 딱딱하다. Flask는 가볍다

그런데 아직 경험이 없는 나로서는 이 문장이 도대체 어떤 맥락인지 이해가 안됐다. 나는 먼저 Django를 선택해서 부딪쳐보기로 했고 적응할만한 즈음 [Django-rest-framework-mongoengine](https://github.com/umutbozkurt/django-rest-framework-mongoengine)에 버그가 있어 뒤집어서 최종적으로 Flask와 pymongo를 사용하기로 했다. 그 과정을 이번 글에서 정리하고자 한다.

## 데이터베이스 설정
### **Django는?**
Django 를 설치하고 [튜토리얼](https://tutorial.djangogirls.org/ko/django_start_project/)을 따라가다보면 장고의 특징이 드러나기 시작한다. Skeleton 프로젝트를 생성하면 덩달아 따라오는 빽빽한 setting.py가 바로 그것이다. 그리고 데이터베이스 파트는 이렇게 생겼다.

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'name',
        'USER': 'user',
        'PASSWORD': 'password'
    }
```
이 부분에 장고에서 지원하는 DB Engine과 접속정보를 기입하면 장고는 로드할 때 여기있는 정보를 기반으로 자체적인 백엔드 로직을 구성하여 돌려준다. 여기서 백엔드로직이라 함은 대표적으로 admin 페이지나 회원가입, 읽고 쓰는 기능이다.

![](/assets/img/DjangoAdminPage.png){:.align-center}
Django Admin Page
{: .caption}

Django는 이런 기능을 내장하고 있고 API 리소스 별로 테스트할 수있는 html-UI도 제공한다. 다만 이것들을 사용하기 위해선 위의 Database 항목을 잘 관리해야한다. 이걸 공백으로 두면 에러가 떠서 서버가 켜지지 않는다. 그리고 이걸 지원하는 않는 모듈을 별도로 쓴다면 더미모듈을 설정해줘야한다.
Python에서 MongoDB를 쓴다고 하면 Djongo, Mongo-engine,  [django-rest-framework-mongoengine](https://github.com/umutbozkurt/django-rest-framework-mongoengine) , pymongo 등의 옵션이 있는데 Djongo를 빼면 (적확하게 말하자면 Django가 지정한 방식대로 쓰지 않으면) 이런 어드민기능을 지원하지 않는다. 즉 까다롭게 설정하는대신 덕은 못본다는 것이다. 그래서 Django는 딱딱하다. 자기기능을 다 쓸게 아니라면 사용자를 답답하게 만든다.

### **Flask는?**
```
from pymongo import MongoClient

connection = MongoClient(host,
                         username=user,
                         password=password)

db = connection[Database_name]
commentsCollections = db.comments
```
{:.align-center}
위 코드는 pymongo를 사용해서 컬렉션을 준비시킨 모습이다.
{: captionC}

플라스크엔 `setting.py`가 없다. 그냥 텅텅비어있다. `app.py`를 읽을때 명시적으로 디비를 지정해놓고 필요할때 유저가 명시적으로 지정해서 사용한다. 유저가 지정할 수 있기 때문에 어디서 무엇이 이루어지는지 확실하게 알 수 있다. 그 대신 Flask가 임의로 만들어주는 로직은 없다고 봐도된다.

## 서버 사이드 데이터 플로우
###  Django는?
![](/assets/img/2022-08-08-08-26-28.png)

Django  [created the skeleton project](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/skeleton_website)을 실행했을 때 생성되는 서버사이드 MVC(MTC) 모델인데 이미 쓰여진 파일명대로 따라서 Django가 읽어서 플로우를 진행한다. 
`urls.py → views.py ← template.html` 의 구조로 진행한다.
- urls.py : 어떤 view로 보낼지 결정 (라우팅)
- views.py : 최종 Return ( 컨트롤러)
- template : html 문서 껍데기

```python
from .models import Book
# models에서 들고오는 데이터베이스 ORM객체
  
def index(request):
    Books = Book.objects.all()
    # 쿼리가 된 결과가 담긴다.
    context = {'Books':Books}
    # template에 넘겨주기위해 딕셔너리형태로 바꾼다.
    return render(request, 'store/index.html', context)
    # index.html에 딕셔너리 데이터가 들어가고 그걸 기반으로 렌더된 문서가 클라이언트로 반환된다.
```
{:.align-center}
views.py
{: .captionC}

이 코드를 이해하기 위해선 `index` 함수가 `urls.py`에 등록되어 있기 때문에 라우팅 흐름을 타고 작동하는 관계를 알아야한다. 이런건 코드에 안쓰여져 있다. 고생해서 익혀야 한다.

### Flask는?

```python
@app.route('/', methods=['GET', 'POST'])
def home():
  return flask.render_template('filename.html')
```

플라스크를 어떤 기능을 어떤 파일명에서 어떤 이름으로 구현하라는 약속이 없다. 필요에 따라 구현하고 구현한 만큼 기능한다. 플라스크는 `@view_decorator`로 처리하는 로직이 많다. 덕분에 명시적인 성향이 강해서 한눈에 어떤 로직이 관계되는지 파악할 수 있다. 위는 app 객체에 route를 등록하고 바로 아래에 Response를 반환하는 예제다.

## 미들웨어
### Django는?

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware'
    'my_middleware_directory.MyCheckTokenClass',
]
```
{:.align-center}
setting.py
{: .captionC}

setting.py에서 미들웨어를 등록해놓으면 request할때는 위에서부터 밑으로 적용시키고 response 할때는 아래부터 밑으로 적용해나간다.

![FlaskMiddlewareFlow](/assets/image/FlaskMiddlewareFlow.png)

다음은 Django에서 미들웨어를 구현한 모습이다. 위에 있는
`MIDDLEWARE = [… my_middleware_directory.MyCheckTokenClass]`
의 연장으로 보면 이해하기 쉽다. 쓰겠다고 했으니 구현한거다.

```python
class MyCheckTokenClass:
    METHOD = ('GET', 'POST', 'PUT', 'DELETE')

    def __init__(self, get_response):
        self.get_response = get_response
        self.API_URLS = [
            re.compile(r'^(.*)/api'),
            re.compile(r'^api'),
        ]
        # 메인 로직에서 사용할 미들웨어의 프로퍼티를 지정하는 생성자 __init__ 

    def __call__(self, request):
        response = None
        if not response:
            response = self.get_response(request)
        if hasattr(self, 'process_response'):
            response = self.process_response(request, response)
        return response
        
        # 클래스가 호출되면 함수처럼 실행되는 __call__
        # 여기서는 REQ객체로부터 RES객체를 생성하고 한다.
        # process_response 함수를 통해 전처리가 된 response를 다음 미들웨어로 보낸다. 

    def process_response(self, request, response):
        path = request.path_info.lstrip('/')
        valid_urls = (url.match(path) for url in self.API_URLS)
        
        # url과 메서드를 검사해서 적용 케이스를 분기한다.
        if request.method in self.METHOD and any(valid_urls):
            response.isCheck = True
        else:
            response.isCheck = False

        return response
```

어떤 요청에 대해 위의 미들웨어 로직을 적용 할지에 대해서는 객체를 직접검사하고 리턴을 돌려주는 방식으로 구현된다.

## Flask는?

다음 코드에서 보여주면서 설명
```python
from flask import Flask
from flask_cors import CORS

# 방법 1 미들웨어를 import하고 감싸주면 적용.
app = Flask(__name__)
cors = CORS(app, resources={r"/api/*": {"origins": "*"}})

# 방법 2 전통적인 방법으로 미들웨어 만들기
from werkzeug.wrappers import Request

class Middleware:
    def __init__(self, app):
        self.app = app

    def __call__(self, environ, start_response):
        request = Request(environ)
        print('path: %s, url: %s' % (request.path, request.url))
        return self.app(environ, start_response)

app.wsgi_app = Middleware(app.wsgi_app)

# 방법 3 데코레이터 사용
from functools import wraps

def login_required():
    def _decorated_function(f):
        @wraps(f)
        def __decorated_function(*args, **kwargs):
            if 'logged_in' in session:
                print(session['email'], "session pass")
                return f(*args, **kwargs)
            else:
                print("️ ___no session___")
                return "NO SESSION ERROR"

        return __decorated_function

    return _decorated_function


@app.route('/home')
@login_required()
def home():
    return 'Hello'
```
{:.align-center}

이것 말고도 다양한 방법이 지원되지만 세가지를 예로 들었다. 
1. app을 래핑하는 미들웨어
2. 전통적 (명시적) 미들웨어
3. decorator를 통해 미리 명시적으로 보여주는 방법. 이때 @app.route 가 상단에 있어야한다. Flask의 방법은 Node — express 와 비슷하다는걸 알 수 있다.

## 정리
이렇게 보면 Flask는 `@decorator`로 상당히 많은것을 하는것처럼 보이지만  [flask_restful](https://flask-restful.readthedocs.io/en/latest/) 을 활용하면 Django처럼 별도의 장소에서 라우팅을 정리하고 적용할 함수형 리소스, 클래스형 리소스을 지정하여서 코드를 깔끔하게 관리할 수도 있다.

```python
from flask_restful import Api, Resource

app = Flask(__name__)
api = Api(app)

class AccountImg(Resource):
    def post(self):
        pic = request.get_json()
        img = pic['img']
        usersCollections.update_one({'email': session['email']},
                                    {'$set': {"img": img}})
        return 'ok'
        
api.add_resource(AccountImg, '/account/img')
```
{:.align-center}
flask_restful 기반 API.
{: .captionC}
내가 경험한것을 요약하면 두 프레임워크의 선택기준은 다음과 같다.

<script src="https://gist.github.com/HibikeQuantum/9c9db6b809614c86eb81711ebc034aaf.js"></script>

---

Reference
*  [https://tutorial.djangogirls.org/ko/django_start_project/](https://tutorial.djangogirls.org/ko/django_start_project/) 
*  [how-to-create-a-custom-django-middleware](https://simpleisbetterthancomplex.com/tutorial/2016/07/18/how-to-create-a-custom-django-middleware.html) 
*  [https://oz123.github.io/advanced-python/book/middlewares.html](https://oz123.github.io/advanced-python/book/middlewares.html) 
*  [DJANGO 커스텀 미들웨어 만들기](https://gyukebox.github.io/blog/django-%EC%BB%A4%EC%8A%A4%ED%85%80-%EB%AF%B8%EB%93%A4%EC%9B%A8%EC%96%B4-%EB%A7%8C%EB%93%A4%EA%B8%B0---rest-framework-%EB%A5%BC-%EC%9C%84%ED%95%9C-http-response-formatting/) 
*  [https://github.com/jadetypehoon/duckhoogosa-server-kth](https://github.com/jadetypehoon/duckhoogosa-server-kth) 
*  [https://developer.mozilla.org/ko/docs/Learn/Server-side/Django](https://developer.mozilla.org/ko/docs/Learn/Server-side/Django)