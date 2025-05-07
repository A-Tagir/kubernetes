# Домашнее задание к занятию «Хранение в K8s. Часть 1»

## Задание 1. Создать Deployment приложения, состоящего из двух контейнеров и обменивающихся данными.

* Создаю Deployment:

[deploy-volumes.yaml](https://github.com/A-Tagir/kubernetes/blob/main/06/deploy-volumes.yaml)

* Применяю

![test_ok](https://github.com/A-Tagir/kubernetes/blob/main/06/Kubernetes06_echo_ok.png)

* Видим, что контейнеры создались, а файл success.txt доступен в контейнере multitool и там каждые 5 сек появляется новая строка.

## Задание 2.