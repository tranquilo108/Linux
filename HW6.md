Задание:
* Настроить статическую конфигурацию (без DHCP) в Ubuntu через ip и netplan. Настроить IP, маршрут по умолчанию и DNS-сервера (1.1.1.1 и 8.8.8.8). Проверить работоспособность сети.
```sh
sudo nano /etc/netplan/*.yaml
```
network:
  version: 2
  renderer: networkd
  ethernets:
    eno1:
      dhcp4: no
      addresses: [192.168.100.9/24]
      gateway4: 192.168.100.255
      nameservers:
        addresses:
          - 1.1.1.1
          - 8.8.8.8
```sh
sudo netplan apply
ping google.com
dig google.com MX
dig yandex.ru
```
* Настроить правила iptables для доступности сервисов на TCP-портах 22, 80 и 443. Также сервер должен иметь возможность устанавливать подключения к серверу обновлений. Остальные подключения запретить.
```sh
sudo iptables -A INPUT -p tcp -m multiport --dports 22,80,443 -j ACCEPT
sudo iptables -I INPUT -a state --state ESTABLISHED,RELATED -j ACCEPT
sudo iptables -A OUTPUT -p tcp -d archive.ubuntu.com --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT
sudo iptables -A OUTPUT -p tcp -d archive.ubuntu.com --dport 443 -m state --state NEW,ESTABLISHED -j ACCEPT
sudo iptables -P INPUT DROP
```
* Запретить любой входящий трафик с IP 3.4.5.6.
```sh
sudo iptables -A INPUT -s 3.4.5.6 -j DROP
```
* * Запросы на порт 8090 перенаправлять на порт 80 (на этом же сервере).
```sh
sudo iptables -A INPUT -p tcp --dport 8090 -j ACCEPT
sudo iptables -t nat -I PREROUTING -p tcp --dport 8090 -j REDIRECT --to-port 80
```
* * Разрешить подключение по SSH только из сети 192.168.0.0/24.
```sh
sudo iptables -A INPUT -p tcp ! -s 192.168.0.0/24 --dport 22 -j DROP
```



## Разрешаем LOOPBACK
Это тот самый 127.что-то там.итд.итп который нужен для внутреннего взаимодействия внутри операционки
```sh
iptables -A INPUT -i lo -j ACCEPT
```
## Разрешаем ICMP
```sh
iptables -A INPUT -p icmp -j ACCEPT
```
