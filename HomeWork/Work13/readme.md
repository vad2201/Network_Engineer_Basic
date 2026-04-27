# Лабораторная работа - Настройка протоколов CDP, LLDP и NTP
# Топология
<img width="740" height="111" alt="image" src="https://github.com/user-attachments/assets/26d57047-f8ff-4049-a597-8c5840da4f6f" />
 
### Таблица адресации
|Устройство|	Интерфейс |	IP-адрес |	Маска подсети |Шлюз по умолчанию|
|-------|-------|-------|-------|-------|
|R1	|Loopback1	|172.16.1.1|	255.255.255.0|	—|
| |	G0/0/1	|10.22.0.1|	255.255.255.0	|—|
|S1|	SVI| VLAN 1| 	10.22.0.2|	255.255.255.0	|10.22.0.1|
|S2	|SVI| VLAN 1|	10.22.0.3|	255.255.255.0|	10.22.0.1|

### Задачи
#### Часть 1. Создание сети и настройка основных параметров устройства
#### Часть 2. Обнаружение сетевых ресурсов с помощью протокола CDP
#### Часть 3. Обнаружение сетевых ресурсов с помощью протокола LLDP
#### Часть 4. Настройка и проверка NTP

#### Часть 1. Создание сети и настройка основных параметров устройства
#### Шаг 1. Создайте сеть согласно топологии.
Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.
<img width="413" height="110" alt="image" src="https://github.com/user-attachments/assets/b4e82311-a65b-4cf8-a17a-1f699ed8e807" />

#### Шаг 2. Настройте базовые параметры для маршрутизатора.
Перед настройкой маршрутизатора проведем его инициализацию:
```
enable
erase startup-config 
delete flash:vlan.dat
reload
```

a.	Назначьте маршрутизатору имя устройства.

b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

f.	Зашифруйте открытые пароли.

g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h.	Настройка интерфейсов, перечисленных в таблице выше

i.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```
Router>enable
Router#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#hostname R1 
R1(config)#no ip domain-lookup
R1(config)#enable secret class
R1(config)#line con 0
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#line vty 0 15
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#service password-encryption 
R1(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Prohibiting unauthorized access to the device!!!! #
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#int g0/0/1
R1(config-if)#ip address 10.22.0.1 255.255.255.0
R1(config-if)#no shutdown
R1(config-if)#exit
R1(config)#int loopback 1
R1(config-if)#ip address 172.16.1.1 255.255.255.0
R1(config-if)#no shutdown
R1(config)#exit
R1#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
```

#### Шаг 3. Настройте базовые параметры каждого коммутатора.
Перед настройкой коммутаторов проведем их инициализацию:
```
enable
erase startup-config
delete flash:vlan.dat 
reload
```
Процедура выполняется на каждом устройстве.

a.	Присвойте коммутатору имя устройства.

b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

f.	Зашифруйте открытые пароли.

g.	Создайте баннер, который предупреждает всех, кто обращается к устройству, видит баннерное сообщение «Только авторизованные пользователи!».  

h.	Отключите неиспользуемые интерфейсы

i.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

Коммутатор S1 
```
Switch>enable
Switch#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S1 
S1(config)#no ip domain-lookup
S1(config)#enable secret class
S1(config)#line con 0
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#line vty 0 15
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#service password-encryption 
S1(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Prohibiting unauthorized access to the device!!!! #
S1(config)#int range f0/2-4,f0/6-24,g0/1-2
S1(config-if-range)#shutdown
S1(config-if-range)#end
S1#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
```
Коммутатор S2 
```
Switch>enable
Switch#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S2 
S2(config)#no ip domain-lookup
S2(config)#enable secret class
S2(config)#line con 0
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#line vty 0 15
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#service password-encryption 
S2(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Prohibiting unauthorized access to the device!!!! #
S2(config)#int range f0/2-24, g0/1-2
S2(config-if-range)#shutdown
S2(config-if-range)#end
S2#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
```
#### Часть 2. Обнаружение сетевых ресурсов с помощью протокола CDP
На устройствах Cisco протокол CDP включен по умолчанию. Воспользуйтесь CDP, чтобы обнаружить порты, к которым подключены кабели.

a.	На R1 используйте соответствующую команду show cdp, чтобы определить, сколько интерфейсов включено CDP, сколько из них включено и сколько отключено.
```
R1#show cdp int
Vlan1 is administratively down, line protocol is down
  Sending CDP packets every 60 seconds
  Holdtime is 180 seconds
GigabitEthernet0/0/0 is administratively down, line protocol is down
  Sending CDP packets every 60 seconds
  Holdtime is 180 seconds
GigabitEthernet0/0/1 is up, line protocol is up
  Sending CDP packets every 60 seconds
  Holdtime is 180 seconds
```
Вопрос:

Сколько интерфейсов участвует в объявлениях CDP? Какие из них активны?

Vlan1, GigabitEthernet0/0/0, GigabitEthernet0/0/1 учавствуют в объявлениях cdp. Только GigabitEthernet0/0/1 активен.

 
b.	На R1 используйте соответствующую команду show cdp, чтобы определить версию IOS, используемую на S1.
```
R1#show cdp entry  S1

Device ID: S1
Entry address(es): 
Platform: cisco 2960, Capabilities: Switch
Interface: GigabitEthernet0/0/1, Port ID (outgoing port): FastEthernet0/5
Holdtime: 145

Version :
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Wed 26-Jun-13 02:49 by mnguyen

advertisement version: 2
Duplex: full
```

Вопрос:

Какая версия IOS используется на  S1?

На S1 используется Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
 
c.	На S1 используйте соответствующую команду show cdp, чтобы определить, сколько пакетов CDP было выданных.
S1# show cdp traffic
CDP counters : 
        Total packets output: 179, Input: 148 
        Hdr syntax: 0, Chksum error: 0, Encaps failed: 0 
        No memory: 0, Invalid packet: 0, 
        CDP version 1 advertisements output: 0, Input: 0 
        CDP version 2 advertisements output: 179, Input: 148
Вопрос:
Сколько пакетов имеет выход CDP с момента последнего сброса счетчика?
Введите ваш ответ здесь.
 
d.	Настройте SVI для VLAN 1 на S1 и S2, используя IP-адреса, указанные в таблице адресации выше. Настройте шлюз по умолчанию для каждого коммутатора на основе таблицы адресов.

Коммутатор S1 
```
S1(config)#vlan 1
S1(config-vlan)#exit
S1(config)#int vlan 1
S1(config-if)#ip address 10.22.0.2 255.255.255.0
S1(config-if)#no shutdown
S1(config-if)#exit
S1(config)#ip default-gateway 10.22.0.1
```
Коммутатор S2 
```
S2(config)#vlan 1
S2(config-vlan)#exit
S2(config)#int vlan 1
S2(config-if)#ip address 10.22.0.3 255.255.255.0
S2(config-if)#no shutdown
S2(config-if)#exit
S2(config)#ip default-gateway 10.22.0.1
```
e.	На R1 выполните команду show cdp entry S1 . 
```
R1#show cdp entry  S1

Device ID: S1
Entry address(es): 
  IP address : 10.22.0.2
Platform: cisco 2960, Capabilities: Switch
Interface: GigabitEthernet0/0/1, Port ID (outgoing port): FastEthernet0/5
Holdtime: 168

Version :
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Wed 26-Jun-13 02:49 by mnguyen

advertisement version: 2
Duplex: full
```

Вопрос:

Какие дополнительные сведения доступны теперь?

Появился ip адрес интерфейса, который подключен к роутеру (IP address : 10.22.0.2).

f.	Отключить CDP глобально на всех устройствах. 
На каждом устройстве выполняем команду no cdp run 
```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#no cdp run

S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#no cdp run

S2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S2(config)#no cdp run 
```

#### Часть 3. Обнаружение сетевых ресурсов с помощью протокола LLDP
На устройствах Cisco протокол LLDP может быть включен по умолчанию. Воспользуйтесь LLDP, чтобы обнаружить порты, к которым подключены кабели.
Откройте окно конфигурации

a.	Введите соответствующую команду lldp, чтобы включить LLDP на всех устройствах в топологии.
```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#lldp run

S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#lldp run

S2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S2(config)#lldp run
```
b.	На S1 выполните соответствующую команду lldp, чтобы предоставить подробную информацию о S2. 
```
S1# show lldp entry S2

Capability codes:
    (R) Router, (B) Bridge, (T) Telephone, (C) DOCSIS Cable Device
    (W) WLAN Access Point, (P) Repeater, (S) Station, (O) Other
------------------------------------------------
Local Intf: Fa0/1  
Chassis id: c025.5cd7.ef00 
Port id: Fa0/1 
Port Description: FastEthernet0/1
System Name: S2

System Description:
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.2(4)E8, RELEASE SOFTWARE (fc3) 
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2019 by Cisco Systems, Inc.
Compiled Fri 15-Mar-19 17:28 by prod_rel_team 

Time remaining: 109 seconds 
System Capabilities: B
Enabled Capabilities: B
Management Addresses:
    IP: 10.22.0.3 
Auto Negotiation - supported, enabled
Physical media capabilities:
    100base-TX(FD)
    100base-TX(HD)
    10base-T(FD)
    10base-T(HD)
Media Attachment Unit type: 16
Vlan ID: 1


Total entries displayed: 1
```
К сожалению, в CPT команда  show lldp entry S2 не работает.
```
S1#show lldp entry S2
             ^
% Invalid input detected at '^' marker.
```
Мы можем посмотреть соседние устройства командой:
```
S1#show lldp neighbors detail
------------------------------------------------
Chassis id: 0001.978B.E601
Port id: Fa0/1
Port Description: FastEthernet0/1
System Name: S2
System Description:
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Wed 26-Jun-13 02:49 by mnguyen
Time remaining: 90 seconds
System Capabilities: B
Enabled Capabilities: B
Management Addresses - not advertised
Auto Negotiation - supported, enabled
Physical media capabilities:
    100baseT(FD)
    100baseT(HD)
    1000baseT(HD)
Media Attachment Unit type: 10
Vlan ID: 1
------------------------------------------------
Chassis id: 0030.A3E0.9702
Port id: Gig0/0/1
Port Description: GigabitEthernet0/0/1
System Name: R1
System Description:
Cisco IOS XE Software, Version 03.13.04.S - Extended Support Release
Cisco IOS Software, ISR Software (X86_64_LINUX_IOSD-UNIVERSALK9-M), Version 15.5(3)S5, RELEASE SOFTWARE (fc2)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2017 by Cisco Systems, Inc.
Compiled Mon 05-Oct-15 11:24 by mcpre
Time remaining: 90 seconds
System Capabilities: R
Enabled Capabilities: R
Management Addresses - not advertised
Auto Negotiation - supported, enabled
Physical media capabilities:
    1000baseT(HD)
    100baseT(FD)
Media Attachment Unit type: 10
Vlan ID: 1

Total entries displayed: 2
```

#### Вопрос:

Что такое chassis ID  для коммутатора S2?

Chassis ID — это обязательный элемент в протоколе Link Layer Discovery Protocol (LLDP), который идентифицирует сетевой порт устройства, передающего LLDPDU (Data Unit).

В данном случае - интерфейс Fa0/1 коммутатора S2.

c.	Соединитесь через консоль на всех устройствах и используйте команды LLDP, необходимые для отображения топологии физической сети только из выходных данных команды show.

Маршрутизатор R1
```
R1#show lldp neighbors detail
------------------------------------------------
Chassis id: 0005.5E15.0D05
Port id: Fa0/5
Port Description: FastEthernet0/5
System Name: S1
System Description:
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Wed 26-Jun-13 02:49 by mnguyen
Time remaining: 90 seconds
System Capabilities: B
Enabled Capabilities: B
Management Addresses - not advertised
Auto Negotiation - supported, enabled
Physical media capabilities:
    100baseT(FD)
    100baseT(HD)
    1000baseT(HD)
Media Attachment Unit type: 10
Vlan ID: 1

Total entries displayed: 1
```
Коммутатор S1
```
S1#show lldp neighbors detail
------------------------------------------------
Chassis id: 0001.978B.E601
Port id: Fa0/1
Port Description: FastEthernet0/1
System Name: S2
System Description:
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Wed 26-Jun-13 02:49 by mnguyen
Time remaining: 90 seconds
System Capabilities: B
Enabled Capabilities: B
Management Addresses - not advertised
Auto Negotiation - supported, enabled
Physical media capabilities:
    100baseT(FD)
    100baseT(HD)
    1000baseT(HD)
Media Attachment Unit type: 10
Vlan ID: 1
------------------------------------------------
Chassis id: 0030.A3E0.9702
Port id: Gig0/0/1
Port Description: GigabitEthernet0/0/1
System Name: R1
System Description:
Cisco IOS XE Software, Version 03.13.04.S - Extended Support Release
Cisco IOS Software, ISR Software (X86_64_LINUX_IOSD-UNIVERSALK9-M), Version 15.5(3)S5, RELEASE SOFTWARE (fc2)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2017 by Cisco Systems, Inc.
Compiled Mon 05-Oct-15 11:24 by mcpre
Time remaining: 90 seconds
System Capabilities: R
Enabled Capabilities: R
Management Addresses - not advertised
Auto Negotiation - supported, enabled
Physical media capabilities:
    1000baseT(HD)
    100baseT(FD)
Media Attachment Unit type: 10
Vlan ID: 1

Total entries displayed: 2
```
Коммутатор S2
```
S2#show lldp neighbors detail
------------------------------------------------
Chassis id: 0005.5E15.0D01
Port id: Fa0/1
Port Description: FastEthernet0/1
System Name: S1
System Description:
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Wed 26-Jun-13 02:49 by mnguyen
Time remaining: 90 seconds
System Capabilities: B
Enabled Capabilities: B
Management Addresses - not advertised
Auto Negotiation - supported, enabled
Physical media capabilities:
    100baseT(FD)
    100baseT(HD)
    1000baseT(HD)
Media Attachment Unit type: 10
Vlan ID: 1

Total entries displayed: 1
```
#### Часть 4. Настройка NTP
В части 4 необходимо настроить маршрутизатор R1 в качестве сервера NTP, а маршрутизатор R2 в качестве клиента NTP маршрутизатора R1. 
Необходимо выполнить синхронизацию времени для Syslog и отладочных функций. Если время не синхронизировано, сложно определить, какое сетевое событие стало причиной данного сообщения.
#### Шаг 1. Выведите на экран текущее время.

Введите команду show clock для отображения текущего времени на R1. 
```
R1#show clock detail 
*0:48:48.540 UTC Mon Mar 1 1993
Time source is hardware calendar
```
Запишите отображаемые сведения о текущем времени в следующей таблице.

|Дата	         |Время	         | Часовой пояс|	Источник времени|
|-------|-------|-------|-------|
|01.03.1993    | 0:48:48.540   |        UTC   |      Time source is hardware calendar|

#### Шаг 2. Установите время.
С помощью команды clock set установите время на маршрутизаторе R1. Введенное время должно быть в формате UTC. 
```
R1#clock set 12:47:00 27 april 2026
``` 
#### Шаг 3. Настройте главный сервер NTP.
Настройте R1 в качестве хозяина NTP с уровнем слоя 4.
```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ntp master 4
``` 
#### Шаг 4. Настройте клиент NTP.
a.	Выполните соответствующую команду на S1 и S2, чтобы просмотреть настроенное время. 
```
S1#show clock
*0:57:18.416 UTC Mon Mar 1 1993
S2#show clock
*0:58:22.678 UTC Mon Mar 1 1993
```
Запишите текущее время,  в следующей таблице.

|Дата|	Время	|Часовой пояс|
|-------|-------|-------|
|01.03.1993   |  0:57:18.416 |          UTC|  ----- Коммутатор S1
|01.03.1993   |  0:58:22.678 |          UTC|  ----- Коммутатор S2

b.	Настройте S1 и S2 в качестве клиентов NTP. Используйте соответствующие команды NTP для получения времени от интерфейса G0/0/1 R1, а также для периодического обновления календаря или аппаратных часов коммутатора.
  

Шаг 5. Проверьте настройку NTP.
a.	Используйте соответствующую команду show , чтобы убедиться, что S1 и S2 синхронизированы с R1.
Примечание. Синхронизация метки времени на маршрутизаторе R2 с меткой времени на маршрутизаторе R1 может занять несколько минут.
b.	Выполните соответствующую команду на S1 и S2, чтобы просмотреть настроенное время и сравнить ранее записанное время.
Откройте окно конфигурации
Вопрос для повторения
Для каких интерфейсов в пределах сети не следует использовать протоколы обнаружения сетевых ресурсов? Поясните ответ.
