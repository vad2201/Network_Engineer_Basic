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
a. Выполнив команду arp -a, понимаем, что через протокол arp не добавлено никаких MAC-адресов
```
C:\>arp -a
No ARP Entries Found
```
b. Пингуем все устройства в сети
```   
    C:\>ping 192.168.1.1
    
    Pinging 192.168.1.1 with 32 bytes of data:
    
    Reply from 192.168.1.1: bytes=32 time<1ms TTL=128
    Reply from 192.168.1.1: bytes=32 time<1ms TTL=128
    Reply from 192.168.1.1: bytes=32 time<1ms TTL=128
    Reply from 192.168.1.1: bytes=32 time=1ms TTL=128
    
    Ping statistics for 192.168.1.1:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 1ms, Average = 0ms
    
    C:\>ping 192.168.1.11
    
    Pinging 192.168.1.11 with 32 bytes of data:
    
    Request timed out.
    Reply from 192.168.1.11: bytes=32 time=10ms TTL=255
    Reply from 192.168.1.11: bytes=32 time=1ms TTL=255
    Reply from 192.168.1.11: bytes=32 time<1ms TTL=255
    
    Ping statistics for 192.168.1.11:
        Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 10ms, Average = 3ms
    
    C:\>ping 192.168.1.12
    
    Pinging 192.168.1.12 with 32 bytes of data:
    
    Request timed out.
    Reply from 192.168.1.12: bytes=32 time<1ms TTL=255
    Reply from 192.168.1.12: bytes=32 time<1ms TTL=255
    Reply from 192.168.1.12: bytes=32 time<1ms TTL=255
    
    Ping statistics for 192.168.1.12:
        Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 0ms, Average = 0ms
```
Понимаем, что все устройства доступны в сети.

c. Подключаемся к коммутатору S2 и смотрим его таблицу MAC-адресов  до проверки связи в сети:

<img width="441" height="220" alt="image" src="https://github.com/user-attachments/assets/e656b7a2-8108-4aaa-a16d-44d2403e6acb" />

и после проведения проверки соединения компьютера PC-B с устройствами в сети:

<img width="454" height="242" alt="image" src="https://github.com/user-attachments/assets/0c7dc355-9772-4417-b13e-c42983c6c941" />

Делаем вывод, что в таблицу MAC-адресов коммутатора добавились записи MAC-адресов, находящихся в сети устройств.
Открываем консоль компьютера PC-B и смотрим кэш MAC-адресов заново
```
C:\>arp -a
```
<img width="577" height="135" alt="image" src="https://github.com/user-attachments/assets/5edb8610-bdf7-4c37-ae7c-8e94db973a7e" />

Таблица MAC-адресов пополнилась.

Вопрос для повторения
В сетях Ethernet данные передаются на устройства по соответствующим МАС-адресам. Для этого коммутаторы и компьютеры динамически создают ARP-кэш и таблицы МАС-адресов. Если компьютеров в сети немного, эта процедура выглядит достаточно простой. Какие сложности могут возникнуть в крупных сетях?

Если в сети много устройств, таблица МАС-адресов может быть переполнена, что может привести к ошибкам при поиске MAC-адреса назначения. 

Устаревшие записи. Если узлы долго не передают данные, соответствующие записи из таблицы удаляются. 

Сложность широковещания запроса ARP. Рассылки ARP могут вызывать широковещательные штормы. 
