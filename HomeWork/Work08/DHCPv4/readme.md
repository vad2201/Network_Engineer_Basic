# Лабораторная работа - Реализация DHCPv4 

# Топология
 <img width="1075" height="92" alt="image" src="https://github.com/user-attachments/assets/19c82064-489b-437a-926e-02bb6a095e71" />

### Таблица адресации
|Устройство|	Интерфейс|	IP-адрес |	Маска подсети | Шлюз по умолчанию |
|-------|-------|-------|-------|-------|
|R1	|G0/0/0|	10.0.0.1|	255.255.255.252|-|
|R1	|G0/0/1|	-|	-|-|
|R1	|G0/0/1.100|192.168.1.1	|255.255.255.192|-|
|R1	|G0/0/1.200|192.168.1.65|255.255.255.224|-|
|R1	|G0/0/1.1000|	|	|-|
|R2	|G0/0/0|	10.0.0.2|	255.255.255.252|-|
|R2	|G0/0/1|192.168.1.97	|255.255.255.240	||
|S1	|VLAN 200|	192.168.1.66|255.255.255.224|192.168.1.65 |
|S2	|VLAN 1|192.168.1.98	|	255.255.255.240|192.168.1.97 |
|PC-A	|NIC|DHCP	|DHCP	|DHCP |
|PC-B	|NIC|DHCP	|DHCP	|DHCP |

### Таблица VLAN
|VLAN|	Имя	|Назначенный интерфейс|
|-------|-------|-------|
|1	|Нет	|S2: F0/18|
|100|	Клиенты|	S1: F0/6 |
|200|	Управление|	S1: VLAN 200 | 
|999|	Parking_Lot|	S1: F0/1-4, F0/7-24, G0/1-2|
|1000|	Собственная|	—|

### Задачи
#### Часть 1. Создание сети и настройка основных параметров устройства
#### Часть 2. Настройка и проверка двух серверов DHCPv4 на R1
#### Часть 3. Настройка и проверка DHCP-ретрансляции на R2
	
#### Часть 1.	Создание сети и настройка основных параметров устройства

#### Шаг 1.	Создание схемы адресации

Подсеть сети 192.168.1.0/24 в соответствии со следующими требованиями:
```
11000000.10101000.00000001.00000000  -   сеть
11111111.11111111.11111111.00000000  -   маска
8 бит на подсети
```
a.	Одна подсеть «Подсеть A», поддерживающая 58 хостов (клиентская VLAN на R1).

Подсеть A

Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.100 . 
```
Делить начинаем с максимальной подсетки  58 хостов
00111111   - 63 хоста
Соответственно увеличиваем маску на 2 значения
11111111.11111111.11111111.11000000  -   маска   /26
Подсетки:
11000000.10101000.00000001.00000000
11000000.10101000.00000001.01000000
11000000.10101000.00000001.10000000
11000000.10101000.00000001.11000000
Подсеть A 58 хостов (клиентская VLAN на R1).
11000000.10101000.00000001.00000000
11111111.11111111.11111111.11000000  -   маска   /26
IP-адрес в таблице адресации для R1 G0/0/1.100
192.168.1.1 /26
```
b.	Одна подсеть «Подсеть B», поддерживающая 28 хостов (управляющая VLAN на R1). 

Подсеть B:

Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.200. 
Запишите второй IP-адрес в таблице адресов для S1 VLAN 200 и введите соответствующий шлюз по умолчанию.
```
«Подсеть B», поддерживающая 28 хостов (управляющая VLAN на R1)
11000000.10101000.00000001.01000000				192.168.1.64 /26
11111111.11111111.11111111.11000000  -   маска   /26
00011111   - 31 хост
Соответственно увеличиваем маску на 1 значение
11111111.11111111.11111111.11100000  -   маска   /27
Подсетки:
11000000.10101000.00000001.01000000
11000000.10101000.00000001.01100000
IP-адрес в таблице адресации для R1 G0/0/1.200
192.168.1.65 /27
IP-адрес в таблице адресов для S1 VLAN 200
192.168.1.66 /27
Шлюз по умолчанию
192.168.1.65 /27
```
c.	Одна подсеть «Подсеть C», поддерживающая 12 узлов (клиентская сеть на R2).

Подсеть C:

Запишите первый IP-адрес в таблице адресации для R2 G0/0/1.
```
«Подсеть C», поддерживающая 12 узлов (клиентская сеть на R2)
11000000.10101000.00000001.01100000
11111111.11111111.11111111.11100000  -   маска   /27
00001111   - 15 хостов
11111111.11111111.11111111.11110000  -   маска   /28
IP-адрес в таблице адресации для R2 G0/0/1
192.168.1.97 /28
IP-адрес в таблице адресов для S2 VLAN 1
192.168.1.98 /28
```

#### Шаг 2.	Создайте сеть согласно топологии.
Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.
<img width="920" height="123" alt="image" src="https://github.com/user-attachments/assets/efdffd5a-ea2d-45a0-b9af-e31a77e27b78" />

#### Шаг 3.	Произведите базовую настройку маршрутизаторов.
Произведем инициализацию маршрутизаторов
```
Router>enable
Router#erase startup-config 
Erasing the nvram filesystem will remove all configuration files! Continue? [confirm]
[OK]
Erase of nvram: complete
%SYS-7-NV_BLOCK_INIT: Initialized the geometry of nvram
Router#delete flash:vlan.dat
Delete filename [vlan.dat]?
Delete flash:/vlan.dat? [confirm]
%Error deleting flash:/vlan.dat (No such file or directory)
Router#reload
Proceed with reload? [confirm]
Initializing Hardware ...
```
Выполняем процедуру для каждого маршрутизатора.

a.	Назначьте маршрутизатору имя устройства.

b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

f.	Зашифруйте открытые пароли.

g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

i.	Установите часы на маршрутизаторе на сегодняшнее время и дату.
Маршрутизатор R1
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
R1(config)#exit
R1#clock set 10:38:30 17 february 2026
R1#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
R1#
```
Маршрутизатор R2
```
Router>enable
Router#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#hostname R2
R2(config)#no ip domain-lookup
R2(config)#enable secret class
R2(config)#line con 0
R2(config-line)#password cisco
R2(config-line)#login
R2(config-line)#exit
R2(config)#line vty 0 15
R2(config-line)#password cisco
R2(config-line)#login
R2(config-line)#exit
R2(config)#service password-encryption
R2(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Prohibiting unauthorized access to the device!!!! #
R2(config)#exit
R2#clock set 10:40:30 17 february 2026
R2#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
R2#
```
#### Шаг 4.	Настройка маршрутизации между сетями VLAN на маршрутизаторе R1
a.	Активируйте интерфейс G0/0/1 на маршрутизаторе.
```
R1(config)#int g0/0/1
R1(config-if)#no shutdown
```
b.	Настройте подинтерфейсы для каждой VLAN в соответствии с требованиями таблицы IP-адресации. Все субинтерфейсы используют инкапсуляцию 802.1Q и назначаются первый полезный адрес из вычисленного пула IP-адресов. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса.
```
R1(config)#interface G0/0/1.100
R1(config-subif)#description customers
R1(config-subif)#encapsulation dot1Q 100
R1(config-subif)#ip address 192.168.1.1 255.255.255.192
R1(config-subif)#exit
R1(config)#int G0/0/1.200
R1(config-subif)#description Management
R1(config-subif)#encapsulation dot1Q 200
R1(config-subif)#ip address 192.168.1.65 255.255.255.224
R1(config-subif)#exit
R1(config)#int G0/0/1.1000
R1(config-subif)#description default gateway for Native
R1(config-subif)#encapsulation dot1Q 1000
R1(config-subif)#exit
R1(config)#
```
c.	Убедитесь, что вспомогательные интерфейсы работают.

<img width="469" height="386" alt="image" src="https://github.com/user-attachments/assets/59a35af5-27c2-4bb1-af8f-c5f0785447d1" />

#### Шаг 5.	Настройте G0/1 на R2, затем G0/0/0 и статическую маршрутизацию для обоих маршрутизаторов
a.	Настройте G0/0/1 на R2 с первым IP-адресом подсети C, рассчитанным ранее.
```
R2(config)#int g0/0/1
R2(config-if)#ip address 192.168.1.97 255.255.255.240
```
b.	Настройте интерфейс G0/0/0 для каждого маршрутизатора на основе приведенной выше таблицы IP-адресации.
c.	Настройте маршрут по умолчанию на каждом маршрутизаторе, указываемом на IP-адрес G0/0/0 на другом маршрутизаторе.

Маршрутизатор R2
```
R2(config)#int g0/0/0
R2(config-if)#ip address 10.0.0.2  255.255.255.252
R2(config)#ip default-gateway 10.0.0.1
R2(config)#no shutdown
```
d.	Убедитесь, что статическая маршрутизация работает с помощью пинга до адреса G0/0/1 R2 от R1.
```
R1#ping 192.168.1.97
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.97, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
```
Маршрутизация не работает. Анализ в режиме Simulation показывает что роутер не понимает за каким интерфейсом находится сеть 192.168.1.96. Необходимо настроить статический маршрут
```
R1(config)#ip route  192.168.1.96 255.255.255.240 g0/0/0
%Default route without gateway, if not a point-to-point interface, may impact performance
R1(config)#exit
```
Проверяем связь
```
R1#ping 192.168.1.97
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.97, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 0/0/1 ms
R1#
```
Подсеть 192.168.1.96 доступна с R1.

e.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
R1#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
```
#### Шаг 6.	Настройте базовые параметры каждого коммутатора.
Произведем инициализацию коммутаторов
```
Switch#erase startup-config 
Erasing the nvram filesystem will remove all configuration files! Continue? [confirm]
[OK]
Erase of nvram: complete
%SYS-7-NV_BLOCK_INIT: Initialized the geometry of nvram
Switch#delete flash:vlan.dat
Delete filename [vlan.dat]?
Delete flash:/vlan.dat? [confirm]
%Error deleting flash:/vlan.dat (No such file or directory)

Switch#reload
Proceed with reload? [confirm]
```
Выполняем процедуру для каждого коммутатора.

a.	Присвойте коммутатору имя устройства.

b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

f.	Зашифруйте открытые пароли.

g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

i.	Установите часы на маршрутизаторе на сегодняшнее время и дату.

j.	Скопируйте текущую конфигурацию в файл загрузочной конфигурации.

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
S1(config)#exit
S1#
S1#wr
Building configuration...
[OK]
S1#
S1(config)#exit
S1#wr
Building configuration...
[OK]
S1#clock set 14:40:30 17 february 2026
S1#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
S1#
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
S2(config)#exit
S2#wr
Building configuration...
[OK]
S2#clock set 14:42:30 17 february 2026
S2#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
S2#
```

#### Шаг 7.	Создайте сети VLAN на коммутаторе S1.

Примечание. S2 настроен только с базовыми настройками. 

a.	Создайте необходимые VLAN на коммутаторе 1 и присвойте им имена из приведенной выше таблицы.
```
S1(config)#vlan 100
S1(config-vlan)#name Customers
S1(config-vlan)#exit
S1(config)#vlan 200
S1(config-vlan)#name Managament
S1(config-vlan)#exit
S1(config)#vlan 1000
S1(config-vlan)#name Native
S1(config-vlan)#exit
S1(config)#vlan 999
S1(config-vlan)#name Parking_Lot
S1(config-vlan)#exit
```

b.	Настройте и активируйте интерфейс управления на S1 (VLAN 200), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того установите шлюз по умолчанию на S1.
```
S1(config)#int vlan 200
S1(config-if)#ip address 192.168.1.66 255.255.255.224
S1(config-if)#ip default-gateway 192.168.1.65
S1(config-if)#no shutdown
```
c.	Настройте и активируйте интерфейс управления на S2 (VLAN 1), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того, установите шлюз по умолчанию на S2
```
S2(config)#int vlan 1
S2(config-if)#ip address 192.168.1.98 255.255.255.240
S2(config-if)#ip default-gateway 192.168.1.97
S2(config-if)#no shutdown
```
d.	Назначьте все неиспользуемые порты S1 VLAN Parking_Lot, настройте их для статического режима доступа и административно деактивируйте их. На S2 административно 
деактивируйте все неиспользуемые порты.

Коммутатор S1 
```
S1(config)#interface range F0/1-4, F0/7-24, G0/1-2
S1(config-if-range)#switchport mode access
S1(config-if-range)#switchport access vlan 999
S1(config-if-range)#shutdown
```

Коммутатор S2
```
S2(config)#int range f0/1-4,f0/6-17,f0/19-24,g0/1-2
S2(config-if-range)#shutdown
```
Примечание. Команда interface range полезна для выполнения этой задачи с минимальным количеством команд.

#### Шаг 8.	Назначьте сети VLAN соответствующим интерфейсам коммутатора.
a.	Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.
```
S1(config)#int f0/6
S1(config-if)#switchport mode access
S1(config-if)#switchport access vlan 100
```

b.	Убедитесь, что VLAN назначены на правильные интерфейсы.
<img width="839" height="330" alt="image" src="https://github.com/user-attachments/assets/03daae69-7c83-4ca6-a79d-bdee996f42c2" />

Вопрос:

Почему интерфейс F0/5 указан в VLAN 1?

По умолчанию все интерфейсы находятся в vlan 1, так как мы не переопределяли f0/5 - он в нем и остался.

#### Шаг 9.	Вручную настройте интерфейс S1 F0/5 в качестве транка 802.1Q.
a.	Измените режим порта коммутатора, чтобы принудительно создать магистральный канал.
b.	В рамках конфигурации транка  установите для native  VLAN значение 1000.
c.	В качестве другой части конфигурации магистрали укажите, что VLAN 100, 200 и 1000 могут проходить по транку.
d.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.
e.	Проверьте состояние транка.
#### Вопрос:
Какой IP-адрес был бы у ПК, если бы он был подключен к сети с помощью DHCP?
Закройте окно настройки.
### Часть 2.	Настройка и проверка двух серверов DHCPv4 на R1
В части 2 необходимо настроить и проверить сервер DHCPv4 на R1. Сервер DHCPv4 будет обслуживать две подсети, подсеть A и подсеть C.
#### Шаг 1.	Настройте R1 с пулами DHCPv4 для двух поддерживаемых подсетей. Ниже приведен только пул DHCP для подсети A
a.	Исключите первые пять используемых адресов из каждого пула адресов.
Откройте окно конфигурации
b.	Создайте пул DHCP (используйте уникальное имя для каждого пула).
c.	Укажите сеть, поддерживающую этот DHCP-сервер.
d.	В качестве имени домена укажите CCNA-lab.com.
e.	Настройте соответствующий шлюз по умолчанию для каждого пула DHCP.
f.	Настройте время аренды на 2 дня 12 часов и 30 минут.
g.	Затем настройте второй пул DHCPv4, используя имя пула R2_Client_LAN и вычислите сеть, маршрутизатор по умолчанию, и используйте то же имя домена и время аренды, что и предыдущий пул DHCP.
#### Шаг 2.	Сохраните конфигурацию.
Сохраните текущую конфигурацию в файл загрузочной конфигурации.
Закройте окно настройки.
#### Шаг 3.	Проверка конфигурации сервера DHCPv4
a.	Чтобы просмотреть сведения о пуле, выполните команду show ip dhcp pool .
b.	Выполните команду show ip dhcp bindings для проверки установленных назначений адресов DHCP.
c.	Выполните команду show ip dhcp server statistics для проверки сообщений DHCP.
#### Шаг 4.	Попытка получить IP-адрес от DHCP на PC-A
a.	Из командной строки компьютера PC-A выполните команду ipconfig /all.
b.	После завершения процесса обновления выполните команду ipconfig для просмотра новой информации об IP-адресе.
c.	Проверьте подключение с помощью пинга IP-адреса интерфейса R0 G0/0/1.
### Часть 3.	Настройка и проверка DHCP-ретрансляции на R2
В части 3 настраивается R2 для ретрансляции DHCP-запросов из локальной сети на интерфейсе G0/0/1 на DHCP-сервер (R1). 
#### Шаг 1.	Настройка R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1
a.	Настройте команду ip helper-address на G0/0/1, указав IP-адрес G0/0/0 R1.
Откройте окно конфигурации
b.	Сохраните конфигурацию.
#### Шаг 2.	Попытка получить IP-адрес от DHCP на PC-B
a.	Из командной строки компьютера PC-B выполните команду ipconfig /all.
b.	После завершения процесса обновления выполните команду ipconfig для просмотра новой информации об IP-адресе.
c.	Проверьте подключение с помощью пинга IP-адреса интерфейса R1 G0/0/1.
d.	Выполните show ip dhcp binding для R1 для проверки назначений адресов в DHCP.
e.	Выполните команду show ip dhcp server statistics для проверки сообщени

