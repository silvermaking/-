# 2 장고 시작하기

## 장고 프로젝트 생성
```bash
$ django-admin startproject config .
```



## DB 생성

```bash
$ python manage.py migrate
```

- DB를 초기화하면서 DB 파일을 생성
- dq.sqlite3 파일이 생성됨



## 프로젝트 구성

### config 폴더

- 프로젝트 설정 파일과 웹 서비스 실행을 위한 파일들이 있다. 
- 프로젝트 생성 후에 변경하려면 매우 귀찮다.



- _init__.py 

  - 파이썬 2.x 버전과의 호환을 위해 만들어진 빈 파일
  - 3.x 대에서는 불필요
  - 지워도 지장 x

- settings.py 

  - 프로젝트 설정에 관한 다양한 내용

- urls.py

  - 특정 기능을 수행하기 위해 접속하는 주소인 url를 설정하는 파일
  - 프로젝트 내 urls 파일이 **최초로 탐색되는 기준 url 파일**

  - settings.py에서 기준 url파일 변경 가능

- wsgi.py

  - 웹 서비스를 실행하기 위한 WSGI 관련 내용이 들어있는 파일
  - 변경할 일은 거의 없음

- manage.py
  - 장고의 다양한 명령어를 실행하기 위한 파일
  - 임의로 변경하면 안 됨



## 디자인 패턴과 MTV

### 2.4.1 디자인 패턴이란?

