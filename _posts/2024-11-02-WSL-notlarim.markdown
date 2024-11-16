---
layout: post
title:  "WSL Notlarım"
date:   2024-11-02 17:57:24 +0000
categories: wsl
---
# WSL Notlarım.

## Ubuntu 22.04 Üzerine Docker Kurmak

```shell
sudo apt install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin
# IP Tablolarını güncelleyerek docker servisinin başlatılabilmesi sağlanır
sudo update-alternatives --config iptables
# Enter 1 to select iptables-legacy
sudo service docker start
docker compose version
```


## Ubuntu 24.04 Üzerine Docker Kurmak

```shell
# Bu komutu windows konsolda çalıştır
wsl --set-default-version 2
# Bu komutları WSL Ubuntu Jammy konsolda çalıştır
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
sudo usermod -aG docker $USER
# Bu komutu windows konsolda çalıştır
wsl --shutdown
```
