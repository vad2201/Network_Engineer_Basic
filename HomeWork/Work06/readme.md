# Лабораторная работа - Внедрение маршрутизации между виртуальными локальными сетями 

# Топология

 <img width="783" height="297" alt="image" src="https://github.com/user-attachments/assets/ced87a12-6970-44ae-991f-4519a67598a9" />

### Таблица адресации

|Устройство|	Интерфейс|	IP-адрес |	Маска подсети |	Шлюз по умолчанию|
|-------|-------|-------|-------|-------|
|R1	|G0/0/1.10	|192.168.10.1	|255.255.255.0	| —
|R1	|G0/0/1.20	|192.168.20.1	|255.255.255.0	| —
|R1	|G0/0/1.30	|192.168.30.1	|255.255.255.0	| —
|R1	|G0/0/1.1000	|—|—	| —
|S1	|VLAN 10	|192.168.10.11	|255.255.255.0	|192.168.10.1
|S2	|VLAN 10	|192.168.10.12	|255.255.255.0	|192.168.10.1
|PC-A	|NIC	|192.168.20.3	|255.255.255.0	|192.168.20.1
|PC-B	|NIC	|192.168.30.3	|255.255.255.0	|192.168.30.1

### Таблица VLAN

|VLAN| Имя|Назначенный интерфейс|
|-------|-------|-------|
|10|Управление|S1: VLAN 10| 
| | |S2: VLAN 10| 
|20|	Sales|	S1: F0/6
|30|Operations|	S2: F0/18
|999|	Parking_Lot|	С1: F0/2-4, F0/7-24, G0/1-2
|||С2: F0/2-17, F0/19-24, G0/1-2
|1000|Собственная|	—

# Задачи
### Часть 1. Создание сети и настройка основных параметров устройства
### Часть 2. Создание сетей VLAN и назначение портов коммутатора
### Часть 3. Настройка транка 802.1Q между коммутаторами.
### Часть 4. Настройка маршрутизации между сетями VLAN
### Часть 5. Проверка, что маршрутизация между VLAN работает

### Примечание:
Так как в Cisco Packet Tracer нет маршрутизатора Cisco 4221, будем использовать ISR 4321.

### Часть 1. Создание сети и настройка основных параметров устройства
В первой части лабораторной работы вам предстоит создать топологию сети и настроить базовые параметры для узлов ПК и коммутаторов.

#### Шаг 1. Создайте сеть согласно топологии.
Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

<img width="539" height="260" alt="image" src="https://github.com/user-attachments/assets/7703925d-7c35-4b2b-a7d7-9dee1ad51d92" />

#### Шаг 2. Настройте базовые параметры для маршрутизатора.

Перед настройкой роутера и коммутаторов, обнулим их начальную конфигурацию.

Сбрасываем настройки маршрутизатора на значения по умолчанию и перегружаем
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
Сбрасываем настройки коммутатора на значения по умолчанию и перегружаем. Процедуру повторяем на обоих коммутаторах.
```
Switch>enable
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
C2960 Boot Loader (C2960-HBOOT-M) Version 12.2(25r)FX, RELEASE SOFTWARE (fc4)
Cisco WS-C2960-24TT (RC32300) processor (revision C0) with 21039K bytes of memory.
2960-24TT starting...
```
a.	Подключитесь к маршрутизатору с помощью консоли и активируйте привилегированный режим EXEC.
Откройте окно конфигурации
<img width="681" height="107" alt="image" src="https://github.com/user-attachments/assets/1594cde6-8ece-47d6-8fde-5e607f83d26f" />

b.	Войдите в режим конфигурации.

c.	Назначьте маршрутизатору имя устройства.

d.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

e.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

f.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

g.	Установите cisco в качестве пароля виртуального терминала и активируйте вход.

h.	Зашифруйте открытые пароли.

i.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

j.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```
Router#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#
Router(config)#hostname R1
R1(config)#no ip domain-lookup
R1(config)#enable secret class
R1(config)#line con 0
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#service password-encryption
R1(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Prohibiting unauthorized access to the device!!!! #
R1(config)#exit
R1#
R1#wr
Building configuration...
[OK]
R1#
```
k.	Настройте на маршрутизаторе время.

Так как наш маршрутизатор не подключен к интернету, мы не можем настроить ntp сервер. Будем настраивать время вручную.
```
R1#clock set 15:02:20 28 January 2026
R1#show clock
15:2:26.357 UTC Wed Jan 28 2026
R1#
```

#### Шаг 3. Настройте базовые параметры каждого коммутатора.
a.	Присвойте коммутатору имя устройства.

b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e.	Установите cisco в качестве пароля виртуального терминала и активируйте вход.

f.	Зашифруйте открытые пароли.

g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h.	Настройте на коммутаторах время.

i.	Сохранение текущей конфигурации в качестве начальной.

##### Коммутатор S1
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
S1(config-line)#line vty 0 15
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#service password-encryption 
S1(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Prohibiting unauthorized access to the device!!!! #
S1(config)#exit
S1#clock set 15:53:20 28 January 2026
S1#show clock
15:54:29.533 UTC Wed Jan 28 2026
S1#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
S1#
```
##### Коммутатор S2
```
Switch>enable
Switch#configure t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S2
S2(config)#no ip domain-lookup 
S2(config)#enable secret class
S2(config)#line con 0
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#line vty 0 15
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#service password-encryption 
S2(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Prohibiting unauthorized access to the device!!!! #
S2(config)#exit
S2#
S2#clock set 16:01:30 28 January 2026
S2#show clock
16:1:35.831 UTC Wed Jan 28 2026
S2#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
S2#
```
##### Примечание:
Для коммутаторов можно было указать в качестве ntp сервера наш роутер, но на данном этапе мы еще не настраиваем ip адреса. Так что дату и время  тоже настраиваем вручную.

#### Шаг 4. Настройте узлы ПК.
##### Компьютер PC-A

IP-адрес и маска подсети

<img width="507" height="130" alt="image" src="https://github.com/user-attachments/assets/986865f9-239b-4153-911b-c7fa9116d877" />

Шлюз по умолчанию

<img width="485" height="165" alt="image" src="https://github.com/user-attachments/assets/a348052d-e219-4d2a-941d-5163db4dc434" />

##### Компьютер PC-B

IP-адрес и маска подсети

<img width="515" height="125" alt="image" src="https://github.com/user-attachments/assets/cc61956e-3d9d-47a5-8a19-71b5c0b60290" />

Шлюз по умолчанию

<img width="476" height="154" alt="image" src="https://github.com/user-attachments/assets/d4cf6252-8cc0-417b-9cb2-bf6ed4f3020d" />

### Часть 2. Создание сетей VLAN и назначение портов коммутатора

#### Шаг 1. Создайте сети VLAN на коммутаторах.

a.	Создайте и назовите необходимые VLAN на каждом коммутаторе из таблицы выше.
##### Коммутатор S1
```
S1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#vlan 10
S1(config-vlan)#name Management
S1(config-vlan)#exit
S1(config)#vlan 20 
S1(config-vlan)#name Sales
S1(config-vlan)#exit
S1(config)#vlan 30 
S1(config-vlan)#name Operations
S1(config-vlan)#exit
S1(config)#vlan 999
S1(config-vlan)#name Parking_Lot
S1(config-vlan)#exit
S1(config)#vlan 1000
S1(config-vlan)#name Own
S1(config-vlan)#exit
```
##### Коммутатор S2
```
S2#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
S2(config)#vlan 10
S2(config-vlan)#name Management
S2(config-vlan)#exit
S2(config)#vlan 20 
S2(config-vlan)#name Sales
S2(config-vlan)#exit
S2(config)#vlan 30 
S2(config-vlan)#name Operations
S2(config-vlan)#exit
S2(config)#vlan 999
S2(config-vlan)#name Parking_Lot
S2(config-vlan)#exit
S2(config)#vlan 1000
S2(config-vlan)#name Own
S2(config-vlan)#exit
```
b.	Настройте интерфейс управления и шлюз по умолчанию на каждом коммутаторе, используя информацию об IP-адресе в таблице адресации. 

##### Коммутатор S1
```
S1(config)#interface vlan 10
S1(config-if)#
%LINK-5-CHANGED: Interface Vlan10, changed state to up

S1(config-if)#ip address 192.168.10.11 255.255.255.0
S1(config-if)#no shutdown
S1(config-if)#ip default-gateway 192.168.10.1
```
##### Коммутатор S2
```
S2(config)#interface vlan 10
S2(config-if)#
%LINK-5-CHANGED: Interface Vlan10, changed state to up

S2(config-if)#ip address 192.168.10.12 255.255.255.0
S2(config-if)#no shutdown
S2(config-if)#ip default-gateway 192.168.10.1
```
c.	Назначьте все неиспользуемые порты коммутатора VLAN Parking_Lot, настройте их для статического режима доступа и административно деактивируйте их.
##### Коммутатор S1
```
S1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#interface range f0/2-4, f0/7-24, g0/1-2
S1(config-if-range)#switchport mode access
S1(config-if-range)#switchprot access vlan 999
S1(config-if-range)#shutdown
```
##### Коммутатор S2
```
S2#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
S2(config)#interface range f0/2-17, f0/19-24, g0/1-2
S2(config-if-range)#switchport mode access
S2(config-if-range)#switchprot access vlan 999
S2(config-if-range)#shutdown
```

#### Шаг 2. Назначьте сети VLAN соответствующим интерфейсам коммутатора.
a.	Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.
##### Коммутатор S1
```
S1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#interface f0/6
S1(config-if)#switchport mode access
S1(config-if)#switchport access vlan 20
S1(config-if)#no shutdown
S1(config-if)#end
S1#
```
##### Коммутатор S2
```
S2#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
S2(config)#interface f0/18
S2(config-if)#switch mode access
S2(config-if)#switch access vlan 30
S2(config-if)#no shutdown
```
b.	Убедитесь, что VLAN назначены на правильные интерфейсы.
##### Коммутатор S1
<img width="845" height="264" alt="image" src="https://github.com/user-attachments/assets/10ca86ec-62de-42b7-93e4-6cace20154a1" />

##### Коммутатор S2
<img width="799" height="258" alt="image" src="https://github.com/user-attachments/assets/5fd97995-4d8c-4a42-a9a3-cc8e90847606" />

### Часть 3. Конфигурация магистрального канала стандарта 802.1Q между коммутаторами
В части 3 вы вручную настроите интерфейс F0/1 как транк.

#### Шаг 1. Вручную настройте магистральный интерфейс F0/1 на коммутаторах S1 и S2.

a.	Настройка статического транкинга на интерфейсе F0/1 для обоих коммутаторов.
Откройте окно конфигурации
b.	Установите native VLAN 1000 на обоих коммутаторах.
c.	Укажите, что VLAN 10, 20, 30 и 1000 могут проходить по транку.
d.	Проверьте транки, native VLAN и разрешенные VLAN через транк.
##### Коммутатор S1
```
S1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#interface f0/1
S1(config-if)#switchport mode trunk
S1(config-if)#switchport trunk allowed vlan 10,20,30,1000
S1(config-if)#switchport trunk native vlan 1000
S1(config-if)#do show run
```
Проверяем транки, native VLAN и разрешенные VLAN через транк.

<img width="1168" height="120" alt="image" src="https://github.com/user-attachments/assets/1b3df63e-d8eb-47e9-b154-4460c0668363" />

##### Коммутатор S2
```
S2#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
S2(config)#interface f0/1
S2(config-if)#switchport mode trunk 
S2(config-if)#switchport trunk allowed vlan 10,20,30,1000 
S2(config-if)#switchport trunk native vlan 1000
S2(config-if)#do show run
Building configuration...
```
Проверяем транки, native VLAN и разрешенные VLAN через транк.

<img width="489" height="101" alt="image" src="https://github.com/user-attachments/assets/e7191022-b33f-43ca-8206-4c93d444065f" />

#### Шаг 2. Вручную настройте магистральный интерфейс F0/5 на коммутаторе S1.
a.	Настройте интерфейс S1 F0/5 с теми же параметрами транка, что и F0/1. Это транк до маршрутизатора.
```
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#interface f0/5
S1(config-if)#switchport mode trunk
S1(config-if)#switchport trunk allowed vlan 10,20,30,1000
S1(config-if)#switchport trunk native vlan 1000
```
b.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
S1(config-if)#end
S1#
S1#wr
Building configuration...
[OK]
```
c.	Проверка транкинга.
```
S1(config-if)#do show run
```
<img width="496" height="100" alt="image" src="https://github.com/user-attachments/assets/c97f02d1-9eb9-49e9-9dd2-34203da7c2d3" />

Вопрос:
Что произойдет, если G0/0/1 на R1 будет отключен?

Если G0/0/1 на R1 будет отключен - не будет доступен шлюз по умолчанию для коммутаторов и компьютеров, соответственно связь будет только между двумя коммутаторами.

### Часть 4. Настройка маршрутизации между сетями VLAN
#### Шаг 1. Настройте маршрутизатор.
a.	При необходимости активируйте интерфейс G0/0/1 на маршрутизаторе.
```
R1(config)#int G0/0/1
R1(config-if)#description trunk link to S1
R1(config-if)#no shutdown
```
b.	Настройте подинтерфейсы для каждой VLAN, как указано в таблице IP-адресации. Все подинтерфейсы используют инкапсуляцию 802.1Q. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса.
```
R1(config)#interface G0/0/1.10
R1(config-subif)#description default gateway for Management
R1(config-subif)#encapsulation dot1Q 10
R1(config-subif)#ip address 192.168.10.1 255.255.255.0
R1(config-subif)#exit
R1(config)#int G0/0/1.20
R1(config-subif)#description default gateway for Sales
R1(config-subif)#encapsulation dot1Q 20
R1(config-subif)#ip address 192.168.20.1 255.255.255.0
R1(config-subif)#exit
R1(config)#
R1(config)#int G0/0/1.30
R1(config-subif)#description default gateway for Operations
R1(config-subif)#encapsulation dot1Q 30
R1(config-subif)#ip address 192.168.30.1 255.255.255.0
R1(config-subif)#exit
R1(config)#int G0/0/1.1000
R1(config-subif)#description default gateway for Native
R1(config-subif)#encapsulation dot1Q 1000
R1(config-subif)#exit
R1(config)#
```
c.	Убедитесь, что вспомогательные интерфейсы работают
```
R1(config)#do show run
```
<img width="511" height="403" alt="image" src="https://github.com/user-attachments/assets/b760a5c2-89ce-4247-8a61-2a609ccb73c6" />

### Часть 5. Проверьте, работает ли маршрутизация между VLAN
#### Шаг 1. Выполните следующие тесты с PC-A. Все должно быть успешно.

a.	Отправьте эхо-запрос с PC-A на шлюз по умолчанию.

<img width="575" height="263" alt="image" src="https://github.com/user-attachments/assets/fea33e13-96dd-4163-bdc6-193d801c92b4" />

Связь успешно установлена.

b.	Отправьте эхо-запрос с PC-A на PC-B.

<img width="574" height="265" alt="image" src="https://github.com/user-attachments/assets/ceade3ac-a8fd-435e-8079-874e36b13966" />

Связь успешно установлена.

c.	Отправьте команду ping с компьютера PC-A на коммутатор S2.

<img width="578" height="267" alt="image" src="https://github.com/user-attachments/assets/bc0fa716-164f-4b03-a79d-2d6fb9e24f13" />

Связь успешно установлена.

Шаг 2. Пройдите следующий тест с PC-B
В окне командной строки на PC-B выполните команду tracert на адрес PC-A.

<img width="606" height="166" alt="image" src="https://github.com/user-attachments/assets/a8354e06-7b9e-4c9a-a74c-c87f7deb981e" />

Вопрос:
Какие промежуточные IP-адреса отображаются в результатах?

В команде tracert отображается адрес шлюза по умолчанию (он находится на роутере).
