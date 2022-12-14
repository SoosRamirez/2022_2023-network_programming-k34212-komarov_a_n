University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Network programming](https://github.com/itmo-ict-faculty/network-programming)

Year: 2022/2023

Group: K34212

Author: Komarov Alexey Nikolaevich

Lab: Lab2

Date of create: 21.11.2022

Date of finished: 

## Создание второй CHR

Аналогично первой работе была создана еще одно машина на RouterOS
![OpenVPN UI](/lab2/lab_2_1.png)

## Использование ansible для настройки CHR и получение конфигурации машин

1. Для ansible была создана специальная папка
```
mkdir /etc/ansible
```
2. Для использования не настроек по умолчанию можно либо создать файл ansible.cgf, который будет использоваться как файл настроек по умолчанию для файлов в текущей директории, или создать файл с иным названием и указывать этот файл при вызове плейбука через флаг -i. В лабораторной работе создан файл ansible.cfg:
```
[defaults]
inventory = ./hosts
remote_tmp = /etc/ansible/ansible_tmp/
```
3. В качестве файла inventory указан файл hosts, в этом файле должны быть прописаны устройства и параметры для подключения к ним:
```
[routers]
router1 ansible_host=10.8.0.4 ansible_ssh_user=Ansible ansible_ssh_pass=Ansible
router2 ansible_host=10.8.0.8 ansible_ssh_user=Ansible ansible_ssh_pass=Ansible

[routers:vars]
ansible_connection=ansible.netcommon.network_cli
ansible_network_os=community.routeros.routeros
```
В квадратных скобках указывается имя группы устройств, через <имя группы>:vars можно указать повторяющееся параметры для группы устройств. В параметр ansible_host указывается адрес устройства, в ansible_ssh_user указываются логин и в ansible_ssh_pass пароль для ssh подключения. Параметр ansible.netcommon.network_cli используется для ssh соединения к сетевым устройсвам, а параметр community.routeros.routeros указывает операционную систему RouterOS, на которой работают CHR.

4. Команды для выполнения пишутся в таске, таск в плее, плей в плейбуке - файле с разрешением yml. Нужно создать файл и указать команды для выполения:
```
- name: set routers config
  hosts: routers
  gather_facts: false
  tasks:
  - name: user add
    community.routeros.command:
      commands:
        - /user add name=Ansible2 password=Ansible group=full
  - name: NTP Client set
    community.routeros.command:
      commands:
        - /system clock set time-zone-name=Europe/Moscow
        - /system ntp client set enabled=yes mode=unicast server=0.ru.pool.ntp.org
  - name: OSPF configuration
    community.routeros.command:
      commands:
        - /ip address add address=10.10.10.1/30 interface=ether1 network=10.10.10.0
        - /interface bridge add name=loopback
        - /ip address add address=172.16.0.1 interface=loopback network=172.16.0.1
        - /routing id add disabled=no id=4.4.4.4 name=OSPF_ID select-dynamic-id=""
        - /routing ospf instance add router-id=OSPF_ID name=ospf-instance-1 originate-default=always
        - /routing ospf area add instance=ospf-instance-1 name=backbone
        - /routing ospf interface-template add area=backbone auth=md5 auth-key=10072001 interfaces=ether1 networks=10.10.10.0/30
        - /routing ospf neighbor print
    register: system_resource_print
```
![OpenVPN UI](/lab2/lab_2_2.png)
![OpenVPN UI](/lab2/lab_2_3.png)
![OpenVPN UI](/lab2/lab_2_4.png)
![OpenVPN UI](/lab2/lab_2_5.png)
5. Для сбора данных создан еще один плейбук data_get.yml:
```
- name: Getting routers data
  hosts: routers
  gather_facts: false
  tasks:
  - name: get_ospf_topology
    community.routeros.command:
      commands:
        - /routing ospf export compact
    register: ospf_topology
  - name: get_router_config
    community.routeros.command:
      commands:
        - /export compact
    register: router_config
  - name: Saving ospf topology
    copy:
      content: "{% for x in ospf_topology.stdout if x!=''%}{{x}}\n{% endfor %}\n"
      dest: /etc/ansible/files/ospf_topology_{{inventory_hostname}}.log
  - name: Saving router config
    copy:
      content: "{% for x in router_config.stdout if x!=''%}{{x}}\n{% endfor %}\n"
      dest: /etc/ansible/files/{{inventory_hostname}}_config.log

```
В данном плэйбуке в разные файлы собирается информация о настройке ospf на роутерах и полная конфигурация роутеров. Потом она записывается в файлы. 
![OpenVPN UI](/lab2/lab_2_6.png)
![OpenVPN UI](/lab2/lab_2_7.png)
![OpenVPN UI](/lab2/lab_2_8.png)
![OpenVPN UI](/lab2/lab_2_9.png)

## Вывод

В ходе работы было выполнено знакомство с ansible. При помощи данного инструмента на обеих машинах была проведена настройка ospf, а так же настроен ntp клиент. Была создана 
![OpenVPN UI](/lab2/lab_2_10_1.png)
