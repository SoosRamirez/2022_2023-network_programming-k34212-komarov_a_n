University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Network programming](https://github.com/itmo-ict-faculty/network-programming)

Year: 2022/2023

Group: K34212

Author: Komarov Alexey Nikolaevich

Lab: Lab1

Date of create: 04.11.2022

Date of finished:

---

## Настройка vpn сервера

1. На Яндекс.Облаке была создана виртуальная машина. Было выполнено подключение к машине через SSH

```
ssh -i <путь_до_приватного_ключа> <имя_пользователя>@<ip_облака>
```
2. На машине были установлены Python3 и Ansible
```
sudo apt install python3-pip
ls -la /usr/bin/python3.6
sudo pip3 install ansible
ansible --version
```

## Настройка OpenVPN

### На созданную виртуальную машину был установлен OpenVPN

1. Устанавливаем необходимые зависимости (ca-сертификаты):
```
apt install ca-certificates wget net-tools gnupg
```
2. Добавляем OpenVPN в список репозиториев, отслеживаемых apt:
```
wget -qO - https://as-repository.openvpn.net/as-repo-public.gpg | apt-key add -
echo "deb http://as-repository.openvpn.net/as/debian focal main">/etc/apt/sources.list.d/openvpn-as-repo.list
apt update
```
3. Устанавливаем OpenVPN access server:
```
apt install openvpn-as
```
4. После установки в консоли видим сообщение об успешной установке OpenVPN, там же видим адрес для подключения к WebUI:
```
https://10.129.0.10:943/admin

During normal operation, OpenVPN AS can be accessed via these URLs:
Admin  UI: https://10.129.0.10:943/admin
Client UI: https://10.129.0.10:943/
To login please use the "openvpn" account with "73uL7ZohCum4" password.
```
5. Подключаемся к админка через браузер, заходим с логином и паролем, которые были созданы автоматически при запуске OVPN
![OpenVPN UI](/lab1/lab_1_1.png)
7. В насторйках Переходим в Configuration/Advanced VPN и устанавливаем TLS Control Channel Security в позицию none, чтобы отключить проверку tls-сертификата.
8. Дальше В Configuration/Network Settings убираем протокол UDP, так как наш клиент в лице Микротика udp не поддерживает.

### Регистрация клиента

1. В User Managment/User Permissions добавляем нового пользователя.
![OpenVPN UI](/lab1/lab_1_2.png)
3. В User Managment/User Profiles на панели только что созданного юзера нажимаем New Profile/Create Profile. Получаем в подарок файл с расширением .ovpn, необходимый для настройки клиента.
![OpenVPN UI](/lab1/lab_1_3.png)

## Настройка CHR, подключение клиента

1. Скачиваем образ Cloud Hosted Router с [Официального сайта Mikrotik](https://mikrotik.com/download)
2. Устанавливаем его на виртуальную машину VirtualBox.
3. При настройке CHR ставим сетевой адаптер типа Сетевой мост для подключения в WebUI микротика
4. Подключаемся к WebFig, для этого при помощи команды ```ip address print``` узнаем ip адрес CHR, дальше подключаемся с логином admin и паролем, заданным при подключении к консоли нашего микротика
![OpenVPN UI](/lab1/lab_1_4.png)
6. В раздел files добавляем наш .ovpn файл
![OpenVPN UI](/lab1/lab_1_5.png)
7. Импортируем его в сертификаты
![OpenVPN UI](/lab1/lab_1_6.png)
8. Создаем интерфейс, необходимо заполнить данные, такие как ip адрес сервера, логин и пароль пользователя, которого мы создали в Openvpn WebUI, и типы шифрования. 
![OpenVPN UI](/lab1/lab_1_7.png)
9. Дальше необходимо изменить на CHR адаптер на NAT, перезапустить виртуальный роутер. После того, как роутер будет перезагружен, при вводе команды ip address print мы увидим наш новый интерфейс. После этого можем проверить соединение при помощи команды Ping. 
![OpenVPN UI](/lab1/lab_1_8.png)
![OpenVPN UI](/lab1/lab_1_9.png)

## Вывод

В процессе выполнения лабораторной работы я научился поднимать OpenVPN сервер на Ubuntu, познакомился с OS от Mikrotik, создал между сервером и CHR-клиентом VPN-туннель.







