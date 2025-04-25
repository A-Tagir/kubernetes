# Домашнее задание к занятию «Базовые объекты K8S»

## Задание 1. Создать Pod с именем hello-world

* microK8S и kubectl установлены
* создаю манифест hello.yaml:
  [hello.yaml](https://github.com/A-Tagir/kubernetes/blob/main/02/hello.yaml)
* Применяю: kubectl apply -f hello.yaml
* Проверяю:

![get pods](https://github.com/A-Tagir/kubernetes/blob/main/02/Kubernetes01_Echoservice_pods.png)

* Далее kubectl port-forward pod-echoserver 8080:8080
* Проверяем:  links 127.0.0.1:8080

![echo_ok](https://github.com/A-Tagir/kubernetes/blob/main/02/Kubernetes01_Echoservice_ok.png)

## Задание 2. 

* Создаю Pod:

[netology-web.yaml](https://github.com/A-Tagir/kubernetes/blob/main/02/netology-web.yaml)

* Применяю: kubectl apply -f netology-web.yaml
* Создаю сервис:

[netology-service.yaml](https://github.com/A-Tagir/kubernetes/blob/main/02/netology-service.yaml)

* Применяю: kubectl apply -f netology-service.yaml
* Проверяю:
![get_pod_svc](https://github.com/A-Tagir/kubernetes/blob/main/02/Kubernetes01_get_svc.png)
* Пробрасываю порт: kubectl port-forward service/netology-web 8080:80

* Подключаюсь:

![Service_ok](https://github.com/A-Tagir/kubernetes/blob/main/02/Kubernetes01_service_netology-web.png)
