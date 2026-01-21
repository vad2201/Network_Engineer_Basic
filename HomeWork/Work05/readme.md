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

Примечание:
Так как в Cisco Packet Tracer нет Tera Term с PC-A, подключаться по ssh будем через командную строку.

b.	Установите SSH-подключение к R1. Use the username admin and password Adm1nP@55. У вас должно получиться установить SSH-подключение к R1.
```
C:\>ssh -l admin 192.168.1.1

Password: 


Prohibiting unauthorized access to the device!!!! 

R1#
```
Подключились сразу в привилегированный режим роутера R1.
