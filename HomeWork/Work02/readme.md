# Лабораторная работа. Просмотр таблицы MAC-адресов коммутатора 
### Топология

<img width="501" height="295" alt="image" src="https://github.com/user-attachments/assets/4f90e2dd-43ac-4cf6-aef2-ed284598dd5d" />

### Таблица адресации

|    Устройство    |      Интерфейс     |      IP-адрес       |    Маска подсети    | 
|------------------|--------------------|---------------------|---------------------|
| S1               | VLAN 1             | 192.168.1.11        |    255.255.255.0    |
| S2               | VLAN 1             | 192.168.1.12        |    255.255.255.0    |
| PC-A             | NIC                | 192.168.1.1         |    255.255.255.0    |
| PC-B             | NIC                | 192.168.1.2         |    255.255.255.0    | 
###  	Цели
###  	Часть 1. Создание и настройка сети
###  	Часть 2. Изучение таблицы МАС-адресов коммутатора

###  Решение:

###  Часть 1. Создание и настройка сети

####  Шаг 1. Подключите сеть в соответствии с топологией.
<img width="374" height="215" alt="image" src="https://github.com/user-attachments/assets/6099d1c7-9eca-4efb-aa5d-a7d72fb58e19" />

####  Шаг 2. Настройте узлы ПК.
Настройки производим в конфигурации компьютеров.
<img width="629" height="551" alt="image" src="https://github.com/user-attachments/assets/1e0496ba-ad82-49af-83e9-b9b4388821c5" />

####  Шаг 3. Выполните инициализацию и перезагрузку коммутаторов.
Для инициализации коммутатора заходим в привилегированный режим и сбрасываем его настройки. После этого перезагружаем коммутатор.
```
Switch>enable
Switch#erase startup-config
Erasing the nvram filesystem will remove all configuration files! Continue? [confirm]y[OK]
Erase of nvram: complete
%SYS-7-NV_BLOCK_INIT: Initialized the geometry of nvram
Switch#reload
```
Эту процедуру повторяем и для второго коммутатора.

####  Шаг 4. Настройте базовые параметры каждого коммутатора.
#####  a.	Настройте имена устройств в соответствии с топологией.
```
Первый коммутатор
Switch>enable
Switch#configure terminal
Switch(config)#hostname S1
S1(config)#
Второй коммутатор
Switch>enable
Switch#configure terminal
Switch(config)#hostname S2
S2(config)#
```
#####  b.	Настройте IP-адреса, как указано в таблице адресации.
```
Первый коммутатор
S1(config)#interface vlan 1
S1(config-if)#ip address 192.168.1.11 255.255.255.0
S1(config-if)#no shutdown
Второй коммутатор
S2(config)#interface vlan 1
S2(config-if)#ip address 192.168.1.12 255.255.255.0
S2(config-if)#no shutdown
```
#####  c.	Назначьте cisco в качестве паролей консоли и VTY.
```
Назначаем пароль консоли для обоих коммутаторов
S1(config)#line con 0
S1(config-line)#password cisco
S1(config-line)#login
S2(config)#line con 0
S2(config-line)#password cisco
S2(config-line)#login
Назначаем пароли VTY для доступа к коммутаторам по протоколу Telnet.
S1(config)#line vty 0 4
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#line vty 5 15
S1(config-line)#password cisco
S1(config-line)#login
Для второго коммутатора повторяем процедуру
S2(config)#line vty 0 4
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#line vty 5 15
S2(config-line)#password cisco
S2(config-line)#login
```
#####  d.	Назначьте class в качестве пароля доступа к привилегированному режиму EXEC.
```
Первый коммутатор
S1>enable
S1#configure terminal
S1(config)#enable secret class
Второй коммутатор
S2>enable
S2#configure terminal
S2(config)#enable secret class
```
###  Часть 2. Изучение таблицы МАС-адресов коммутатора
#####  Шаг 1. Запишите МАС-адреса сетевых устройств.
a.
На обоих компьютерах в командной строке выводим команду:
```
ipconfig /all
```
Первый компьютер. MAC-адрес (IPv4, IPv6)

<img width="621" height="138" alt="image" src="https://github.com/user-attachments/assets/eb513ca6-f20f-4a8e-acbb-67ce7174a1bf" />


Второй компьютер. MAC-адрес (IPv4, IPv6)

<img width="617" height="145" alt="image" src="https://github.com/user-attachments/assets/d3c93513-9014-4f0a-8186-549156aba924" />

b.	Подключаемся к коммутаторам S1 и S2 через консоль и вводим команду 

Первый комутатор
```
User Access Verification
Password: 
S1>enable
Password: 
S1#show interface F0/1
FastEthernet0/1 is up, line protocol is up (connected)
  Hardware is Lance, address is 0001.9738.4601 (bia 0001.9738.4601)
```
MAC-адрес:   0001.9738.4601

Второй коммутатор
```
User Access Verification
Password: 
S2>enable
Password: 
S2#show interface F0/1
FastEthernet0/1 is up, line protocol is up (connected)
  Hardware is Lance, address is 000a.4118.7201 (bia 000a.4118.7201)
```
MAC-адрес:   000a.4118.7201

#####  Шаг 2. Просмотрите таблицу МАС-адресов коммутатора.
a.	Подключаемся к коммутатору S2 через консоль
```
User Access Verification
Password: 
S2>enable
Password: 
S2#
```
b.  Выводим на экран таблицу MAC-адресов 

<img width="467" height="207" alt="image" src="https://github.com/user-attachments/assets/38fbc113-889f-4439-8991-7be3d66a3c0e" />

1    0001.9738.4601    DYNAMIC     Fa0/1  -  Динамически определенный MAC-адрес, соответствует первому коммутатору, подключен на 1 порту.

1    000d.bda5.dae0    DYNAMIC     Fa0/18 -  Динамически определенный MAC-адрес, соответствует второму компьютеру, подключен на 18 порту.

######  Если вы не записали МАС-адреса сетевых устройств в шаге 1, как можно определить, каким устройствам принадлежат МАС-адреса, используя только выходные данные команды show mac address-table? Работает ли это решение в любой ситуации?

В таблице указано соответствие MAC-адреса и порта. В большинстве случаев этого достаточно чтобы узнать подключенное устройство. Это не сработает, если к порту подключено не одно устройство, а коммутатор, который собирает данные о всех устройствах, подключенных к ниму.
Соответственно в таблицу попадут все MAC-адреса подключенных устройств.
#####  Шаг 3. Очищаем таблицу МАС-адресов коммутатора S2.
а. Используем команду:
```
S2# clear mac address-table dynamic
```
b.	Снова смотрим таблицу MAC-адресов.

<img width="463" height="238" alt="image" src="https://github.com/user-attachments/assets/052461b1-fe9c-4c80-8520-08578a092b75" />

МАС-адрес для VLAN 1 указан;
Других МАС-адресов нет.
После повторного просмотра таблицы изменений не появилось.

#####  Шаг 4. С компьютера PC-B отправьте эхо-запросы устройствам в сети и просмотрите таблицу МАС-адресов коммутатора.

#####  
