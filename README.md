# docker

docker ps  
docker ps -a    //список контейнеров
docker images   //списрк образов
docker diff id_container
docker commit id_container nameImage //коммит контейнера в готовый образ
docker rm   //delete container
docker rmi  //delete image

docker pull python:3  //скачивание образа python3
docker run -it python:3 python //запуск контейнера (+запуск программы внутри)
docker run -it python:3 pip install django

docker run --rm -it name_container bash

#DOCKERFILE

FROM python:3
RUN pip install django
RUN mkdir /data
WORKDIR /data


docker build -t name_container . //создание контейнера из dockerfile
docker run -v 'pwd':/data --rm -it name_container django-admin.py startproject name_project //проброс тома из контейнера в host-систему

sudo chown name:name ./ -R   //меняем владельца папки проекта в host-системе

docker run -p 8000:8000 -v 'pwd':/data --rm -it name_container ./name_project/manage.py runserver 0.0.0.0:8000


#DOCKER_COMPOSE
version '3'
services:
    web:
        build: ./name_project
        command: python ./manage.py runserver 0.0.0.0:8000
        ports:
            -8000:8000
        volumes:
            - ./name_project:/data    

В файле requirements.txt описываем все зависимости
Django~=2.2.5

В dockerfile производим модификацию

FROM python:3
RUN mkdir /data
WORKDIR /data
ADD requirements.txt /data
RUN pip install -r requirements.txt
ADD . /data