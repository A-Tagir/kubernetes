# Домашнее задание к занятию «Управление доступом»

## Задание 1.  Создайте конфигурацию для подключения пользователя

* Создаю ключ и сертификат. Сертификат и ключ удостоверяющего цента беру из папки /var/snap/microk8s/current/certs:
```
tiger@VM1:~/Kubernetes/09$ openssl genrsa -out netology.key 2048
tiger@VM1:~/Kubernetes/09$ openssl req -new -key netology.key -out netology.csr -subj "/CN=netology/O=view"
tiger@VM1:~/Kubernetes/09$ openssl x509 -req -in netology.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out netology.crt -days 3650
Certificate request self-signature ok
subject=CN = netology, O = view
```
* Добавляю нового пользователя и сертификаты
```
kubectl config set-credentials netology --client-certificate=netology.crt --client-key=netology.key
User "netology" set.
```
* Проверяю  tiger@VM1:~/.kube$ cat config и видим, что пользователь и сертификаты появились в конфигурации:

![.kube/config](https://github.com/A-Tagir/kubernetes/blob/main/09/Kubernetes09-User-OK.png)

* Включаю RBAC microk8s enable rbac
* Создаю роль и binding, применяю.

[role_view.yaml](https://github.com/A-Tagir/kubernetes/blob/main/09/role_view.yaml)

[role_binding.yaml](https://github.com/A-Tagir/kubernetes/blob/main/09/role_binding.yaml)

* Переключаю контекст  kubectl config use-context netology  и проверяю:

![RBAC_OK](https://github.com/A-Tagir/kubernetes/blob/main/09/Kubernetes09-RBAC-OK.png)

* Мы видим, что теперь пользователь netolgy может видеть список pods, но ему недоступен список svc.
