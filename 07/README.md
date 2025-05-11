# Домашнее задание к занятию «Хранение в K8s. Часть 2»

## Задание 1. Создать Deployment приложения, использующего локальный PV, созданный вручную.

* Создаю PV:

[pv.yaml](https://github.com/A-Tagir/kubernetes/blob/main/07/pv.yaml)

* Создаю PVC, StorageClass пока не указываем:

[pvc.yaml](https://github.com/A-Tagir/kubernetes/blob/main/07/pvc-vol.yaml)

* Создаю Deploy согласно заданию:

[deploy-pv.yaml](https://github.com/A-Tagir/kubernetes/blob/main/07/deploy-pv.yaml)

* Применяем созданные объекты и проверяем:

![apply_ok](https://github.com/A-Tagir/kubernetes/blob/main/07/Kubernetes07_pvc_apply_ok.png)

- здесь мы видим, что после создания PVC, PV перешел из  статуса Available в статус Bound.
- Deploy создался, pod поднялся.

* Проверяем, что выполняются условия задания (multitool может читать файл, в который busybox пишет каждые пять секунд в общей директории):

![read-write_OK](https://github.com/A-Tagir/kubernetes/blob/main/07/Kubernetes07_pvc_r-w_ok.png)

* Согласно заданию удаляем deploy и pvc. Проверяем PV - он находится в статусе Error. 
  Причина - был задан persistentVolumeReclaimPolicy: Delete, но host_path deleter only supports /tmp/.+ but received provided /var/data/pv1. В данном случае правильно было бы указать Retain, тогда PV оказался бы в статусе Released.

* Удаляем PV: kubectl delete pv pv1
```
tiger@VM1:~/Kubernetes/07$
tiger@VM1:/var/data/pv1$ ls
success.txt
tiger@VM1:/var/data/pv1
```

* Видим, что файл остался на диске. Чтобы директива persistentVolumeReclaimPolicy: Delete сработала, нужно размещать 
  Volume в папке tmp.

* Заменяем Delete на Retain и проверяем снова:

![PV-Released](https://github.com/A-Tagir/kubernetes/blob/main/07/Kubernetes07_pv_released.png)

## Задание 2. Создать Deployment приложения, которое может хранить файлы на NFS с динамическим созданием PV.

