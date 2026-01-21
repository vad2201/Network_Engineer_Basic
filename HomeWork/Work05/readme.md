# Лабораторная работа. Доступ к сетевым устройствам по протоколу SSH

# Топология

<img width="665" height="139" alt="image" src="https://github.com/user-attachments/assets/5bc10035-068c-4ab5-a800-dce5c3cd4aee" />

### Таблица адресации

|Устройство|	Интерфейс|	IP-адрес |	Маска подсети |	Шлюз по умолчанию|
|-------|-------|-------|-------|-------|
|R1	|G0/0/1	|192.168.1.1	|255.255.255.0	| —
|S1	|VLAN 1	|192.168.1.11	|255.255.255.0	|192.168.1.1
|PC-A	|NIC	|192.168.1.3	|255.255.255.0	|192.168.1.1

# Задачи
### Часть 1. Настройка основных параметров устройства
### Часть 2. Настройка маршрутизатора для доступа по протоколу SSH
### Часть 3. Настройка коммутатора для доступа по протоколу SSH
### Часть 4. SSH через интерфейс командной строки (CLI) коммутатора

### Примечание:
Так как в Cisco Packet Tracer нет маршрутизатора Cisco 4221, будем использовать ISR 4321.

### Часть 1. Настройка основных параметров устройств
В части 1 потребуется настроить топологию сети и основные параметры, такие как IP-адреса интерфейсов, доступ к устройствам и пароли на маршрутизаторе.

#### Шаг 1. Создайте сеть согласно топологии.

<img width="469" height="141" alt="image" src="https://github.com/user-attachments/assets/141094f4-8cc1-4bae-8da4-bb46164e6e17" />

#### Шаг 2. Выполните инициализацию и перезагрузку маршрутизатора и коммутатора.

Сбрасываем настройки маршрутизатора на значения по умолчанию и перегружаем
```
Router>enable
Router#erase startup-config 
Erasing the nvram filesystem will remove all configuration files! Continue? [confirm]
[OK]
Erase of nvram: complete
%SYS-7-NV_BLOCK_INIT: Initialized the geometry of nvram
Router#reload
Proceed with reload? [confirm]
Initializing Hardware ...
```
Сбрасываем настройки коммутатора на значения по умолчанию и перегружаем
```
Switch#erase startup-config 
Erasing the nvram filesystem will remove all configuration files! Continue? [confirm]
[OK]
Erase of nvram: complete
%SYS-7-NV_BLOCK_INIT: Initialized the geometry of nvram
Switch#reload
System configuration has been modified. Save? [yes/no]:y
Building configuration...
[OK]
Proceed with reload? [confirm]
C2960 Boot Loader (C2960-HBOOT-M) Version 12.2(25r)FX, RELEASE SOFTWARE (fc4)
Cisco WS-C2960-24TT (RC32300) processor (revision C0) with 21039K bytes of memory.
2960-24TT starting...
```
#### Шаг 3. Настройте маршрутизатор.
a.	Подключитесь к маршрутизатору с помощью консоли и активируйте привилегированный режим EXEC.

b.	Войдите в режим конфигурации.

c.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

d.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

e.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

f.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

g.	Зашифруйте открытые пароли.

h.	Создайте баннер, который предупреждает о запрете несанкционированного доступа.

i.	Настройте и активируйте на маршрутизаторе интерфейс G0/0/1, используя информацию, приведенную в таблице адресации.

j.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

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
R1(config-line)#line vty 0 4
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#line vty 5 15
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#service password-encryption 
R1(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Prohibiting unauthorized access to the device!!!! #
R1(config)#interface g0/0/1
R1(config-if)#ip address 192.168.1.1 255.255.255.0
R1(config-if)#no shutdown 
R1(config-if)#exit
R1(config)#exit
R1#wr
Building configuration...
[OK]
R1#
```
#### Шаг 4. Настройте компьютер PC-A.
a.	Настройте для PC-A IP-адрес и маску подсети.

<img width="523" height="189" alt="image" src="https://github.com/user-attachments/assets/e4e3c57a-2e14-4064-967a-e72a2140ba6c" />

b.	Настройте для PC-A шлюз по умолчанию.

<img width="477" height="189" alt="image" src="https://github.com/user-attachments/assets/e5f981ac-2c4f-449b-bb1d-1ec43d585eb4" />

#### Шаг 5. Проверьте подключение к сети.
Пошлите с PC-A команду Ping на маршрутизатор R1. Если эхо-запрос с помощью команды ping не проходит, найдите и устраните неполадки подключения.
```
Cisco Packet Tracer PC Command Line 1.0
C:\>ping 192.168.1.1

Pinging 192.168.1.1 with 32 bytes of data:

Reply from 192.168.1.1: bytes=32 time<1ms TTL=255
Reply from 192.168.1.1: bytes=32 time=1ms TTL=255
Reply from 192.168.1.1: bytes=32 time=1ms TTL=255
Reply from 192.168.1.1: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.1.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 1ms, Average = 0ms

C:\>
```
Связь с маршрутизатором установлена.

### Часть 2. Настройка маршрутизатора для доступа по протоколу SSH
Подключение к сетевым устройствам по протоколу Telnet сопряжено с риском для безопасности, поскольку вся информация передается в виде открытого текста. Протокол SSH шифрует данные сеанса и обеспечивает аутентификацию устройств, поэтому для удаленных подключений рекомендуется использовать именно этот протокол. В части 2 вам нужно настроить маршрутизатор для приема соединений SSH по линиям VTY.
#### Шаг 1. Настройте аутентификацию устройств.
При генерации ключа шифрования в качестве его части используются имя устройства и домен. Поэтому эти имена необходимо указать перед вводом команды crypto key.
Откройте окно конфигурации
a.	Задайте имя устройства.
Имя устройства мы задали при начальной настройке маршрутизатора (Часть 1, Шаг 3. Настройте маршрутизатор.)
```
Router>enable
Router#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#hostname R1
```
b.	Задайте домен для устройства.
```
R1>enable
Password: 
R1#config t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ip domain-name confil.ru
R1(config)#
```
#### Шаг 2. Создайте ключ шифрования с указанием его длины.
```
R1>enable
Password: 
R1#config t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ip domain-name confil.ru
R1(config)#
R1(config)#crypto key generate rsa
The name for the keys will be: R1.confil.ru
Choose the size of the key modulus in the range of 360 to 2048 for your
  General Purpose Keys. Choosing a key modulus greater than 512 may take
  a few minutes.

How many bits in the modulus [512]: 2048
% Generating 2048 bit RSA keys, keys will be non-exportable...[OK]
Переключаем ssh на версию 2, так как она является самой безопасной.
R1(config)#ip ssh version 2
*Mar 1 2:9:5.758: %SSH-5-ENABLED: SSH 1.99 has been enabled
R1(config)#do show ip ssh 
SSH Enabled - version 2.0
Authentication timeout: 120 secs; Authentication retries: 3
R1(config)#
```
Длина ключа шифрования -  2048 бит.

#### Шаг 3. Создайте имя пользователя в локальной базе учетных записей.
Настройте имя пользователя, используя admin в качестве имени пользователя и Adm1nP @55 в качестве пароля.
```
R1(config)#username admin privilege 15 secret Adm1nP@55
```
#### Шаг 4. Активируйте протокол SSH на линиях VTY.
a.	Активируйте протоколы Telnet и SSH на входящих линиях VTY с помощью команды transport input.
```
R1(config)#line vty 0 4
R1(config-line)#transport input ?
  all     All protocols
  none    No protocols
  ssh     TCP/IP SSH protocol
  telnet  TCP/IP Telnet protocol
R1(config-line)#transport input all
```
b.	Измените способ входа в систему таким образом, чтобы использовалась проверка пользователей по локальной базе учетных записей.
```
R1(config-line)#login local
```
#### Шаг 5. Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
R1(config-line)#exit
R1(config)#exit
R1#
R1#wr
Building configuration...
[OK]
R1#
```
#### Шаг 6. Установите соединение с маршрутизатором по протоколу SSH.
a.	Запустите Tera Term с PC-A.

<img width="660" height="234" alt="image" src="https://github.com/user-attachments/assets/cd4c2ebb-a803-49ed-99ac-70492fa2a0bc" />

b.	Установите SSH-подключение к R1. Use the username admin and password Adm1nP@55. У вас должно получиться установить SSH-подключение к R1.

<img width="687" height="278" alt="image" src="https://github.com/user-attachments/assets/c1c3bf7f-239f-48fb-9618-b27b321df281" />

Подключились сразу в привилегированный режим роутера R1.

### Часть 3. Настройка коммутатора для доступа по протоколу SSH
#### Шаг 1. Настройте основные параметры коммутатора.

Откройте окно конфигурации

a.	Подключитесь к коммутатору с помощью консольного подключения и активируйте привилегированный режим EXEC.

b.	Войдите в режим конфигурации.

c.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

d.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

e.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

f.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

g.	Зашифруйте открытые пароли.

h.	Создайте баннер, который предупреждает о запрете несанкционированного доступа.

i.	Настройте и активируйте на коммутаторе интерфейс VLAN 1, используя информацию, приведенную в таблице адресации.

j.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```
Switch>
Switch>enable
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S1
S1(config)#no ip domain-lookup 
S1(config)#enable secret class
S1(config-line)#line con 0
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#line vty 0 4
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#line vty 5 15
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#service password-encryption
S1(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Prohibiting unauthorized access to the device!!!! #
S1(config)#interface vlan 1
S1(config-if)#
S1(config-if)#ip address 192.168.1.11 255.255.255.0
S1(config-if)#ip default-gateway 192.168.1.1
S1(config)#exit
S1#
%SYS-5-CONFIG_I: Configured from console by console
wr
Building configuration...
[OK]
S1#
```

#### Шаг 2. Настройте коммутатор для соединения по протоколу SSH.
Для настройки протокола SSH на коммутаторе используйте те же команды, которые применялись для аналогичной настройки маршрутизатора в части 2.

a.	Настройте имя устройства, как указано в таблице адресации.
```
Switch>
Switch>enable
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S1
```
b.	Задайте домен для устройства.
```
S1#
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#ip domain-name confil.ru
```
c.	Создайте ключ шифрования с указанием его длины.
```
S1(config)#crypto key generate rsa
The name for the keys will be: S1.confil.ru
Choose the size of the key modulus in the range of 360 to 2048 for your
  General Purpose Keys. Choosing a key modulus greater than 512 may take
  a few minutes.

How many bits in the modulus [512]: 1024
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]
S1(config)#ip ssh version 2
```
Длина ключа - 1024.
Сразу переходим на ssh второй версии, как самый безопасный.

d.	Создайте имя пользователя в локальной базе учетных записей.
```
S1(config)#username admin privilege 15 password Adm1nP@55
```
e.	Активируйте протоколы Telnet и SSH на линиях VTY.
```
S1(config)#line vty 0 4
S1(config-line)#transport input all
```
f.	Измените способ входа в систему таким образом, чтобы использовалась проверка пользователей по локальной базе учетных записей.
```
S1(config-line)#login local
S1(config-line)#exit
S1(config)#exit
S1#
%SYS-5-CONFIG_I: Configured from console by console
wr
Building configuration...
[OK]
S1#
```
#### Шаг 3. Установите соединение с коммутатором по протоколу SSH.
Запустите программу Tera Term на PC-A, затем установите подключение по протоколу SSH к интерфейсу SVI коммутатора S1.
<img width="658" height="232" alt="image" src="https://github.com/user-attachments/assets/e621455d-b164-43ab-9c14-b706073b300a" />

Вопрос:
Удалось ли вам установить SSH-соединение с коммутатором?

Соединение установить не удалось, так как на коммутаторе vlan 1 по умолчанию отключен.
Идем на коммутатор и включаем vlan 1.
```
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#interface vlan 1
S1(config-if)#no shutdown
S1(config-if)#exit
S1(config)#exit
S1#
S1#wr
Building configuration...
[OK]
S1#
```
Проверяем с компьютера доступность коммутатора:
```
C:\>ping 192.168.1.11

Pinging 192.168.1.11 with 32 bytes of data:

Request timed out.
Reply from 192.168.1.11: bytes=32 time<1ms TTL=255
Reply from 192.168.1.11: bytes=32 time<1ms TTL=255
Reply from 192.168.1.11: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.1.11:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
C:\>
```
Доступ получен.
Запускаем терминальный клиент заново.
<img width="666" height="257" alt="image" src="https://github.com/user-attachments/assets/7047331a-5b14-4f9f-bc6a-13df59b392a9" />

Подключились сразу в привилегированный режим коммутатора S1.

### Часть 4. Настройка протокола SSH с использованием интерфейса командной строки (CLI) коммутатора
Клиент SSH встроен в операционную систему Cisco IOS и может запускаться из интерфейса командной строки. В части 4 вам предстоит установить соединение с маршрутизатором по протоколу SSH, используя интерфейс командной строки коммутатора.

#### Шаг 1. Посмотрите доступные параметры для клиента SSH в Cisco IOS.
Откройте окно конфигурации
Используйте вопросительный знак (?), чтобы отобразить варианты параметров для команды ssh.
```
Prohibiting unauthorized access to the device!!!! 
User Access Verification
Password: 
S1>enable
Password: 
S1#ssh ?
  -l  Log in using this user name
  -v  Specify SSH Protocol Version
S1#ssh 
```
#### Шаг 2. Установите с коммутатора S1 соединение с маршрутизатором R1 по протоколу SSH.
a.	Чтобы подключиться к маршрутизатору R1 по протоколу SSH, введите команду –l admin. Это позволит вам войти в систему под именем admin. При появлении приглашения введите в качестве пароля Adm1nP@55
```
S1#ssh -l admin 192.168.1.1
Password: 
Prohibiting unauthorized access to the device!!!! 
R1#
```
Загрузился сразу привилегированный режим роутера.

b.	Чтобы вернуться к коммутатору S1, не закрывая сеанс SSH с маршрутизатором R1, нажмите комбинацию клавиш Ctrl+Shift+6. Отпустите клавиши Ctrl+Shift+6 и нажмите x. Отображается приглашение привилегированного режима EXEC коммутатора.
```
R1#
S1#
```
c.	Чтобы вернуться к сеансу SSH на R1, нажмите клавишу Enter в пустой строке интерфейса командной строки. Чтобы увидеть окно командной строки маршрутизатора, нажмите клавишу Enter еще раз.
```
[Resuming connection 1 to 192.168.1.1 ... ]
R1#
```
d.	Чтобы завершить сеанс SSH на маршрутизаторе R1, введите в командной строке маршрутизатора команду exit.
```
R1#exit
[Connection to 192.168.1.1 closed by foreign host]
S1#
```
Вопрос:
Какие версии протокола SSH поддерживаются при использовании интерфейса командной строки?
```
S1#ssh -v ?
  1  Protocol Version 1
  2  Protocol Version 2
 ```
Поддерживаются 1 и 2 версии протокола ssh.

#### Вопрос для повторения
Как предоставить доступ к сетевому устройству нескольким пользователям, у каждого из которых есть собственное имя пользователя?
Необходимо в локальную базу пользователей устройства добавить пользователей спомощью команды:
 ```
username 'NAME' password 'PASSWORD'
 ```
либо с указанием привилегий (при заведении админа например)
 ```
username 'NAME' privilege 15 password 'PASSWORD'
 ```
