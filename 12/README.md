# Домашнее задание к занятию «Установка Kubernetes с помощью kubeadm, kubespray»

## Задание 1.  Установить кластер k8s с 1 master node

* Чек-лист 5 ВМ с Ubuntu. Создаю Виртуальную машину с Ubuntu, добавляю свой ключ ssh, устанавливаю базовые пакеты:
```
sudo apt install net-tools
sudo apt install bind9utils
sudo apt install mc
sudo apt install python3.10-venv
```
* клонирую еще на 4 машины:

![VMs-created](https://github.com/A-Tagir/kubernetes/blob/main/12/Kubernetes12_Cluster_VMs.png)

* Настраиваем первую ноду для запуска kubespray
```
sudo apt install git
python3 -m venv venv
source venv/bin/activate
sudo apt install pip
sudo apt install python3-netaddr
git clone https://github.com/kubernetes-sigs/kubespray.git
pip install -U -r requirements.txt
```
* теперь добавляем хосты в inventory.ini
```
[kube_control_plane]
node1 ansible_host=192.168.0.135 # ip=10.3.0.1 etcd_member_name=etcd1

[etcd:children]
kube_control_plane

[kube_node]
node2 ansible_host=192.168.0.136
node3 ansible_host=192.168.0.137
node4 ansible_host=192.168.0.139
node4 ansible_host=192.168.0.140
```
* Запускаем playbook:
```
ansible-playbook -i inventory/mycluster/inventory.ini cluster.yml -b -v
```
![playbook_ok](https://github.com/A-Tagir/kubernetes/blob/main/12/Kubernetes12_Cluster_Ansible_OK.png)

* Теперь копируем конфигурационный файл на node1
```
sudo cp -i /etc/kubernetes/admin.conf /home/tiger/.kube/config
sudo chmod tiger:tiger /home/tiger/.kube/config
```
* Проверяем

![kubectl_ok]()
