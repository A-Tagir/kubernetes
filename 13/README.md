# Домашнее задание к занятию «Как работает сеть в K8s»

## Задание 1. Создать сетевую политику или несколько политик для обеспечения доступа.

* Создаем deploy frontend, backend, cache, namespace app и соответствующие сервисы:

[deploy-frontend.yaml](https://github.com/A-Tagir/kubernetes/blob/main/13/deploy-frontend.yaml)
[deploy-backend.yaml](https://github.com/A-Tagir/kubernetes/blob/main/13/deploy-backend.yaml)
[deploy-cache.yaml](https://github.com/A-Tagir/kubernetes/blob/main/13/deploy-cache.yaml)
[services.yaml](https://github.com/A-Tagir/kubernetes/blob/main/13/services.yaml)
[namespace.yaml](https://github.com/A-Tagir/kubernetes/blob/main/13/namespace.yaml)

* Применяем

![deploy_ok](https://github.com/A-Tagir/kubernetes/blob/main/13/Kubernetes13_deploy_ok.png)

* Заходим в pod-ы и убеждаемся, что есть доступ:

![no-policy](https://github.com/A-Tagir/kubernetes/blob/main/13/Kubernetes13_access_no_policy_ok.png)

* Теперь создаем правило, которое по-умолчанию все запрещает:

[policy.yaml](https://github.com/A-Tagir/kubernetes/blob/main/13/policy.yaml)

* Применяем policy и проверяем доступы к сервисам с различных подов, убеждаемся, что доступ запрещен:

![deny_policy](https://github.com/A-Tagir/kubernetes/blob/main/13/Kubernetes13_access_deny.png)

* Создаем правило, которое разрешает доступ с frontend на backend и c backend на cache. Применяем и проверяем:

[policy-apply.yaml](https://github.com/A-Tagir/kubernetes/blob/main/13/policy-apply.yaml)

![policy_ok](https://github.com/A-Tagir/kubernetes/blob/main/13/Kubernetes13_access_apply_policy_ok.png)

* Видим, что доступ появился согласно правилам, а в других комбинациях доступа нет.
  