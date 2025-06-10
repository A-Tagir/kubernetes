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

![kubectl_ok](https://github.com/A-Tagir/kubernetes/blob/main/12/Kubernetes12_Cluster_kubectl_OK.png)

![etcd_ok](https://github.com/A-Tagir/kubernetes/blob/main/12/Kubernetes12_Cluster_etcd_OK.png)

## Задание 2.  Установить HA кластер

* Будем устанавливать кластер состоящий из 3-х мастеров и 2-х вокеров.
* В качестве HA будет keepalive, в качестве балансировщика HAProxy. Они будут также на мастер-нодах.
* Все делаем также как в первом задании, но inventory теперь такой:
```
[kube_control_plane]
node1 ansible_host=192.168.0.135  etcd_member_name=etcd1
node2 ansible_host=192.168.0.141  etcd_member_name=etcd2
node3 ansible_host=192.168.0.142  etcd_member_name=etcd3

[etcd:children]
kube_control_plane

[kube_node]
node4 ansible_host=192.168.0.143  #ip=10.3.0.4
node5 ansible_host=192.168.0.144  #ip=10.3.0.5
```
* Также в group_vars/k8s_cluster/k8s-cluster.yml добавляем строку (это будет виртуальный IP)
  
  supplementary_addresses_in_ssl_keys: [192.168.0.50]

* Запускаем playbook. Затем проверяем что все поднялось без ошибок:

![HA_Apply_OK](https://github.com/A-Tagir/kubernetes/blob/main/12/Kubernetes12_Cluster_2HA_Apply.png)

* Далее заходим на каждую мастер-ноду и устанавливаем 
```
sudo apt install keepalived 
sudo apt install haproxy 
sudo apt install psmisc
sudo echo 'net.ipv4.ip_nonlocal_bind=1' >> /etc/sysctl.conf
```
* Теперь конфигурируем keepalive на первой машине:
```
global_defs {
  notification_email {
  }
  router_id LVS_DEVEL
  vrrp_skip_check_adv_addr
  vrrp_garp_interval 0
  vrrp_gna_interval 0
}

vrrp_script chk_haproxy {
  script "killall -0 haproxy"
  interval 2
  weight 2
}

vrrp_instance haproxy-vip {
  state BACKUP
  priority 100
  interface eth0                       # Network card
  virtual_router_id 60
  advert_int 1
  authentication {
    auth_type PASS
    auth_pass 1111
  }
  unicast_src_ip 192.168.0.135      # The IP address of this machine
  unicast_peer {
    192.168.0.141
    192.168.0.142                         # The IP address of peer machines
  }

  virtual_ipaddress {
    192.168.0.50/24                  # The VIP address
  }

  track_script {
    chk_haproxy
  }
}
```
и systemctl enable keepalived
* На других мастерах также, только IP адреса меняем в соответствии.
* Конфигурируем HAProxy:  haproxy.conf
```
global
        log /dev/log    local0
        log /dev/log    local1 notice
        chroot /var/lib/haproxy
        stats socket /run/haproxy/admin.sock mode 660 level admin expose-fd listeners
        stats timeout 30s
        user haproxy
        group haproxy
        daemon

        # Default SSL material locations
        ca-base /etc/ssl/certs
        crt-base /etc/ssl/private

        # See: https://ssl-config.mozilla.org/#server=haproxy&server-version=2.0.3&config=intermediate
        ssl-default-bind-ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384
        ssl-default-bind-ciphersuites TLS_AES_128_GCM_SHA256:TLS_AES_256_GCM_SHA384:TLS_CHACHA20_POLY1305_SHA256
        ssl-default-bind-options ssl-min-ver TLSv1.2 no-tls-tickets

defaults
        log     global
        mode    http
        option  httplog
        option  dontlognull
        timeout connect 5000
        timeout client  50000
        timeout server  50000
        errorfile 400 /etc/haproxy/errors/400.http
        errorfile 403 /etc/haproxy/errors/403.http
        errorfile 408 /etc/haproxy/errors/408.http
        errorfile 500 /etc/haproxy/errors/500.http
        errorfile 502 /etc/haproxy/errors/502.http
        errorfile 503 /etc/haproxy/errors/503.http
        errorfile 504 /etc/haproxy/errors/504.http
frontend kube-apiserver
  bind 192.168.0.50:6443
  mode tcp
  option tcplog
  default_backend kube-apiserver

backend kube-apiserver
    mode tcp
    option tcp-check
    balance roundrobin
    default-server inter 10s downinter 5s rise 2 fall 2 slowstart 60s maxconn 250 maxqueue 256 weight 100
    server kube-apiserver-1 192.168.0.135:6443 check # Replace the IP address with your own.
    server kube-apiserver-2 192.168.0.141:6443 check # Replace the IP address with your own.
    server kube-apiserver-3 192.168.0.142:6443 check # Replace the IP address with your own.


frontend kube-apiserver-local
  bind 127.0.0.1:6443
  mode tcp
  option tcplog
  default_backend kube-apiserver-local

backend kube-apiserver-local
    mode tcp
    server kube-apiserver-1 192.168.0.135:6443 check # Replace the IP address with your own.
```
и systemctl enable haproxy
* На других мастерах делаем соответственно с нужными IP (только в последней строке меняем!)
* Теперь конфигурируем kubernetes: kube-apiserver.yaml меняем соотв. серверу
```
    - --advertise-address=192.168.0.135
    - --bind-address=192.168.0.135
```
* Далее в конфигурациях правим 127.0.0.1:6443 на 192.168.0.135:6443 итп.
* Теперь в вокер нодах прописываем 
```
kubeadm-client.conf
---
    apiServerEndpoint: "192.168.0.50:6443"
```
* Перезагружаем все ноды и проверяем что все поднялось:

![no_errors!](https://github.com/A-Tagir/kubernetes/blob/main/12/Kubernetes12_Cluster_2HA_Apply.png)

* Теперь применяем deploy с тремя репликами([Сетевое взаимодействие в K8S. Часть 1](https://github.com/A-Tagir/kubernetes/tree/main/04)), создаем сервис nport и убеждаемся что все работает:
```
tiger@node1:~/kubernetes/kubernetes/04$ kubectl apply -f nginx-multitool-deploy.yaml
deployment.apps/nginx-multitool-deployment created
tiger@node1:~/kubernetes/kubernetes/04$ kubectl get pods
NAME                                          READY   STATUS    RESTARTS   AGE
nginx-multitool-deployment-65f4d75986-4p6rm   2/2     Running   0          11s
nginx-multitool-deployment-65f4d75986-g42jq   2/2     Running   0          11s
nginx-multitool-deployment-65f4d75986-x8dx8   2/2     Running   0          11s
tiger@node1:~/kubernetes/kubernetes/04$ kubectl apply -f nginx-service-nport.yaml
service/nginx-nport unchanged
tiger@node1:~/kubernetes/kubernetes/04$ kubectl get svc
NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes    ClusterIP   10.233.0.1      <none>        443/TCP        6h8m
nginx-nport   NodePort    10.233.29.174   <none>        80:30001/TCP   48m

```
![192.168.0.50:30001](https://github.com/A-Tagir/kubernetes/blob/main/12/Kubernetes12_Cluster_2HA_nginx_ok.png)
* Затем выключаем один мастер и один вокер.
```
 kubectl get pods
NAME                                          READY   STATUS    RESTARTS   AGE
nginx-multitool-deployment-65f4d75986-4p6rm   2/2     Running   0          6m30s
nginx-multitool-deployment-65f4d75986-g42jq   2/2     Running   0          6m30s
nginx-multitool-deployment-65f4d75986-x8dx8   2/2     Running   0          6m30s
tiger@node1:~/kubernetes/kubernetes/04$ kubectl get nodes
NAME    STATUS     ROLES           AGE     VERSION
node1   Ready      control-plane   6h14m   v1.33.1
node2   Ready      control-plane   6h14m   v1.33.1
node3   NotReady   control-plane   6h14m   v1.33.1
node4   Ready      <none>          6h13m   v1.33.1
node5   NotReady   <none>          6h13m   v1.33.1
```
* Видим, что примерно через 5 минут недостающие реплики пересоздаются на оставщемся вокере.
```
 kubectl get pods
NAME                                          READY   STATUS        RESTARTS   AGE
nginx-multitool-deployment-65f4d75986-4p6rm   2/2     Running       0          12m
nginx-multitool-deployment-65f4d75986-82ftr   2/2     Running       0          38s
nginx-multitool-deployment-65f4d75986-g42jq   2/2     Terminating   0          12m
nginx-multitool-deployment-65f4d75986-h75rs   2/2     Running       0          38s
nginx-multitool-deployment-65f4d75986-x8dx8   2/2     Terminating   0          12m
```
* Также видим, что nginx доступен
![192.168.0.50:30001](https://github.com/A-Tagir/kubernetes/blob/main/12/Kubernetes12_Cluster_2HA_nginx_ok.png)
* Включаем ноды.
```
 kubectl get nodes
NAME    STATUS   ROLES           AGE     VERSION
node1   Ready    control-plane   6h21m   v1.33.1
node2   Ready    control-plane   6h21m   v1.33.1
node3   Ready    control-plane   6h21m   v1.33.1
node4   Ready    <none>          6h20m   v1.33.1
node5   Ready    <none>          6h20m   v1.33.1
```

