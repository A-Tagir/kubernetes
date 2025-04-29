# Домашнее задание к занятию «Сетевое взаимодействие в K8S. Часть 1»

## Задание 1. Создать Deployment и обеспечить доступ к контейнерам приложения по разным портам из другого Pod внутри кластера

* Создаю Deployment приложения, состоящего из двух контейнеров (nginx и multitool), с количеством реплик 3 шт:

[nginx-multitool-deploy.yaml](https://github.com/A-Tagir/kubernetes/blob/main/04/nginx-multitool-deploy.yaml)

* Создаю Service, который обеспечит доступ внутри кластера до контейнеров приложения из п.1 по порту 9001 — nginx 80, по 9002 — multitool 8080:

[nginx-multitool-service.yaml](https://github.com/A-Tagir/kubernetes/blob/main/04/nginx-multitool-service.yaml)

* Создаю отдельный Pod с приложением multitool:

[multitool.yaml](https://github.com/A-Tagir/kubernetes/blob/main/04/multitool.yaml)

* Применяю:
![Apply](https://github.com/A-Tagir/kubernetes/blob/main/04/Kubernetes01_Apply.png)

* Видим, что Deploy применился, создалось 3 реплики. Сервис запущен, Pod multitool поднялся.
* Теперь проверяем доступность сервиса внутри ноды:
  
![Nginx_ok](https://github.com/A-Tagir/kubernetes/blob/main/04/Kubernetes01_nginx_ok.png)

## Задание 2. 