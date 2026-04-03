# Лабораторная работа. Настройка и проверка расширенных списков контроля доступа.

# Топология
<img width="1006" height="362" alt="image" src="https://github.com/user-attachments/assets/de918650-83ba-4396-b0ce-93cc9118c970" />
 
### Таблица адресации

|Устройство|	Интерфейс |	IP-адрес |	Маска подсети |Шлюз по умолчанию|
|-------|-------|-------|-------|-------|
|R1|	G0/0/1|	—|	—|—| 
| |	G0/0/1.20|	10.20.0.1|	255.255.255.0|	 |
| |	G0/0/1.30|	10.30.0.1|	255.255.255.0|	 |
| |	G0/0/1.40|	10.40.0.1|	255.255.255.0|	 |
| |	G0/0/1.1000|	—	|—|	 |
| |	Loopback1|	172.16.1.1|	255.255.255.0|	 |
|R2|	G0/0/1|	10.20.0.4	|255.255.255.0	|—|
|S1|	VLAN 20|	10.20.0.2|	255.255.255.0	|10.20.0.1|
|S2|	VLAN 20|	10.20.0.3	|255.255.255.0	|10.20.0.1|
|PC-A|	NIC|	10.30.0.10|	255.255.255.0	|10.30.0.1|
|PC-B|	NIC|	10.40.0.10|	255.255.255.0|	10.40.0.1|

### Таблица VLAN

|VLAN	|Имя|	Назначенный интерфейс|
|-------|-------|-------|
|20	|Management|	S2: F0/5|
|30	|Operations|	S1: F0/6|
|40	|Sales	|S2: F0/18|
|999	|ParkingLot|	S1: F0/2-4, F0/7-24, G0/1-2|
| | |S2: F0/2-4, F0/6-17, F0/19-24, G0/1-2|
|1000	|Собственная	|—|



### Задачи
#### Часть 1. Создание сети и настройка основных параметров устройства
#### Часть 2. Настройка и проверка списков расширенного контроля доступа

#### Часть 1. Создание сети и настройка основных параметров устройства
#### Шаг 1. Создайте сеть согласно топологии.
<img width="700" height="251" alt="image" src="https://github.com/user-attachments/assets/2ce5e99e-e354-4bdc-982a-497b7e83140a" />

#### Шаг 2. Произведите базовую настройку маршрутизаторов.
Перед настройкой маршрутизаторов проведем их инициализацию:
```
enable
erase startup-config 
delete flash:vlan.dat
reload
```
Процедура выполняется на каждом устройстве.

a.	Назначьте маршрутизатору имя устройства.

b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

f.	Зашифруйте открытые пароли.

g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

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
R1#copy running-config startup-config 
Destination filename [startup-config]? 
%SYS-5-CONFIG_I: Configured from console by console

Building configuration...
[OK]
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
R2#copy running-config startup-config 
Destination filename [startup-config]? 
%SYS-5-CONFIG_I: Configured from console by console

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

g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

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
S1#copy running-config startup-config 
Destination filename [startup-config]? 
%SYS-5-CONFIG_I: Configured from console by console

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

S2(config)#exit
S2#copy running-config startup-config 
Destination filename [startup-config]? 
%SYS-5-CONFIG_I: Configured from console by console

Building configuration...
[OK]
```
#### Часть 2. Настройка сетей VLAN на коммутаторах.
#### Шаг 1. Создайте сети VLAN на коммутаторах.

a.	Создайте необходимые VLAN и назовите их на каждом коммутаторе из приведенной выше таблицы.

Коммутатор  S1
```
S1(config)#Vlan 20
S1(config-vlan)#Name Management
S1(config-vlan)#Exit
S1(config)#Vlan 30
S1(config-vlan)#Name Operations
S1(config-vlan)#Exit
S1(config)#Vlan 40 
S1(config-vlan)#Name Sales
S1(config-vlan)#Exit
S1(config)#Vlan 999
S1(config-vlan)#Name ParkingLot
S1(config-vlan)#Exit 
S1(config)#Vlan 1000
S1(config-vlan)#Name Native
S1(config-vlan)#exit
S1(config)#
```
Коммутатор S2
```
S2(config)#Vlan 20
S2(config-vlan)#Name Management
S2(config-vlan)#Exit
S2(config)#Vlan 30
S2(config-vlan)#Name Operations
S2(config-vlan)#Exit
S2(config)#Vlan 40 
S2(config-vlan)#Name Sales
S2(config-vlan)#Exit
S2(config)#Vlan 999
S2(config-vlan)#Name ParkingLot
S2(config-vlan)#Exit 
S2(config)#Vlan 1000
S2(config-vlan)#Name Native
S2(config-vlan)#exit
S2(config)#
```
b.	Настройте интерфейс управления и шлюз по умолчанию на каждом коммутаторе, используя информацию об IP-адресе в таблице адресации. 

Коммутатор S1
```
S1(config)#int vlan 20
S1(config-if)#
%LINK-5-CHANGED: Interface Vlan20, changed state to up

S1(config-if)#ip address 10.20.0.2 255.255.255.0
S1(config-if)#ip default-gateway 10.20.0.1
```
Коммутатор S2
```
S2(config)#int vlan 20
S2(config-if)#
%LINK-5-CHANGED: Interface Vlan20, changed state to up

S2(config-if)#ip address 10.20.0.3 255.255.255.0
S2(config-if)#ip default-gateway 10.20.0.1
```
c.	Назначьте все неиспользуемые порты коммутатора VLAN Parking Lot, настройте их для статического режима доступа и административно деактивируйте их.

Коммутатор S1
```
S1(config)#int range f0/2-4, f0/7-24, g0/1-2
S1(config-if-range)#switchport mode access
S1(config-if-range)#switchport access vlan 999
S1(config-if-range)#shutdown
```
Коммутатор S2
```
S2(config)#int range f0/2-4,f0/6-17,f0/19-24,g0/1-2
S2(config-if-range)#switchport mode access
S2(config-if-range)#switchport access vlan 999
S2(config-if-range)#shutdown
```
#### Шаг 2. Назначьте сети VLAN соответствующим интерфейсам коммутатора.
a.	Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.

Коммутатор S1
```
S1(config)#int f0/6
S1(config-if)#switchport mode access
S1(config-if)#switchport access vlan 30
```
Коммутатор S2
```
S2(config)#int f0/5
S2(config-if)#switchport mode access
S2(config-if)#switchport access vlan 20
S2(config-if)#exit
S2(config)#int f0/18
S2(config-if)#switchport mode access
S2(config-if)#switchport access vlan 40
```
b.	Выполните команду show vlan brief, чтобы убедиться, что сети VLAN назначены правильным интерфейсам.

Коммутатор S1
```
S1#show vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/5
20   Management                       active    
30   Operations                       active    Fa0/6
40   Sales                            active    
999  ParkingLot                       active    Fa0/2, Fa0/3, Fa0/4, Fa0/7
                                                Fa0/8, Fa0/9, Fa0/10, Fa0/11
                                                Fa0/12, Fa0/13, Fa0/14, Fa0/15
                                                Fa0/16, Fa0/17, Fa0/18, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24, Gig0/1, Gig0/2
1000 native                           active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active
```
Коммутатор S2
```
S2#show vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1
20   Management                       active    Fa0/5
30   Operations                       active    
40   Sales                            active    Fa0/18
999  ParkingLot                       active    Fa0/2, Fa0/3, Fa0/4, Fa0/6
                                                Fa0/7, Fa0/8, Fa0/9, Fa0/10
                                                Fa0/11, Fa0/12, Fa0/13, Fa0/14
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24, Gig0/1, Gig0/2
1000 native                           active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
```
#### Часть 3. Настройте транки (магистральные каналы).
#### Шаг 1. Вручную настройте магистральный интерфейс F0/1.

a.	Измените режим порта коммутатора на интерфейсе F0/1, чтобы принудительно создать магистральную связь. Не забудьте сделать это на обоих коммутаторах.

b.	В рамках конфигурации транка установите для native vlan значение 1000 на обоих коммутаторах. При настройке двух интерфейсов для разных собственных VLAN сообщения об ошибках могут отображаться временно.

c.	В качестве другой части конфигурации транка укажите, что VLAN 20, 30, 40 и 1000 разрешены в транке.

d.	Выполните команду show interfaces trunk для проверки портов магистрали, собственной VLAN и разрешенных VLAN через магистраль.

Коммутатор S1
```
S1(config)#int f0/1
S1(config-if)#switchport mode trunk
S1(config-if)#switchport trunk allowed vlan 20,30,40,1000
S1(config-if)#switchport trunk native vlan 1000
S1(config-if)#end

S1#show interfaces trunk
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1000

Port        Vlans allowed on trunk
Fa0/1       20,30,40,1000

Port        Vlans allowed and active in management domain
Fa0/1       20,30,40,1000

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       20,30,40

```
Коммутатор S2
```
S2(config)#int f0/1
S2(config-if)#switchport mode trunk
S2(config-if)#switchport trunk allowed vlan 20,30,40,1000
S2(config-if)#switchport trunk native vlan 1000
S2(config-if)#end

S2#show interfaces trunk
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1000

Port        Vlans allowed on trunk
Fa0/1       20,30,40,1000

Port        Vlans allowed and active in management domain
Fa0/1       20,30,40,1000

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       20,30,40,1000
```
#### Шаг 2. Вручную настройте магистральный интерфейс F0/5 на коммутаторе S1.

a.	Настройте интерфейс S1 F0/5 с теми же параметрами транка, что и F0/1. Это транк до маршрутизатора.

b.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

c.	Используйте команду show interfaces trunk для проверки настроек транка.
```
S1(config)#int f0/5
S1(config-if)#switchport mode trunk
S1(config-if)#switchport trunk allowed vlan 20,30,40,1000
S1(config-if)#switchport trunk native vlan 1000
S1(config-if)#end

S1#show interfaces trunk
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1000

Port        Vlans allowed on trunk
Fa0/1       20,30,40,1000

Port        Vlans allowed and active in management domain
Fa0/1       20,30,40,1000

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       20,30,40,1000
```
#### Часть 4. Настройте маршрутизацию.
#### Шаг 1. Настройка маршрутизации между сетями VLAN на R1.
Откройте окно конфигурации
a.	Активируйте интерфейс G0/0/1 на маршрутизаторе.
```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#int g0/0/1
R1(config-if)#no shutdown
```
b.	Настройте подинтерфейсы для каждой VLAN, как указано в таблице IP-адресации. Все подинтерфейсы используют инкапсуляцию 802.1Q. Убедитесь, что подинтерфейс для собственной VLAN не имеет назначенного IP-адреса. Включите описание для каждого подинтерфейса.
```
R1(config)#
interface G0/0/1.20
description Management
encapsulation dot1Q 20
ip address 10.20.0.1 255.255.255.0
exit
interface G0/0/1.30
description Operations
encapsulation dot1Q 30
ip address 10.30.0.1 255.255.255.0
exit
interface G0/0/1.40
description Sales
encapsulation dot1Q 40
ip address 10.40.0.1 255.255.255.0
exit
interface G0/0/1.1000
description Native
encapsulation dot1Q 100
```
c.	Настройте интерфейс Loopback 1 на R1 с адресацией из приведенной выше таблицы.
```
R1(config)#int loopback 1
R1(config-if)#ip address 172.16.1.1 255.255.255.0
```
d.	С помощью команды show ip interface brief проверьте конфигурацию подынтерфейса.
```
R1(config)#do show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0/0   unassigned      YES NVRAM  administratively down down 
GigabitEthernet0/0/1   unassigned      YES NVRAM  up                    up 
GigabitEthernet0/0/1.20 10.20.0.1       YES manual up                    up 
GigabitEthernet0/0/1.30 10.30.0.1       YES manual up                    up 
GigabitEthernet0/0/1.40 10.40.0.1       YES manual up                    up 
GigabitEthernet0/0/1.1000 unassigned      YES unset  up                    up 
Loopback1              172.16.1.1      YES manual up                    up 
Vlan1                  unassigned      YES NVRAM  administratively down down
```
#### Шаг 2. Настройка интерфейса R2 g0/0/1 с использованием адреса из таблицы и маршрута по умолчанию с адресом следующего перехода 10.20.0.1
```
R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#int g0/0/1
R2(config-if)#ip address 10.20.0.4 255.255.255.0
R2(config-if)#no shutdown
R2(config-if)#ip default-gateway 10.20.0.1
```
#### Часть 5. Настройте удаленный доступ
#### Шаг 1. Настройте все сетевые устройства для базовой поддержки SSH.

a.	Создайте локального пользователя с именем пользователя SSHadmin и зашифрованным паролем $cisco123!

b.	Используйте ccna-lab.com в качестве доменного имени.

c.	Генерируйте криптоключи с помощью 1024 битного модуля.

d.	Настройте первые пять линий VTY на каждом устройстве, чтобы поддерживать только SSH-соединения и с локальной аутентификацией.

Маршрутизатор R1
```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ip domain-name ccna-lab.com
R1(config)#crypto key generate rsa
The name for the keys will be: R1.ccna-lab.com
Choose the size of the key modulus in the range of 360 to 2048 for your
  General Purpose Keys. Choosing a key modulus greater than 512 may take
  a few minutes.

How many bits in the modulus [512]: 1024
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]

R1(config)#ip ssh version 2
*Mar 1 1:10:58.984: %SSH-5-ENABLED: SSH 1.99 has been enabled
R1(config)#username SSHadmin privilege 15 secret $cisco123!
R1(config)#line vty 0 4
R1(config-line)#transport input ssh
R1(config-line)#login local
R1(config-line)#
```
Маршрутизатор R2
```
R2(config)#ip domain-name ccna-lab.com
R2(config)#crypto key generate RSA
The name for the keys will be: R2.ccna-lab.com
Choose the size of the key modulus in the range of 360 to 2048 for your
  General Purpose Keys. Choosing a key modulus greater than 512 may take
  a few minutes.

How many bits in the modulus [512]: 1024
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]

R2(config)#ip ssh version 2
*Mar 1 1:26:9.76: %SSH-5-ENABLED: SSH 1.99 has been enabled
R2(config)#username SSHadmin privilege 15 secret $cisco123!
R2(config)#line vty 0 4
R2(config-line)#transport input ssh
R2(config-line)#login local
R2(config-line)#
```
Коммутатор S1
```
S1(config)#ip domain-name ccna-lab.com
S1(config)#crypto key generate RSA
The name for the keys will be: S1.ccna-lab.com
Choose the size of the key modulus in the range of 360 to 2048 for your
  General Purpose Keys. Choosing a key modulus greater than 512 may take
  a few minutes.

How many bits in the modulus [512]: 1024
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]

S1(config)#ip ssh version 2
*Mar 1 1:27:51.878: %SSH-5-ENABLED: SSH 1.99 has been enabled
S1(config)#username SSHadmin privilege 15 secret $cisco123!
S1(config)#line vty 0 4
S1(config-line)#transport input ssh
S1(config-line)#login local
```
Коммутатор S2
```
S2(config)#ip domain-name ccna-lab.com
S2(config)#crypto key generate RSA
The name for the keys will be: S2.ccna-lab.com
Choose the size of the key modulus in the range of 360 to 2048 for your
  General Purpose Keys. Choosing a key modulus greater than 512 may take
  a few minutes.

How many bits in the modulus [512]: 1024
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]

S2(config)#ip ssh version 2
*Mar 1 1:29:23.497: %SSH-5-ENABLED: SSH 1.99 has been enabled
S2(config)#username SSHadmin privilege 15 secret $cisco123!
S2(config)#line vty 0 4
S2(config-line)#transport input ssh
S2(config-line)#login local
```
#### Шаг 2. Включите защищенные веб-службы с проверкой подлинности на R1.

a.	Включите сервер HTTPS на R1.

R1(config)# ip http secure-server 

b.	Настройте R1 для проверки подлинности пользователей, пытающихся подключиться к веб-серверу.

R1(config)# ip http authentication local

```
R1(config)#ip http secure-server
               ^
% Invalid input detected at '^' marker.
	
R1(config)# ip http authentication local
                ^
% Invalid input detected at '^' marker.
```
Команды http в CPT не поддерживаются.
Для проверки HTTP подключим к R1 сервер и соединим его через интерфейс g0/0/0
<img width="218" height="221" alt="image" src="https://github.com/user-attachments/assets/2638d855-58b9-4875-9841-4616cda04719" />

Настроим g0/0/0.

```
R1(config)#int g0/0/0
R1(config-if)#no shutdown
R1(config-if)#ip address 10.50.0.1 255.255.255.0
```

Настроим сервер.

<img width="666" height="249" alt="image" src="https://github.com/user-attachments/assets/ae3b5c58-6c7e-43b5-8fd1-98c99ff89419" />

Включим на нем HTTPS.

<img width="658" height="214" alt="image" src="https://github.com/user-attachments/assets/0f35e1e3-7f28-420b-87ac-f9e511fa8e83" />

#### Часть 6. Проверка подключения
#### Шаг 1. Настройте узлы ПК.

Компьютер PC-A

<img width="664" height="272" alt="image" src="https://github.com/user-attachments/assets/f8187bf5-1140-469f-b38b-64aaab8c8d5d" />

Компьютер PC-B

<img width="666" height="282" alt="image" src="https://github.com/user-attachments/assets/4dd7700d-2fcd-480d-a80a-d7c5a3feffc8" />

#### Шаг 2. Выполните следующие тесты. Эхозапрос должен пройти успешно.

Примечание. Возможно, вам придется отключить брандмауэр ПК для работы ping

От	Протокол	Назначение

PC-A	Ping	10.40.0.10

```
C:\>ping 10.40.0.10
Pinging 10.40.0.10 with 32 bytes of data:
Request timed out.
Reply from 10.40.0.10: bytes=32 time=1ms TTL=127
Reply from 10.40.0.10: bytes=32 time=1ms TTL=127
Reply from 10.40.0.10: bytes=32 time=1ms TTL=127
Ping statistics for 10.40.0.10:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss)
```
    
PC-A	Ping	10.20.0.1
```
C:\>ping 10.20.0.1
Pinging 10.20.0.1 with 32 bytes of data:
Reply from 10.20.0.1: bytes=32 time<1ms TTL=255
Reply from 10.20.0.1: bytes=32 time<1ms TTL=255
Reply from 10.20.0.1: bytes=32 time<1ms TTL=255
Reply from 10.20.0.1: bytes=32 time=1ms TTL=255
Ping statistics for 10.20.0.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

PC-B	Ping	10.30.0.10
```
C:\>ping 10.30.0.10
Pinging 10.30.0.10 with 32 bytes of data:
Reply from 10.30.0.10: bytes=32 time=1ms TTL=127
Reply from 10.30.0.10: bytes=32 time=1ms TTL=127
Reply from 10.30.0.10: bytes=32 time=1ms TTL=127
Reply from 10.30.0.10: bytes=32 time<1ms TTL=127
Ping statistics for 10.30.0.10:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

PC-B	Ping	10.20.0.1
```
C:\>ping 10.20.0.1
Pinging 10.20.0.1 with 32 bytes of data:
Reply from 10.20.0.1: bytes=32 time<1ms TTL=255
Reply from 10.20.0.1: bytes=32 time<1ms TTL=255
Reply from 10.20.0.1: bytes=32 time<1ms TTL=255
Reply from 10.20.0.1: bytes=32 time=1ms TTL=255
Ping statistics for 10.20.0.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

PC-B	Ping	172.16.1.1
```
C:\>ping 172.16.1.1
Pinging 172.16.1.1 with 32 bytes of data:
Reply from 172.16.1.1: bytes=32 time<1ms TTL=255
Reply from 172.16.1.1: bytes=32 time<1ms TTL=255
Reply from 172.16.1.1: bytes=32 time<1ms TTL=255
Reply from 172.16.1.1: bytes=32 time=1ms TTL=255
Ping statistics for 172.16.1.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

PC-B	HTTPS	10.20.0.1

<img width="675" height="147" alt="image" src="https://github.com/user-attachments/assets/ff72a944-96ed-4fa2-8ef6-e9d9409a1551" />

PC-B	HTTPS	172.16.1.1

<img width="675" height="151" alt="image" src="https://github.com/user-attachments/assets/391b1de5-b68d-4f34-afcf-6589b09272bc" />

Доступ по HTTPS не доступен , так как мы не смогли включить HTTP на R1.

Проверим HTTPS через сервер, который мы подлючили к R1.

<img width="671" height="314" alt="image" src="https://github.com/user-attachments/assets/6bafe6ca-c9f2-481f-917b-0abe29071a09" />

HTTP работает.

PC-B	SSH	10.20.0.1
```
C:\>ssh -l SSHadmin 10.20.0.1

Password: 


Prohibiting unauthorized access to the device!!!! 

R1#
```

Подключение работает.

PC-B	SSH	172.16.1.1
```
C:\>ssh -l SSHadmin 172.16.1.1

Password: 


Prohibiting unauthorized access to the device!!!! 

R1#
```

Подключение работает.

#### Часть 7. Настройка и проверка списков контроля доступа (ACL)

При проверке базового подключения компания требует реализации следующих политик безопасности:

Политика1. Сеть Sales не может использовать SSH в сети Management (но в  другие сети SSH разрешен). 

Политика 2. Сеть Sales не имеет доступа к IP-адресам в сети Management с помощью любого веб-протокола (HTTP/HTTPS). Сеть Sales также не имеет доступа к интерфейсам R1 с помощью любого веб-протокола. Разрешён весь другой веб-трафик (обратите внимание — Сеть Sales  может получить доступ к интерфейсу Loopback 1 на R1).

Политика3. Сеть Sales не может отправлять эхо-запросы ICMP в сети Operations или Management. Разрешены эхо-запросы ICMP к другим адресатам. 

Политика 4: Cеть Operations  не может отправлять ICMP эхозапросы в сеть Sales. Разрешены эхо-запросы ICMP к другим адресатам. 

#### Шаг 1. Проанализируйте требования к сети и политике безопасности для планирования реализации ACL.
 
#### Шаг 2. Разработка и применение расширенных списков доступа, которые будут соответствовать требованиям политики безопасности.
Откройте окно конфигурации
 
Закройте окно настройки.
#### Шаг 3. Убедитесь, что политики безопасности применяются развернутыми списками доступа.
Выполните следующие тесты. Ожидаемые результаты показаны в таблице:
От	Протокол	Назначение	Результат
PC-A	Ping	10.40.0.10	Сбой
PC-A	Ping	10.20.0.1	Успех
PC-B	Ping	10.30.0.10	Сбой
PC-B	Ping	10.20.0.1	Сбой
PC-B	Ping	172.16.1.1	Успех
PC-B	HTTPS	10.20.0.1	Сбой
PC-B	HTTPS	172.16.1.1	Успех
PC-B	SSH	10.20.0.4	Сбой
PC-B	SSH	172.16.1.1	Успех
Конец документа

