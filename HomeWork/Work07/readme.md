# Лабораторная работа. Развертывание коммутируемой сети с резервными каналами
# Топология
 
### Таблица адресации


|Устройство|	Интерфейс|	IP-адрес |	Маска подсети |
|-------|-------|-------|-------|
|S1	|VLAN 1|	192.168.1.1|	255.255.255.0|
|S2	|VLAN 1|	192.168.1.2|	255.255.255.0|
|S3	|VLAN 1|	192.168.1.3|	255.255.255.0|

### Цели
#### Часть 1. Создание сети и настройка основных параметров устройства
#### Часть 2. Выбор корневого моста
#### Часть 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов
#### Часть 4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов
	
### Часть 1:	Создание сети и настройка основных параметров устройства

#### Шаг 1:	Создайте сеть согласно топологии.
<img width="509" height="211" alt="image" src="https://github.com/user-attachments/assets/95d70496-45fd-4a36-8f35-f8b85034a9fe" />

#### Шаг 2:	Выполните инициализацию и перезагрузку коммутаторов.
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
Initializing Hardware ...
```
Выполняем процедуру для каждого коммутатора.
#### Шаг 3:	Настройте базовые параметры каждого коммутатора.
a.	Отключите поиск DNS.

b.	Присвойте имена устройствам в соответствии с топологией.

c.	Назначьте class в качестве зашифрованного пароля доступа к привилегированному режиму.

d.	Назначьте cisco в качестве паролей консоли и VTY и активируйте вход для консоли и VTY каналов.

e.	Настройте logging synchronous для консольного канала.

f.	Настройте баннерное сообщение дня (MOTD) для предупреждения пользователей о запрете несанкционированного доступа.

g.	Задайте IP-адрес, указанный в таблице адресации для VLAN 1 на всех коммутаторах.

h.	Скопируйте текущую конфигурацию в файл загрузочной конфигурации.

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
S1(config-line)#logging synchronous
S1(config-line)#exit
S1(config)#line vty 0 15
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#service password-encryption
S1(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Prohibiting unauthorized access to the device!!!! #
S1(config)#vlan 1
S1(config-vlan)#exit
S1(config)#interface vlan 1
S1(config-if)#ip address 192.168.1.1 255.255.255.0
S1(config-if)#no shutdown
S1(config-if)#end
S1#copy running-config startup-config
Destination filename [startup-config]? 
%LINK-5-CHANGED: Interface Vlan1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up

%SYS-5-CONFIG_I: Configured from console by console

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
S2(config-line)#logging synchronous
S2(config-line)#exit
S2(config)#line vty 0 15
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#service password-encryption
S2(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Prohibiting unauthorized access to the device!!!! #
S2(config)#vlan 1
S2(config-vlan)#exit
S2(config)#interface vlan 1
S2(config-if)#ip address 192.168.1.2 255.255.255.0
S2(config-if)#no shutdown
S2(config-if)#end
S2#copy running-config startup-config
Destination filename [startup-config]? 
%LINK-5-CHANGED: Interface Vlan1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up

%SYS-5-CONFIG_I: Configured from console by console

Destination filename [startup-config]? 
Building configuration...
[OK]
S2#
```
Коммутатор S3
```
Switch>enable
Switch#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S3
S3(config)#no ip domain-lookup
S3(config)#enable secret class
S3(config)#line con 0
S3(config-line)#password cisco
S3(config-line)#login
S3(config-line)#logging synchronous
S3(config-line)#exit
S3(config)#line vty 0 15
S3(config-line)#password cisco
S3(config-line)#login
S3(config-line)#exit
S3(config)#service password-encryption
S3(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Prohibiting unauthorized access to the device!!!! #
S3(config)#vlan 1
S3(config-vlan)#exit
S3(config)#interface vlan 1
S3(config-if)#ip address 192.168.1.3 255.255.255.0
S3(config-if)#no shutdown
S3(config-if)#end
S3#copy running-config startup-config
Destination filename [startup-config]? 
%LINK-5-CHANGED: Interface Vlan1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up

%SYS-5-CONFIG_I: Configured from console by console

Destination filename [startup-config]? 
Building configuration...
[OK]
S3#
```
#### Шаг 4:	Проверьте связь.
Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S2?	

<img width="609" height="207" alt="image" src="https://github.com/user-attachments/assets/8a448e52-2af3-48bf-ae7e-d54d5a3ca697" />

Связь есть.

Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S3?

<img width="604" height="167" alt="image" src="https://github.com/user-attachments/assets/6b0532d2-8433-46e7-9a56-b65ba6807ebd" />

Связь есть.

Успешно ли выполняется эхо-запрос от коммутатора S2 на коммутатор S3?

<img width="602" height="197" alt="image" src="https://github.com/user-attachments/assets/2bfac67d-4293-4771-b485-04fba577490e" />

Связь есть.

### Часть 2:	Определение корневого моста
Для каждого экземпляра протокола spanning-tree (коммутируемая сеть LAN или широковещательный домен) существует коммутатор, выделенный в качестве корневого моста. Корневой мост служит точкой привязки для всех расчётов протокола spanning-tree, позволяя определить избыточные пути, которые следует заблокировать.
Процесс выбора определяет, какой из коммутаторов станет корневым мостом. Коммутатор с наименьшим значением идентификатора моста (BID) становится корневым мостом. Идентификатор BID состоит из значения приоритета моста, расширенного идентификатора системы и MAC-адреса коммутатора. Значение приоритета может находиться в диапазоне от 0 до 65535 с шагом 4096. По умолчанию используется значение 32768.
#### Шаг 1:	Отключите все порты на коммутаторах.
Коммутатор S1
```
S1(config)#inter range f0/1-24
S1(config-if-range)#shutdown
S1(config-if-range)#inter range g0/1-2
S1(config-if-range)#shutdown
S1(config-if-range)#end
S1#
```
Коммутатор S2 
```
S2(config)#interface range f0/1-24
S2(config-if-range)#shutdown
S2(config-if-range)#interface range g0/1-2
S2(config-if-range)#shutdown
S2(config-if-range)#end
S2#
```
Коммутатор S3
```
S3(config)#interface range f0/1-24
S3(config-if-range)#shutdown
S3(config-if-range)#interface range g0/1-2
S3(config-if-range)#shutdown
S3(config-if-range)#end
S3#
```
#### Шаг 2:	Настройте подключенные порты в качестве транковых.
Коммутатор S1 
```
S1(config)#vlan 1000
S1(config-vlan)#name NATIVE
S1(config-vlan)#exit
S1(config)#interface range f0/2, f0/4
S1(config-if-range)#switchport mode trunk
S1(config-if-range)#switchport trunk allowed vlan 1,1000
S1(config-if-range)#switchport trunk native vlan 1000
S1(config-if-range)#exit
```
Коммутатор S2 
```
S2(config)#vlan 1000
S2(config-vlan)#name NATIVE
S2(config-vlan)#exit
S2(config)#interface range f0/2, f0/4
S2(config-if-range)#switchport mode trunk
S2(config-if-range)#switchport trunk allowed vlan 1,1000
S2(config-if-range)#switchport trunk native vlan 1000
S2(config-if-range)#exit
```
Коммутатор S3 
```
S3(config)#vlan 1000
S3(config-vlan)#name NATIVE
S3(config-vlan)#exit
S3(config)#interface range f0/2, f0/4
S3(config-if-range)#switchport mode trunk
S3(config-if-range)#switchport trunk allowed vlan 1,1000
S3(config-if-range)#switchport trunk native vlan 1000
S3(config-if-range)#exit
```
#### Шаг 3:	Включите порты F0/2 и F0/4 на всех коммутаторах.
Коммутаор S1 
```
S1(config)#interface range f0/2, f0/4
S1(config-if-range)#no shutdown
```
Коммутаор S2
```
S2(config)#interface range f0/2, f0/4
S2(config-if-range)#no shutdown
```
Коммутаор S3 
```
S3(config)#interface range f0/2, f0/4
S3(config-if-range)#no shutdown
```
#### Шаг 4:	Отобразите данные протокола spanning-tree.
Введите команду show spanning-tree на всех трех коммутаторах. Приоритет идентификатора моста рассчитывается путем сложения значений приоритета и расширенного идентификатора системы. Расширенным идентификатором системы всегда является номер сети VLAN. В примере ниже все три коммутатора имеют равные значения приоритета идентификатора моста (32769 = 32768 + 1, где приоритет по умолчанию = 32768, номер сети VLAN = 1).

Коммутатор S1 

<img width="794" height="349" alt="image" src="https://github.com/user-attachments/assets/b1a08782-0158-462a-9a3f-9638869550fb" />

Коммутатор S2

<img width="793" height="371" alt="image" src="https://github.com/user-attachments/assets/3c607dd7-4af3-4881-873e-0bd70c53d334" />

Коммутатор S3 

<img width="791" height="386" alt="image" src="https://github.com/user-attachments/assets/d197fa4f-b62c-4972-8aae-5cebe23c77ba" />

Mac адреса коммутаторов:

S1 - 0003.E4C7.6105

S2 - 000A.41B5.D217

S3 - 0006.2A36.6A56

Коммутатор с самым низким значением MAC-адреса становится корневым мостом — S1.

Примечание. Режим STP по умолчанию на коммутаторе 2960 — протокол STP для каждой сети VLAN (PVST).

Мы подключили быстрый режим rapid-pvst.

##### В схему ниже запишите роль и состояние (Sts) активных портов на каждом коммутаторе в топологии.
 
С учетом выходных данных, поступающих с коммутаторов, ответьте на следующие вопросы.

##### Какой коммутатор является корневым мостом? 

S1 

##### Почему этот коммутатор был выбран протоколом spanning-tree в качестве корневого моста?

Он выбран потому что у него самый маленький приоритет идентификатора моста (BID), определен по самому маленькому mac адресу.

##### Какие порты на коммутаторе являются корневыми портами?  

Порт на коммутаторе с наименьшей стоимостью для достижения корневого моста.

S2, порт f0/2

S3, порт f0/4

##### Какие порты на коммутаторе являются назначенными портами? 

Назначенный порт, это порт, который выбирается для каждого сегмента (каждого канала), исходя из стоимости возврата к корневому мосту для любой стороны канала.

S1, порт f0/2, f0/4

S3, порт f0/2

Какой порт отображается в качестве альтернативного и в настоящее время заблокирован? 

S2, порт f0/4

##### Почему протокол spanning-tree выбрал этот порт в качестве невыделенного (заблокированного) порта?
Альтернативный порт — резервный порт (только RSTP) для назначенного порта, если другая сторона не является корневым портом.
Выбирается по принципу максимальной стоимости идентификатора моста. Так как идентификатор в данной схеме различается по mac адресу,

S2 - 000A.41B5.D217

S3 - 0006.2A36.6A56

соответственно альтернативным становится порт со стороны S2 (у него больше mac адрес)

### Часть 3:	Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов
Алгоритм протокола spanning-tree (STA) использует корневой мост как точку привязки, после чего определяет, какие порты будут заблокированы, исходя из стоимости пути. Порт с более низкой стоимостью пути является предпочтительным. Если стоимости портов равны, процесс сравнивает BID. Если BID равны, для определения корневого моста используются приоритеты портов. Наиболее низкие значения являются предпочтительными. В части 3 вам предстоит изменить стоимость порта, чтобы определить, какой порт будет заблокирован протоколом spanning-tree.
#### Шаг 1:	Определите коммутатор с заблокированным портом.
При текущей конфигурации только один коммутатор может содержать заблокированный протоколом STP порт. Выполните команду show spanning-tree на обоих коммутаторах некорневого моста.

Коммутатор S2

<img width="793" height="371" alt="image" src="https://github.com/user-attachments/assets/3c607dd7-4af3-4881-873e-0bd70c53d334" />

Коммутатор S3 

<img width="791" height="386" alt="image" src="https://github.com/user-attachments/assets/d197fa4f-b62c-4972-8aae-5cebe23c77ba" />

Коммутатор S2 - коммутатор с заблокированным портом f0/4.

#### Шаг 2:	Измените стоимость порта.
Помимо заблокированного порта, единственным активным портом на этом коммутаторе является порт, выделенный в качестве порта корневого моста. Уменьшите стоимость этого порта корневого моста до 18, выполнив команду spanning-tree vlan 1 cost 18 режима конфигурации интерфейса.
```
S2(config)#inter f0/2
S2(config-if)#spanning-tree vlan 1 cost 18
```
#### Шаг 3:	Просмотрите изменения протокола spanning-tree.
После изменения стоимости порта на коммутаторе S2, ничего не произошло. Пришлось отключить порты на коммутаторах и потом включить, чтобы прошли перевыборы.

Коммутатор S2

<img width="839" height="367" alt="image" src="https://github.com/user-attachments/assets/17ba5115-12a2-46e2-a57c-17bc0d1e0799" />

Коммутатор S3

<img width="807" height="371" alt="image" src="https://github.com/user-attachments/assets/32a2c8b5-b39f-4c99-af07-363c63c046b9" />

Ранее заблокированный порт (S2 – F0/4) теперь является назначенным портом, и протокол spanning-tree теперь блокирует порт на другом коммутаторе некорневого моста S3 (F0/2).

##### Почему протокол spanning-tree заменяет ранее заблокированный порт на назначенный порт и блокирует порт, который был назначенным портом на другом коммутаторе?
Алгоритм протокола spanning-tree (STA) использует корневой мост как точку привязки, после чего определяет, какие порты будут заблокированы, исходя из стоимости пути. 
Так как мы уменьшили стоимость корневого порта S2 по сравнению с S3,  то альтернативный порт будет на коммутаторе S3 (порт f0/2, порт f0/4 является корневым).

#### Шаг 4:	Удалите изменения стоимости порта.
a.	Выполните команду no spanning-tree vlan 1 cost 18 режима конфигурации интерфейса, чтобы удалить запись стоимости, созданную ранее.
```
S2(config)#inter f0/2
S2(config-if)#no spanning-tree vlan 1 cost 18
```
b.	Повторно выполните команду show spanning-tree, чтобы подтвердить, что протокол STP сбросил порт на коммутаторе некорневого моста, вернув исходные настройки порта. 

Коммутатор S2 

<img width="791" height="365" alt="image" src="https://github.com/user-attachments/assets/38e264dd-905a-4412-899c-9c509522031c" />

Коммутатор S3 

<img width="789" height="398" alt="image" src="https://github.com/user-attachments/assets/c75f936d-0ca7-4aab-974d-442b3f20ef78" />

Протоколу STP требуется примерно 30 секунд, чтобы завершить процесс перевода порта.

Правило 30 секунд не сработало. Пришлось отключать порты на коммутаторах и заново включать.

### Часть 4:	Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов
Если стоимости портов равны, процесс сравнивает BID. Если BID равны, для определения корневого моста используются приоритеты портов. Значение приоритета по умолчанию — 128. STP объединяет приоритет порта с номером порта, чтобы разорвать связи. Наиболее низкие значения являются предпочтительными. В части 4 вам предстоит активировать избыточные пути до каждого из коммутаторов, чтобы просмотреть, каким образом протокол STP выбирает порт с учетом приоритета портов.
a.	Включите порты F0/1 и F0/3 на всех коммутаторах.
```
S2(config)#interface range f0/1-4
S2(config-if-range)#shutdown
-----------------------------------
S3(config)#interface range f0/1-4
S3(config-if-range)#shutdown
```
Потом включаем эти порты, почему-то не отрабатывает правило 30 секунд.
```
S2(config)#interface range f0/1-4
S2(config-if-range)#shutdown
-----------------------------------
S3(config-if-range)#interface range f0/1-4
S3(config-if-range)#no shutdown
```

b.	Подождите 30 секунд, чтобы протокол STP завершил процесс перевода порта, после чего выполните команду show spanning-tree на коммутаторах некорневого моста. Обратите внимание, что порт корневого моста переместился на порт с меньшим номером, связанный с коммутатором корневого моста, и заблокировал предыдущий порт корневого моста.

Коммутатор S2

<img width="792" height="411" alt="image" src="https://github.com/user-attachments/assets/ad9f699d-2b42-472a-a2f9-f78077aea676" />

Коммутатор S3

<img width="798" height="434" alt="image" src="https://github.com/user-attachments/assets/358177a0-4cca-4e7f-b0c3-e47857dfbc99" />

##### Какой порт выбран протоколом STP в качестве порта корневого моста на каждом коммутаторе некорневого моста?
S2 - f0/1

S3 - f0/3

##### Почему протокол STP выбрал эти порты в качестве портов корневого моста на этих коммутаторах?
Порт на коммутаторе с наименьшей стоимостью для достижения корневого моста. Стоимость одинаковая, играет роль номер порта - у кого он меньше, тот и выиграл.

#### 	Вопросы для повторения
##### 1.	Какое значение протокол STP использует первым после выбора корневого моста, чтобы определить выбор порта?
Стоимость маршрута. 
##### 2.	Если первое значение на двух портах одинаково, какое следующее значение будет использовать протокол STP при выборе порта?
Номер порта - у кого он меньше, тот и выиграл.
##### 3.	Если оба значения на двух портах равны, каким будет следующее значение, которое использует протокол STP при выборе порта?
Идентификатор свича (Bridge ID).

