# Лабораторная работа. Базовая настройка коммутатора
### Топология

<img width="498" height="150" alt="image" src="https://github.com/user-attachments/assets/73dbe4d4-e6a9-47bd-91e2-f507eaf0cf52" />

### Таблица адресации

|    Устройство    |      Интерфейс     |   IP-адрес / префикс       |
|------------------|--------------------|----------------------------|
| S1               | VLAN 1             | 192.168.1.2 /24            |
| PC-A             | NIC                | 192.168.1.10 /24           |
 
###  Задание: 

1. Проверка конфигурации коммутатора по умолчанию
2. Создание сети и настройка основных параметров устройства
  - Настройте базовые параметры коммутатора.
  - Настройте IP-адрес для ПК.
3. Проверка сетевых подключений
  -	Отобразите конфигурацию устройства.
  -	Протестируйте сквозное соединение, отправив эхо-запрос.
  -	Протестируйте возможности удаленного управления с помощью Telnet.

###  Решение:

###  Часть 1. Создание сети и проверка настроек коммутатора по умолчанию

####  Шаг 1. Создайте сеть согласно топологии.

<img width="977" height="641" alt="image" src="https://github.com/user-attachments/assets/b5f79650-5441-4eec-a96b-e59623ca9c1e" />

Первоначально нельзя подключиться к коммутатору через Telnet или SSH потому что в конфигурации по умолчанию не задан виртуальный IP адрес устройства.

####  Шаг 2. Проверьте настройки коммутатора по умолчанию.
```
a.
Switch>
Switch>enable
Switch#show running-config 
Building configuration...

Current configuration : 1080 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname Switch
```
b.
- Интерфейсов FastEthernet имеется 24.
- Интерфейсов Gigabit Ethernet имеется 2.
- Диапазоны значений, отображаемых в vty-линиях:
  - line vty 0 4
  - line vty 5 15
c.
При попытке изучить файл загрузочной конфигурации (startup configuration), который содержится в энергонезависимом ОЗУ (NVRAM)

```
Switch#show startup-config 
startup-config is not present
Switch#
```

Данная надпись (startup-config is not present) появляется, так как у нас не сохранена конфигурация по умолчанию.
d.
```
Switch#show interface vlan 1
``` 
IP-адрес сети VLAN 1 не назначен.
mac-address 0001.c766.715b
Данный интерфейс административно отключен.
e.
```
Switch#show ip interface vlan 1
Vlan1 is administratively down, line protocol is down
  Internet protocol processing disabled
```
Vlan 1 - отключен;
Канал передачи данных - отключен;
Отключена обработка интернет-протокола.
f.
<img width="318" height="140" alt="image" src="https://github.com/user-attachments/assets/dc6e37e8-e01b-43c0-aec4-8e8f76cbad6f" />
g.
```
Switch#show version
```
Версия ОС Cisco IOS  -  Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)

Файл образа системы  -  2960-lanbasek9-mz.150-2.SE4.bin
h.
```
Switch#show interface f
```
Интерфейс включен;

Чтобы включить интерфейс:
```
Switch#configure terminal 
Switch(config)#interface f0/6
Switch(config-if)#no shutdown
```
mac-address 0002.4a5b.dd06

Настройки скорости и дуплекса -   Full-duplex, 100Mb/s

i.
```
Switch#show flash
```
Файл образа системы  - 2960-lanbasek9-mz.150-2.SE4.bin

###  Часть 2. Настройка базовых параметров сетевых устройств

####  Шаг 1. Настройка базовых параметров коммутатора.
a.
```
Switch#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#no ip domain-lookup
Switch(config)#hostname S1
S1(config)#service password-encryption
S1(config)#enable secret class
S1(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Unauthorized access is strictly prohibited. #

S1(config)#
```
b. Назначение IP-адреса интерфейсу SVI на коммутаторе
```
S1(config)#interface vlan 1
S1(config-if)#ip address 192.168.1.2 255.255.255.0
```
c. Доступ через порт консоли 
```
S1(config)#line con 0
S1(config-line)#logging synchronous
S1(config-line)#password cisco
S1(config-line)#login
```
d.	Настройка каналов виртуального соединения для удаленного управления (vty)
```
S1(config)#line vty 0 4
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#line vty 5 15
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#
```
Команда login включает проверку пароля при входе в систему.

####  Шаг 2. Настройте IP-адрес на компьютере PC-A.
<img width="826" height="350" alt="image" src="https://github.com/user-attachments/assets/0ae3b6e8-c277-4fb2-803d-69c27d36edee" />

###  Часть 3. Проверка сетевых подключений
a.
```
S1>enable
Password: 
S1#show run
Building configuration...

Current configuration : 1328 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname S1
!
enable secret 5 $1$mERr$9cTjUIEqNGurQiFU.ZeCi1
!
!
!
no ip domain-lookup
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
!
interface FastEthernet0/2
!
interface FastEthernet0/3
!
interface FastEthernet0/4
!
interface FastEthernet0/5
!
interface FastEthernet0/6
!
interface FastEthernet0/7
!
interface FastEthernet0/8
!
interface FastEthernet0/9
!
interface FastEthernet0/10
!
interface FastEthernet0/11
!
interface FastEthernet0/12
!
interface FastEthernet0/13
!
interface FastEthernet0/14
!
interface FastEthernet0/15
!
interface FastEthernet0/16
!
interface FastEthernet0/17
!
interface FastEthernet0/18
!
interface FastEthernet0/19
!
interface FastEthernet0/20
!
interface FastEthernet0/21
!
interface FastEthernet0/22
!
interface FastEthernet0/23
!
interface FastEthernet0/24
!
interface GigabitEthernet0/1
!
interface GigabitEthernet0/2
!
interface Vlan1
 ip address 192.168.1.2 255.255.255.0
 shutdown
!
banner motd ^C
Unauthorized access is strictly prohibited. ^C
!
!
!
line con 0
 password 7 0822455D0A16
 logging synchronous
 login
!
line vty 0 4
 password 7 0822455D0A16
 login
line vty 5 15
 password 7 0822455D0A16
 login
!
!
!
!
end


S1#
```
b. Проверка параметров VLAN 1.
```
S1#show interface vlan 1
```
Полоса пропускания (BW)   -  100000 Kbit

####  Шаг 2. Протестируйте сквозное соединение, отправив эхо-запрос.
a.
```
Cisco Packet Tracer PC Command Line 1.0
C:\>ping 192.168.1.10

Pinging 192.168.1.10 with 32 bytes of data:

Reply from 192.168.1.10: bytes=32 time=9ms TTL=128
Reply from 192.168.1.10: bytes=32 time=8ms TTL=128
Reply from 192.168.1.10: bytes=32 time=10ms TTL=128
Reply from 192.168.1.10: bytes=32 time<1ms TTL=128

Ping statistics for 192.168.1.10:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 10ms, Average = 6ms
```
Связь есть.
b.
Сначала включаем Vlan 1, так как он по умолчанию отключен
```
S1>enable
Password: 
S1#configure terminal 
S1(config)#interface vlan 1
S1(config-if)#no shutdown 
S1(config-if)#
S1(config-if)#exit
```
Проверяем с командной строки компьютера связь с коммутатором
```
C:\>ping 192.168.1.2

Pinging 192.168.1.2 with 32 bytes of data:

Request timed out.
Reply from 192.168.1.2: bytes=32 time<1ms TTL=255
Reply from 192.168.1.2: bytes=32 time<1ms TTL=255
Reply from 192.168.1.2: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.1.2:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```
Связь есть.
####  Шаг 3. Проверьте удаленное управление коммутатором S1.

