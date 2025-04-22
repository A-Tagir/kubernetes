# Домашнее задание к занятию «Kubernetes. Причины появления. Команда kubectl»

## Задание 1. Установка MicroK8S

* Выполняем команду sudo snap install microk8s --classic --channel=1.32
* Проверяем microk8s status --wait-ready

![microk8s status](https://github.com/A-Tagir/kubernetes/blob/main/01/Kubernetes01_mikroK8Sready.png)


* Устанавливаем Dashboard:  microk8s enable dashboard
* Генерируем токен: microk8s kubectl create token default
* Пробрасываем порт nohup microk8s kubectl port-forward -n kube-system service/kubernetes-dashboard 10443:443 --address 0.0.0.0 &
* Dashboard теперь доступен https://172.26.89.194:10443/

![Dashboard](https://github.com/A-Tagir/kubernetes/blob/main/01/Kubernetes01_mikroK8Sdashboard.png)

## Задание 2 Задание 2. Установка и настройка локального kubectl

* Устанавливаем kubctl: sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
* Копируем конфиг: microk8s config > ./.kube/
* проверяем:  kubectl get pods -A

![kubctl ok](https://github.com/A-Tagir/kubernetes/blob/main/01/Kubernetes01_Kubctl_ready.png)

* Подключение к Dashboard  nohup microk8s kubectl port-forward -n kube-system service/kubernetes-dashboard 10443:443 --address 0.0.0.0 &
* https://172.26.89.194:10443/

![Dashboard](https://github.com/A-Tagir/kubernetes/blob/main/01/Kubernetes01_mikroK8Sdashboard.png)
