# tjmoon update
기초자료
* https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker
* https://www.digitalocean.com/community/tutorials/how-to-scale-and-secure-a-django-application-with-docker-nginx-and-let-s-encrypt

사전준비
* DB서버 : PostgreSQL 설치 및 설정
* 웹서버 : psql 등을 이용하여 Postgresql에 접속 되는지 확인. docker, docker compose 설치.
* env 파일에는 PostgreSQL IP, DB name, DB user, DB user password 설정
* django 가 돌아가는 서버의 IP를 nginx_conf/nginx.conf 에서 지정하는 부분이 있다. (upstream django) nginx 설정파일에서는 이 부분만 확인을 하면 된다.

파일설명
* nginx 컨테이너에서 사용하는 nginx.conf 파일 : nginx_conf/nginx.conf
* django에서 사용하는 static 파일은 nginx 에 올려서 사용을 함. 해당 파일은 ~/docker-django-polls/static


docker compose 이용하여 컨테이너 실행. django 컨테이너에 들어가서 초기화 작업을 진행함.

TZ=Asia/Seoul 환경변수를 이용해서 timezone 설정을 함.

```
docker run -d --rm --name web --env-file env -p 8000:8000 django-polls
docker run -d --rm --name nginx -p 80:80 -p 443:443 nginx:1.21.1\
    -v ~/docker-django-polls/nginx_conf/nginx.conf:/etc/nginx/conf.d/nginx.conf:ro \
    -v ~/docker-django-polls/static:/usr/share/nginx/html/static \
``` 
or docker compose (이미지 없으면 docker build도 진행함)

```
docker compose up -d
docker run -i -t --env-file env django-polls sh
python manage.py makemigrations && python manage.py migrate
DJANGO_SUPERUSER_PASSWORD=xxxxxx ./manage.py createsuperuser \
     --no-input \
     --username=admin \
     --email=example@example.com
exit

docker compose ps 

docker compose logs -f nginx
```

hostname 이나 IP로 접속하여 확인을 해본다. nginx는 80 port, django 는 8000 port에서 돌아간다.
```
curl http://web.example.com/polls/
curl http://web.example.com/admin/

curl http://192.168.33.10/polls/
```

# Django Tutorial Polls App (Dockerized)

This repository contains the complete code for the [Django](https://www.djangoproject.com/) project's [tutorial](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) `polls` app. The code should mirror the code you've written at the end of [Part 7](https://docs.djangoproject.com/en/2.1/intro/tutorial07/). 

In this branch of the repository, the Django project code has been modified to work in a Docker-based environment, as described in [How to Build a Django and Gunicorn Application with Docker](https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker).

Sensitive variables in the `env` file have been scrubbed. Instructions for filling in these configuration variables are available in the accompanying DigitalOcean [tutorial](https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker)

This app is meant to be used as a reference Django app for several DigitalOcean tutorials, and should not be deployed in production.

----

### Quickstart

To learn how to build and run the `polls` app container image, consult [How to Build a Django and Gunicorn Application with Docker](https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker).
