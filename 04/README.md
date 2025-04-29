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

## Задание 2. Создать Service и обеспечить доступ к приложениям снаружи кластера

* Создаю Service типа NodePort:
  
[nginx-service-nport.yaml)](https://github.com/A-Tagir/kubernetes/blob/main/04/nginx-service-nport.yaml)

* Применяю. Сначала был выбран nodePort: 9003 и при запуске получил ошибку:
  ```
   kubectl apply -f nginx-service-nport.yaml
   The Service "nginx-nport" is invalid: spec.ports[0].nodePort: Invalid value: 9003: provided port is not in the valid range. The range of valid ports is 30000-32767
  ```
* Поправил nodePort: 30001 (При необходимости NodePort range можно переопределить, но сейчас не принципиально):
* kubectl apply -f nginx-service-nport.yaml:

![nginx_nodeport_ok](https://github.com/A-Tagir/kubernetes/blob/main/04/Kubernetes01_nginx_nodeport.png)

* Видим, что с nod-ы curl localhost:30001 успешен.
* Пробуем с хост-машины Windows:
![nodeport_host-machine](https://github.com/A-Tagir/kubernetes/blob/main/04/Kubernetes01_nginx_nodeport_host-machine.png)
