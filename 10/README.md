# Домашнее задание к занятию «Helm»

## Задание 1.  Подготовить Helm-чарт для приложения

* Устанавливаю HELM:
```
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```
* Подготовил чарт для нашего приложения:

[netology-chart](https://github.com/A-Tagir/kubernetes/tree/main/10/netology-chart)

* 