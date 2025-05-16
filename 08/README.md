# Домашнее задание к занятию «Конфигурация приложений»

## Задание 1. Создать Deployment приложения и решить возникшую проблему с помощью ConfigMap. Добавить веб-страницу

* Создаю Deployment приложения, состоящего из контейнеров nginx и multitool:

[nginx-deploy-cm.yaml](https://github.com/A-Tagir/kubernetes/blob/a0ac159dca814c40db3744aa998580af23a2349b/08/nginx-deploy-cm.yaml)

* Применяем и видим ошибку:

![Multitool_bind_error](https://github.com/A-Tagir/kubernetes/blob/main/08/Kubernetes08-multitool-bind_error.png)

* Создаем ConfigMap, чтобы переопределить порт 80 на 8080:

[configmap-mt.yaml](https://github.com/A-Tagir/kubernetes/blob/main/08/configmap-mt.yaml)

* Добавляем в Deploy переопределение порта:

[nginx-deploy-cm.yaml](https://github.com/A-Tagir/kubernetes/blob/9eba85ccdf0a1f3bb78bb4be924fd86c0fb49b9f/08/nginx-deploy-cm.yaml)

* Применяем 
```
kubectl apply -f configmap-mt.yaml
kubectl apply -f nginx-deploy-cm.yaml
```
* Проверяем и видим, что порт переопределен:

![config-map-ok](https://github.com/A-Tagir/kubernetes/blob/main/08/Kubernetes08-multitool-configmap_ok.png)

* Создаем веб-страницу и подключаем с помощью configMap:

[configmap-nginx.yaml](https://github.com/A-Tagir/kubernetes/blob/main/08/configmap-nginx.yaml)

[nginx-deploy-cm.yaml](https://github.com/A-Tagir/kubernetes/blob/ef6ba40dc834c9435f447b3a529db3437a406b95/08/nginx-deploy-cm.yaml)

* Создаем сервис:

[nginx-service-nport.yaml](https://github.com/A-Tagir/kubernetes/blob/main/08/nginx-service-nport.yaml)

* Применяем и проверяем:

![html_ok](https://github.com/A-Tagir/kubernetes/blob/main/08/Kubernetes08-html_ok.png)

* Видим, что index.html создался в нужной директории, а страница открывается через service.

## Задание 2. Создать приложение с вашей веб-страницей, доступной по HTTPS

* Создаем self-signed сертификат для домена netotest.local (домен должен совпадать в ingress! иначе TLS сертификат будет заменен на default fake kubernetes)
```
 openssl req -new -x509 -days 9999 -nodes -out cert.pem -keyout cert.key
```
* Создаем Secret
```
 kubectl create secret tls nginx-web --cert=./cert.pem --key=./cert.key
secret/nginx-web created
tiger@VM1:~/Kubernetes/08$ kubectl get secret
NAME        TYPE                DATA   AGE
nginx-web   kubernetes.io/tls   2      11s
```
* Добавляем в hosts строку: 172.26.89.194   netotest.local
* Создаем Ingress:

[ingress.yaml](https://github.com/A-Tagir/kubernetes/blob/main/08/ingress.yaml)

* Создаем Service:

[nginx-service-tls.yaml](https://github.com/A-Tagir/kubernetes/blob/main/08/nginx-service-tls.yaml)

* Deploy используем прежний:

[nginx-deploy-cm.yaml](https://github.com/A-Tagir/kubernetes/blob/main/08/nginx-deploy-cm.yaml)

* Применяем и проверяем:

![tls_ok](https://github.com/A-Tagir/kubernetes/blob/main/08/Kubernetes08-tls_ok.png)

- Видим, что отвечает правильная страница, а TLS-сертификат применился правильный.

