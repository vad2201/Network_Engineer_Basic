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
Switch>
Switch>enable
Switch#show run
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

- Интерфейсов FastEthernet имеется 24.
- Интерфейсов Gigabit Ethernet имеется 2.
- Диапазоны значений, отображаемых в vty-линиях:
```
line con 0
!
line vty 0 4
 login
line vty 5 15
 login
```
При попытке изучить файл загрузочной конфигурации (startup configuration), который содержится в энергонезависимом ОЗУ (NVRAM)
```
Switch#show star
Switch#show startup-config 
startup-config is not present
Switch#
```
Данная надпись (startup-config is not present) появляется, так как у нас не сохранена конфигурация по умолчанию.
