University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Network programming](https://github.com/itmo-ict-faculty/network-programming)

Year: 2022/2023

Group: K34212

Author: Komarov Alexey Nikolaevich

Lab: Lab3

Date of create: 28.11.2022

Date of finished: 

# Лабораторная работа №3 "Развертывание Netbox, сеть связи как источник правды в системе технического учета Netbox"

## Описание
   В данной лабораторной работе вы ознакомитесь с интеграцией Ansible и Netbox и изучите методы сбора информации с помощью данной интеграции.

## Цель работы:
   С помощью Ansible и Netbox собрать всю возможную информацию об устройствах и сохранить их в отдельном файле.

## Ход работы:
   В процессе выполнения лабораторной работы были выпонены следующие шаги:
   
   ### Развертывание Netbox на Ubuntu ###
   1. Изначально была предпринята попытка установить Netbox из архива с последним релизом. В таком случае Netbox не запускался из-за того, что не мог найти некоторые файлы. После этого Netbox был установлен при помощи клонирования репозитория по официальной документации. Было выполнено подключение к веб интерфейсу Netbox.

![image](/lab3/lab_3_1.png)

 
   2. В NetBox были созданы site, devices type, manufactures и добавлены роутеры R1 и R2. Была заполнена информация о них.

![image](/lab3/lab_3_2.png)
![image](/lab3/lab_3_3.png)
![image](/lab3/lab_3_4.png)
![image](/lab3/lab_3_5.png)

   3. Из NetBox был экспортирован файл netbox_devices.csv с данными об устройствах. Файл был скопирован на сервер Ubuntu.

```
scp /Users/aleksejkomarov/Downloads/netbox_devices.csv soos@158.160.23.129:~/ 
```

   4. Далее при помощи ansible из файла netbox_devices.csv были получены имена роутеров. Далее на CHR, которые были созданы в прошлых работах, имена были заменены.

![image](/lab3/lab_3_6.png)
![image](/lab3/lab_3_7.png)

   5. Теперь задача обратная предыдущей - с помощью ansible playbook нужно передать информацию о роутере в NetBox.

![image](/lab3/lab_3_8.png)
![image](/lab3/lab_3_9.png)
![image](/lab3/lab_3_10.png)

   6. Был создан playbook, при помощи которого достаем информацию о серийном номере из микротика и отправляем в NetBox

![image](/lab3/lab_3_11.png)
![image](/lab3/lab_3_12.png)
![image](/lab3/lab_3_13.png)


## Выводы:
   Таким образом, в процессе выполнения лабораторной работы ознакомился с инструментом NetBox, были созданы сценарии Ansible для работы с NetBox. Также была выполнена проверка локальной связности между роутерами и NetBox.
   
![image](/lab3/lab_3_14.png)
![image](/lab3/lab_3_15.png)
![image](/lab3/lab_3_16.png)

  Схема связи
  
![image](/lab3/drawio.png)
