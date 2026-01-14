# Лабораторная работа. Настройка IPv6-адресов на сетевых устройствах 

# Топология
<img width="952" height="128" alt="image" src="https://github.com/user-attachments/assets/5f2d90d6-5e14-4cef-921f-8ddedb5ac01a" />

### Таблица адресации:

|Устройство|	Интерфейс|	IPv6-адрес|	Link local IPv6-адрес|	Длина префикса|	Шлюз по умолчанию|
|-------|-------|-------|-------|-------|---------|
|R1	|G0/0/0	|2001:db8:acad:a::1	|fe80::1	|64	|—
|	|G0/0/1	|2001:db8:acad:1::1 	|fe80::1	|64|	—
|S1	|VLAN 1	|2001:db8:acad:1::b	|fe80::b	|64|	—
|PC-A	|NIC	|2001:db8:acad:1::3	|SLACC	|64	|fe80::1
|PC-B	|NIC	|2001:db8:acad:a::3	|SLACC	|64	|fe80::1

# Задачи
### Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора
### Часть 2. Ручная настройка IPv6-адресов
### Часть 3. Проверка сквозного соединения

### Примечание:
Так как в Cisco Packet Tracer нет маршрутизатора Cisco 4221, будем использовать ISR 4321.
### Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора

### Сценарий начальной настройки устройств Cisco
a.	Привилегированный режим.

b.	Имя устройства.

c.	Отключите поиск DNS, чтобы предотвратить попытки неверного преобразования введённых команд так, как если бы они были узлами.

d.	Назначьте class в качестве пароля привилегированного режима.

e.	Назначьте cisco в качестве пароля консоли и включите вход по паролю.

f.	Назначьте cisco в качестве пароля виртуального терминала и включите вход по паролю.

g.	Зашифруйте пароли, хранящиеся в открытом виде.

h.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

i.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

#### Шаг 1. Настройте маршрутизатор.
Сбрасываем настройки на значения по умолчанию и перегружаем
```
Router>enable
Router#
Router#erase startup-config
Router#reload
```
Настраиваем маршрутизатор
```
Router>enable
Router#
Router#configure terminal
Router(config)#hostname R1
R1(config)#no ip domain-lookup
R1(config)#enable secret class
R1(config)#line con 0
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#line vty 0 4
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#line vty 5 15
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#service password-encryption
R1(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Prohibiting unauthorized access to the device!!!! #
R1(config)#
R1(config)#exit
R1#copy running-config st
R1#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
R1#
```
#### Шаг 2. Настройте коммутатор.
Сбрасываем настройки на значения по умолчанию и перегружаем
```
Switch>enable
Switch#
Switch#erase startup-config
Switch#reload
```
Настраиваем коммутатор
```
Switch>enable
Switch#
Switch#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostn
Switch(config)#hostname S1
S1(config)#no ip domain-lookup
S1(config)#enable secret class
S1(config)#line con 0
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#line vty 0 4
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#line vty 5 15
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#service passw
S1(config)#service password-encryption 
S1(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Prohibiting unauthorized access to the device!!!! #
S1(config)#exit
S1#
S1#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
S1#
```
### Часть 2. Ручная настройка IPv6-адресов
#### Шаг 1. Назначьте IPv6-адреса интерфейсам Ethernet на R1.
a.	Назначьте глобальные индивидуальные IPv6-адреса, указанные в таблице адресации обоим интерфейсам Ethernet на R1.
```
R1#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface g0/0/0
R1(config-if)#ipv6 address 2001:db8:acad:a::1/64
R1(config-if)#no shutdown
R1(config-if)#exit
R1(config)#interface g0/0/1
R1(config-if)#ipv6 address 2001:db8:acad:1::1/64
R1(config-if)#no shutdown
R1(config-if)#exit
R1(config)#exit
```
b.	Введите команду show ipv6 interface brief, чтобы проверить, назначен ли каждому интерфейсу корректный индивидуальный IPv6-адрес.
Примечание. Отображаемый локальный адрес канала основан на адресации EUI-64, которая автоматически использует MAC-адрес интерфейса для создания 128-битного локального IPv6-адреса канала.
```
R1#show ipv6 interface brief
GigabitEthernet0/0/0       [up/up]
    FE80::201:64FF:FE41:AE01
    2001:DB8:ACAD:A::1
GigabitEthernet0/0/1       [up/up]
    FE80::201:64FF:FE41:AE02
    2001:DB8:ACAD:1::1
Vlan1                      [administratively down/down]
    unassigned
R1#
```
Адреса назначены обоим интерфейсам.
c.	Чтобы обеспечить соответствие локальных адресов канала индивидуальному адресу, вручную введите локальные адреса канала на каждом интерфейсе Ethernet на R1.
Примечание. Каждый интерфейс маршрутизатора относится к отдельной сети. Пакеты с локальным адресом канала никогда не выходят за пределы локальной сети, а значит, для обоих интерфейсов можно указывать один и тот же локальный адрес канала.
```
R1#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface g0/0/0
R1(config-if)#ipv6 address fe80::1 link-local 
R1(config-if)#exit
R1(config)#interface g0/0/1
R1(config-if)#ipv6 address fe80::1 link-local 
R1(config-if)#exit
R1(config)#
```
d.	Используйте выбранную команду, чтобы убедиться, что локальный адрес связи изменен на fe80::1.  
```
R1#show ipv6 interface g0/0/0
GigabitEthernet0/0/0 is up, line protocol is up
IPv6 is enabled, link-local address is FE80::1
  No Virtual link-local address(es):
  Global unicast address(es):
    2001:DB8:ACAD:A::1, subnet is 2001:DB8:ACAD:A::/64
  Joined group address(es):
    FF02::1
    FF02::1:FF00:1
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
R1#show ipv6 interface g0/0/1
GigabitEthernet0/0/1 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::1
  No Virtual link-local address(es):
  Global unicast address(es):
    2001:DB8:ACAD:1::1, subnet is 2001:DB8:ACAD:1::/64
  Joined group address(es):
    FF02::1
    FF02::1:FF00:1
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
R1#
```
Локальные адреса связи поменялись на обоих интерфейсах.

Вопрос:
Какие группы многоадресной рассылки назначены интерфейсу G0/0?
 FF02::1
    FF02::1:FF00:1
#### Шаг 2. Активируйте IPv6-маршрутизацию на R1.
a.	В командной строке на PC-B введите команду ipconfig, чтобы получить данные IPv6-адреса, назначенного интерфейсу ПК.
<img width="619" height="261" alt="image" src="https://github.com/user-attachments/assets/84aed17a-1c99-41d1-a2f6-6feef96283d9" />
Индивидуальный IPv6-адрес сетевой интерфейсной карте (NIC) на PC-B не назначен
b.	Активируйте IPv6-маршрутизацию на R1 с помощью команды IPv6 unicast-routing.
, чтобы убедиться, что новая многоадресная группа назначена интерфейсу G0/0/0. Обратите внимание, что в списке групп для интерфейса G0/0 отображается группа многоадресной рассылки всех маршрутизаторов (FF02::2).
Joined group address(es):
    FF02::1
    FF02::2
    FF02::1:FF00:1
Примечание. Это позволит компьютерам получать IP-адреса и данные шлюза по умолчанию с помощью функции SLAAC (Stateless Address Autoconfiguration (Автоконфигурация без сохранения состояния адреса)).
c.	Теперь, когда R1 входит в группу многоадресной рассылки всех маршрутизаторов, еще раз введите команду ipconfig на PC-B. Проверьте данные IPv6-адреса.
<img width="613" height="251" alt="image" src="https://github.com/user-attachments/assets/e801a85c-cc57-470a-860f-2ee683521751" />
Назначен ipv6 адрес и шлюз по умолчанию.

Вопрос:
Почему PC-B получил глобальный префикс маршрутизации и идентификатор подсети, которые вы настроили на R1?

