# Домашнее задание к занятию «Хранение в K8s. Часть 2»

## Задание 1. Создать Deployment приложения, использующего локальный PV, созданный вручную.

* Создаю PV:

[pv.yaml](https://github.com/A-Tagir/kubernetes/blob/main/07/pv.yaml)

* Создаю PVC, StorageClass пока не указываем:

[pvc.yaml](https://github.com/A-Tagir/kubernetes/blob/main/07/pvc-vol.yaml)

* Создаю Deploy согласно заданию:

[deploy-pv.yaml](https://github.com/A-Tagir/kubernetes/blob/main/07/deploy-pv.yaml)

* Применяем созданные объекты и проверяем:



