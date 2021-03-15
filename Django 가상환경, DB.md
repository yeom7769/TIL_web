# django pjt



## 1. 가상환경

> 필요없는 라이브러리 설치 X, 호환성 떄문

1. 폴더 생성 => vs코드 열기
2. 독립된 공간으로 만들기 => python -m venv venv
3. 가상 실행 시키기 => source venv/Scripts/activate
4. 가상환경 끄기 => deactivate



- 가상환경을 킨 상태에서
  1. pip install django
  2. .... 가상환경에서는 라이브러리를 다시 설치해야함
  3. gitignore 작성(django, python...)
  4. README.md 작성



- crud 실행
  1. django-admin startproject crud .(현재 폴더에 project를 만들어라)
  2. pip freeze > requirments.txt => 모든 라이브러리를 오른쪽 파일에 넣어라
  3.  pip install -r requirments.txt => requirments.txt에 있는 모든 라이브러리를 설치해라
  4. python manage.py startapp movies



- db.sqlite를 넘겨주지 않음 => json파일로 관리하자

  1. database는 전달하지 않음 

     왜?? 서로의 database가 서로 충돌하기 때문

  2. dumpdata 활용(dummy)

  3. pyhton manage.py dumpdata (--indent 4) movies.movie => 데이터 뽑음

  4. pyhton manage.py dumpdata (--indent 4) movies.movie > movies.json => 뽑은 데이터를 json파일에 저장

  5. movies 폴더에 fixtures 폴더 생성 -> 받아옴

  6. python manage.py migrate => data 안보여짐=> json 파일 namespace해줘야함(movies폴더 상위폴더 생성)

  7. python manage.py loaddata movies/movies.json => database에 없기 떄문에 load해줘야함

     
