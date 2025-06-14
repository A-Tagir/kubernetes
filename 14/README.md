# Домашнее задание к занятию «Обновление приложений»

## Задание 1. Выбрать стратегию обновления приложения и описать ваш выбор.

1. Имеется приложение, состоящее из нескольких реплик, которое требуется обновить.
2. Ресурсы, выделенные для приложения, ограничены, и нет возможности их увеличить.
3. Запас по ресурсам в менее загруженный момент времени составляет 20%.
4. Обновление мажорное, новые версии приложения не умеют работать со старыми.
5. Вам нужно объяснить свой выбор стратегии обновления приложения.

* В данном случае, новые версии не умеют работать со старыми, поэтому стратегия Rolling update не подходит.
* Поскольку ресурсы ограничены, возможности увеличить их нет, а запас составляет всего 20%, то стратегии Blue-green,
  Canary и A/B не подходят.
* Ответ: Подходит только стратения Recreate.

## Задание 2. Обновить приложение.

* Создаем deploy и выбираем стратегию RollingUpdate поскольку приложение должно быть доступно:

[nginx-multitool-deploy.yaml](https://github.com/A-Tagir/kubernetes/blob/main/14/nginx-multitool-deploy.yaml)

* Применяем, затем обновляем nginx до 1.20
```
kubectl set image deployment/nginx-multitool-deployment nginx=nginx:1.20
deployment.apps/nginx-multitool-deployment image updated
```
![apply_update_ok](https://github.com/A-Tagir/kubernetes/blob/main/14/Kubernetes14_update_ok.png)

* Теперь, согласно заданию обновляем до версии 1.28:

![1.28_ok](https://github.com/A-Tagir/kubernetes/blob/main/14/Kubernetes14_1-28update_ok.png)

* Видим, что обновление также прошло успешно, версия nginx теперь 1.28.
* Пробуем обновить до 1.38:
![1.38-failed](https://github.com/A-Tagir/kubernetes/blob/main/14/Kubernetes14_1-38update_failed.png)
* Видим, что обновление не прошло, поскольку образа 1.38 не существует. Приложение доступно. Откатываем:
![rollout_ok](https://github.com/A-Tagir/kubernetes/blob/main/14/Kubernetes14_1-38-rollout_ok.png)

## Задание 3. Создать Canary deployment
1. Создать два deployment'а приложения nginx.
2. При помощи разных ConfigMap сделать две версии приложения — веб-страницы.
3. помощью ingress создать канареечный деплоймент, чтобы можно было часть трафика перебросить на разные версии приложения.

* Создаю helm-chart:
[canary-chart](https://github.com/A-Tagir/kubernetes/tree/main/14/canary-chart)
* ingress при обращении к ссылке /v1 будет направлять в приложение v1 а по ссылке /v2 - в приложение v2
* добавляем nginx.ingress.kubernetes.io/rewrite-target: /  чтобы в самом nginx обращение было в корневую.
* Проверяем:
```
helm test canary-chart/
Error: releaseTest: Release name is invalid: canary-chart/
tiger@VM1:~/Kubernetes/14$ helm lint canary-chart/
==> Linting canary-chart/
[INFO] Chart.yaml: icon is recommended

1 chart(s) linted, 0 chart(s) failed
```
* Применяем:
```
 helm upgrade canary canary-chart/
Release "canary" has been upgraded. Happy Helming!
NAME: canary
LAST DEPLOYED: Sat Jun 14 15:54:05 2025
NAMESPACE: default
STATUS: deployed
REVISION: 6
TEST SUITE: None
```
* Проверяем:
```
tiger@VM1:~/Kubernetes/14$ curl netotest.local/v1
<html>
<body>
  <h1>Hello Netlology v1.0</h1>
</body>
</html>
tiger@VM1:~/Kubernetes/14$ curl netotest.local/v2
<html>
<body>
  <h1>Hello Netlology v1.2</h1>
</body>
</html>
```
* Видим, что все работает.
