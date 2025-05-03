# Домашнее задание к занятию «Сетевое взаимодействие в K8S. Часть 2»

## Задание 1. Создать Deployment приложений backend и frontend.

* Создаем Deployment приложения frontend из образа nginx с количеством реплик 3 шт:

[deploy-frontend.yaml](https://github.com/A-Tagir/kubernetes/blob/main/05/deploy-frontend.yaml)

* Создаем Deployment приложения backend из образа multitool:

[deploy-backend.yaml](https://github.com/A-Tagir/kubernetes/blob/main/05/deploy-backend.yaml)

* Создаем сервисы nginx для frontend и multitool для backend:

[service-frontend.yaml](https://github.com/A-Tagir/kubernetes/blob/main/05/service-frontend.yaml)

[service-backend.yaml](https://github.com/A-Tagir/kubernetes/blob/main/05/service-backend.yaml)

* Применяем и видим, что контейнеры и сервисы создались:

![deploy_success](https://github.com/A-Tagir/kubernetes/blob/main/05/Kubernetes01_deploys_created.png)

* Пробуем зайти в контейнеры и проверить доступ друг к другу через сервисы. Из контейнера backend доступен 
  сервис nginx, а доступ из контейнера frontend сервиса multitool проверить не удается, нет пакета curl. 
  Возможно, потому что образ nginx выбран image: nginx:1.14.2

![check_access](https://github.com/A-Tagir/kubernetes/blob/main/05/Kubernetes01_check_access.png)

* Меняем образ nginx:1.14.2 на nginx:latest и видим, что создаются новые контейнеры, а старые после успеха удаляются:

![nginx:latest](https://github.com/A-Tagir/kubernetes/blob/main/05/Kubernetes01_change_nginx_version.png)

* Проверяем снова и видим, что в образе nginx:latest установлен curl, сервис multitool доступен из frontend:

![access_ok](https://github.com/A-Tagir/kubernetes/blob/main/05/Kubernetes01_access_ok.png)

## Задание 2: Создать Ingress и обеспечить доступ к приложениям снаружи кластера.

* Включаем ingress - контроллер:

![ingress_enabled](https://github.com/A-Tagir/kubernetes/blob/main/05/Kubernetes01_enable_ingress.png)

* Создаем Ingress, обеспечивающий доступ снаружи по IP-адресу кластера MicroK8S так, чтобы при запросе только по адресу открывался frontend а при добавлении /api - backend. Параметр host не указываем, так что при любом имени запрос будет обрабатываться:

[ingress.yaml](https://github.com/A-Tagir/kubernetes/blob/main/05/ingress.yaml)

* Проверяем доступ:

![ingress_ok](https://github.com/A-Tagir/kubernetes/blob/main/05/Kubernetes01_ingress_ok.png)

* C хост машины:

![ingress_host_machine](https://github.com/A-Tagir/kubernetes/blob/main/05/Kubernetes01_ingress_host_machine.png)

![ingress_host_machine_api](https://github.com/A-Tagir/kubernetes/blob/main/05/Kubernetes01_ingress_host_machine_api.png)

