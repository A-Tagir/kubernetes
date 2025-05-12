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

* Согласно заданию удаляем deploy и pvc. Проверяем PV - он находится в статусе Failed. 

![PV-Error](https://github.com/A-Tagir/kubernetes/blob/main/07/Kubernetes07_pv_error.png)

  Командой  kubectl describe pv pv1  проверяем почему:
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

* Настраиваем NFS по инструкции https://microk8s.io/docs/how-to-nfs:

```
sudo apt-get install nfs-kernel-server
sudo mkdir -p /srv/nfs
sudo chown nobody:nogroup /srv/nfs
sudo chmod 0777 /srv/nfs
sudo mv /etc/exports /etc/exports.bak
echo '/srv/nfs 10.0.0.0/8(rw,sync,no_subtree_check)' | sudo tee /etc/exports
sudo systemctl restart nfs-kernel-server
microk8s enable helm3
microk8s helm3 repo add csi-driver-nfs https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/charts
microk8s helm3 repo update
icrok8s helm3 install csi-driver-nfs csi-driver-nfs/csi-driver-nfs \
    --namespace kube-system \
    --set kubeletDir=/var/snap/microk8s/common/var/lib/kubelet
```
* Конфигурируем nfs чтобы они принимал соединения подсетей 10.0.0.0/8 и 172.0.0.0/8
```
/srv/nfs 10.0.0.0/8(rw,sync,no_subtree_check)
/srv/nfs 172.0.0.0/8(rw,sync,no_subtree_check)
```
* Создаем StorageClass for NFS:

[sc-nfs.yaml](https://github.com/A-Tagir/kubernetes/blob/main/07/sc-nfs.yaml)

* Создаем PVC:

[pvc-nfs.yaml](https://github.com/A-Tagir/kubernetes/blob/main/07/pvc-nfs.yaml)

* Применяем и проверяем, видим, что PV создался автоматически:

![PVC-NFS](https://github.com/A-Tagir/kubernetes/blob/main/07/Kubernetes07_pvc_nfs_ok.png)

* Создаем Deploy (меняем в прежнем claimName: pvc-nfs):

[deploy-nfs.yaml](https://github.com/A-Tagir/kubernetes/blob/main/07/deploy-nfs.yaml)

* Применяем и проверяем, видим что файл создается, пишется и доступен с multitool:

![deploy_nfs_ok](https://github.com/A-Tagir/kubernetes/blob/main/07/Kubernetes07_deploy_nfs_ok.png)








