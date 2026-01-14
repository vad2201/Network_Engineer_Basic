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
Настраеваем роутер
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
