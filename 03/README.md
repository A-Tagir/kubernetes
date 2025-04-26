# Домашнее задание к занятию «Запуск приложений в K8S»

## Задание 1. Создать Deployment и обеспечить доступ к репликам приложения из другого Pod

* microK8S и kubectl установлены
* создаю манифест ngnix-deploy.yaml:
  [nginx-deploy.yaml](https://github.com/A-Tagir/kubernetes/blob/main/03/nginx-deploy.yaml)
* Применяем: kubectl apply -f nginx-deploy.yaml
* Проверяем и видим, что запустился 1 контейнер из двух:
![pods_error](https://github.com/A-Tagir/kubernetes/blob/main/03/Kubernetes01_pod_error.png)
* Проверяем логи и видим, что nginx запустился, а multitool нет, поскольку возник конфликт портов:
![multitool_error](https://github.com/A-Tagir/kubernetes/blob/main/03/Kubernetes01_multitool_error.png)
* Правим манифест, в секцию multitool добавляем переопределение порта по-умолчанию 80->8080:
```
env:
          - name: HTTP_PORT
            value: "8080"
```
* Применяем: kubectl apply -f nginx-deploy.yaml
* Проверяем и видим, что оба контейнера запустились, реплика одна.
![pods_ok](https://github.com/A-Tagir/kubernetes/blob/main/03/Kubernetes01_pods_ok.png)
* Согласно заданию, увеличиваем количество реплик до 2-х:  replicas: 2
* Проверяем и видим, что реплик стало две, успешно запущены:
![replicas_2](https://github.com/A-Tagir/kubernetes/blob/main/03/Kubernetes01_replicas_2.png)
* 