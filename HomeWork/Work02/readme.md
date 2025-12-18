# Лабораторная работа. Просмотр таблицы MAC-адресов коммутатора 
### Топология

<img width="501" height="295" alt="image" src="https://github.com/user-attachments/assets/4f90e2dd-43ac-4cf6-aef2-ed284598dd5d" />

### Таблица адресации

|    Устройство    |      Интерфейс     |      IP-адрес       |    Маска подсети    | 
|------------------|--------------------|---------------------|---------------------|
| S1               | VLAN 1             | 192.168.1.11        |    255.255.255.0    |
| S2               | VLAN 1             | 192.168.1.12        |    255.255.255.0    |
| PC-A             | NIC                | 192.168.1.1         |    255.255.255.0    |
| PC-B             | NIC                | 192.168.1.2         |    255.255.255.0    | 
###  	Цели
###  	Часть 1. Создание и настройка сети
###  	Часть 2. Изучение таблицы МАС-адресов коммутатора

###  Решение:

###  Часть 1. Создание и настройка сети

####  Шаг 1. Подключите сеть в соответствии с топологией.
<img width="374" height="215" alt="image" src="https://github.com/user-attachments/assets/6099d1c7-9eca-4efb-aa5d-a7d72fb58e19" />

####  Шаг 2. Настройте узлы ПК.
Настройки производим в конфигурации компьютеров.
<img width="629" height="551" alt="image" src="https://github.com/user-attachments/assets/1e0496ba-ad82-49af-83e9-b9b4388821c5" />

####  Шаг 3. Выполните инициализацию и перезагрузку коммутаторов.
Для инициализации коммутатора заходим в привилегированный режим и сбрасываем его настройки. После этого перезагружаем коммутатор.
```
Switch>enable
Switch#erase startup-config
Erasing the nvram filesystem will remove all configuration files! Continue? [confirm]y[OK]
Erase of nvram: complete
%SYS-7-NV_BLOCK_INIT: Initialized the geometry of nvram
Switch#reload
```
Это процедуру повторяем и для второго коммутатора
