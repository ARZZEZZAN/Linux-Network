# Сети в Linux

Настройка сетей в Linux на виртуальных машинах.


## Оглавление

   1. [Инструмент ipcalc](#part-1-инструмент-ipcalc)
   2. [Статическая маршрутизация между двумя машинами](#part-2-статическая-маршрутизация-между-двумя-машинами)
   3. [Утилита iperf3](#part-3-утилита-iperf3)
   4. [Сетевой экран](#part-4-сетевой-экран)
   5. [Статическая маршрутизация сети](#part-5-статическая-маршрутизация-сети)
   6. [Динамическая настройка IP с помощью DHCP](#part-6-динамическая-настройка-ip-с-помощью-dhcp)
   7. [NAT](#part-7-nat)


## Отчет

## Part 1. Инструмент **ipcalc**

#### 1.1. Сети и маски
 1) ![192.167.38.54/13](part-1/1.1/1/1.2.png)    
    `Адрес сети *192.167.38.54\13* - *192.160.0.0*`

 2) ![192.167.38.54/24](part-1/1.1/1/1.1.png)
  `Перевод маски *255.255.255.0* в префискную и двоичную запись`
	- CIDR: /24
	- Mask: 255.255.255.0
	- Binary mask: 11111111.11111111.11111111.00000000
    
    ![192.167.38.54/15](part-1/1.1/2/1.1.png)
    `Перевод маски */15* в обычную и двоичную запись`
    - CIDR: /15
    - Mask: 255.254.0.0
    - Binary mask: 11111111.11111110.00000000.00000000
    
    ![192.167.38.54/28](part-1/1.1/2/1.2.png)
    `Переводмаски *11111111.11111111.11111111.11110000* в обычную и двоичную запись`
	- CIDR: /28
	- Mask: 255.255.255.240
	- Binary mask: 11111111.11111111.11111111.11110000

 3) ![12.167.38.4/8](part-1/1.1/3/1.1.png)    
	` MIN host: *12.0.0.1* MAX host: *12.255.255.254*`

    ![12.167.38.4/16](part-1/1.1/3/1.2.png)    
	` MIN host: *12.167.0.1* MAX host: *12.167.255.254*`

    ![12.167.38.4/23](part-1/1.1/3/1.3.png)    
	` MIN host: *12.167.38.1* MAX host: *12.167.39.254*`

    ![12.167.38.4/4](part-1/1.1/3/1.4.png)   
	` MIN host: *0.0.0.1* MAX host: *15.255.255.254*`

#### 1.2. localhost

Можно обратиться к приложению с IP: *127.0.0.2/24*, *127.1.0.1/8*

Нельзя обратиться к приложению с IP: *194.34.23.100*, *128.0.0.1*

#### 1.3. Диапазоны и сегменты сетей
##### 1) Какие из перечисленных IP можно использовать в качестве публичного, а какие только в качестве частных: *10.0.0.45/8*, *134.43.0.2/16*, *192.168.4.2/16*, *172.20.250.4/12*, *172.0.2.1/12*, *192.172.0.1/12*, *172.68.0.2/12*, *172.16.255.255/12*, *10.10.10.10/8*, *192.169.168.1/16*
	 частные:
	 10.0.0.45/8
	 192.168.4.2/16
	 172.20.250.4/12
	 172.16.255.255/12
	 10.10.10.10/8
	 
	 публичные:
	 134.43.0.2/16
	 172.0.2.1/12
	 192.172.0.1/12
	 172.68.0.2/12
	 192.169.168.1/16

##### 2) какие из перечисленных IP адресов шлюза возможны у сети *10.10.0.0/18*: *10.0.0.1*, *10.10.0.2*, *10.10.10.10*, *10.10.100.1*, *10.10.1.255*
	 10.10.0.2
	 10.10.10.10
	 10.10.1.255

## Part 2. Статическая маршрутизация между двумя машинами  

#### С помощью команды ip a посмотреть существующие сетевые интерфейсы  
* ws1  & ws2
![ip_a.jpg](part-2/2.0/ip_a1.png)

#### Описать сетевой интерфейс, соответствующий внутренней сети, на обеих машинах и задать следующие адреса и маски: ws1 - 192.168.100.10, маска /16, ws2 - 172.24.116.8, маска /12  
* На обеих машинах выполняем команду `sudo nano /etc/netplan/00-installer-config.yaml` и вносим изменения.  
* ws1  
![Part_2.0.4.jpg](part-2/2.0/yaml1.png)
* ws2  
![Part_2.0.4.jpg](part-2/2.0/yaml2.png)

#### Выполнить команду netplan apply для перезапуска сервиса сети  
* ws1  & ws2
![Part_2.0.6.jpg](part-2/2.0/netplan.png)

### 2.1. Добавление статического маршрута вручную  
#### Добавить статический маршрут от одной машины до другой и обратно при помощи команды вида ip r add  
* Команда для ws1: `sudo ip r add 172.24.116.8 via 192.168.100.10 dev enp0s8`  
![Part_2.0.8.jpg](part-2/2.1/ws1ipr.png)
* Команда для ws2: `sudo ip r add 192.168.100.10 via 172.24.116.8 dev enp0s8`  
![Part_2.0.9.jpg](part-2/2.1/ws2ipr.png) 

#### Пропинговать соединение между машинами  
* ws1  
![Part_2.0.10.jpg](part-2/2.1/ping1.png)   
* ws2  
![Part_2.0.11.jpg](part-2/2.1/ping2.png)  

### 2.2. Добавление статического маршрута с сохранением  

#### Добавить статический маршрут от одной машины до другой с помощью файла etc/netplan/00-installer-config.yaml  и пропинговать соединение между машинами  
* ws1  
![Part_2.2.1.jpg](part-2/2.2/stat&ping1.png)  
* ws2  
![Part_2.2.2.jpg](part-2/2.2/stat&ping2.png) 

## Part 3. Утилита iperf3  
### 3.1. Скорость соединения  
#### Перевести и записать в отчёт: 8 Mbps в MB/s, 100 MB/s в Kbps, 1 Gbps в Mbps  
* 8 Mbps = 1 MB/s  
* 100 MB/s = 819200 Kbps  
* 1 Gbps = 1024 Mbps  

### 3.2. Утилита iperf3  
#### Измерить скорость соединения между ws1 и ws2  
* ws1 выступает в роли сервера. Запуск iperf3 сервер, команда: `iperf3 -s -f m`  
* ws2 выступает в роли клиента. Запуск iperf3 клиент, команда: `iperf3 -c 192.168.100.10`  
![Part_3.2.2.jpg](part-3/speed.png) 

## Part 4. Сетевой экран  
### 4.1. Утилита iptables  
#### Создать файл /etc/firewall.sh, имитирующий фаерволл, на ws1 и ws2:
    #!/bin/sh  

    # Deleting all the rules in the "filter" table (default).  
    iptables -F  
    iptables –X  

#### Нужно добавить в файл подряд следующие правила:  

#### 1) на ws1 применить стратегию когда в начале пишется запрещающее правило, а в конце пишется разрешающее правило (это касается пунктов 4 и 5)  

#### 2) на ws2 применить стратегию когда в начале пишется разрешающее правило, а в конце пишется запрещающее правило (это касается пунктов 4 и 5)  

#### 3) открыть на машинах доступ для порта 22 (ssh) и порта 80 (http)  

#### 4) запретить echo reply (машина не должна "пинговаться”, т.е. должна быть блокировка на OUTPUT)  

#### 5) разрешить echo reply (машина должна "пинговаться")  

* добавляем правила для ws1:    
* добавляем правила для ws2:  
 #### Запустить файлы на обеих машинах командами `chmod +x /etc/firewall.sh` и `/etc/firewall.sh`  
* запускаем файл на ws1, не забываем дописать `sudo`  
* запускаем файл на ws2 тоже с `sudo`   
![Part_4.1.1.jpg](part-4/ws12rules.png) 

* Разница между стратегиями заключается в том, что в первом файле первым подходящим правилом для пакета является запрет, а во втором - разрешение. Применяется только первое подходящее правило, остальные игнорируются.  

### 4.2. Утилита nmap  
#### Командой ping найти машину, которая не "пингуется", после чего утилитой nmap показать, что хост машины запущен  
_Проверка: в выводе nmap должно быть сказано: `Host is up`_  
* пингуем ws2 с ws1  
* пингуем ws1 с ws2 и видим, что машина не "пингуется". Сразу проверяем утилитой nmap.  
![Part_4.2.2.jpg](part-4/ws12ping.png)    

## Part 5. Статическая маршрутизация сети  
#### Поднять пять виртуальных машин (3 рабочие станции (ws11, ws21, ws22) и 2 роутера (r1, r2))  

### 5.1. Настройка адресов машин  
#### Настроить конфигурации машин в etc/netplan/00-installer-config.yaml согласно сети на рисунке.  
* ws11  
![Part_5.1.1.jpg](part-5/adr3.png)  


* r1  
![Part_5.1.1.jpg](part-5/adr4.png)  


* ws21  
![Part_5.1.1.jpg](part-5/adr1.png)  


* ws22  
![Part_5.1.1.jpg](part-5/adr2.png)  


* r2  
![Part_5.1.1.jpg](part-5/adr5.png)  

#### Перезапустить сервис сети. Если ошибок нет, то командой ip -4 a проверить, что адрес машины задан верно. Также пропинговать ws22 с ws21. Аналогично пропинговать r1 с ws11.  
* ws11 
![Part_5.1.6.jpg](part-5/ip5.png)  


* ws22  
![Part_5.1.7.jpg](part-5/ip4.png)  


* ws21  
![Part_5.1.8.jpg](part-5/ip3.png)  


* r1  
![Part_5.1.9.jpg](part-5/ip2.png)    


* r2  
![Part_5.1.10.jpg](part-5/ip1.png)  

### 5.2. Включение переадресации IP-адресов.  
#### Для включения переадресации IP, выполните команду на роутерах:  
`sysctl -w net.ipv4.ip_forward=1`  
* ввод на r1  
![Part_5.1.10.jpg](part-5/sys1.png)   

* ввод на r2  
![Part_5.1.10.jpg](part-5/sys2.png) 

#### Откройте файл /etc/sysctl.conf и добавьте в него следующую строку:  
`net.ipv4.ip_forward = 1`  
* изменяем файл для r1 
![Part_5.1.10.jpg](part-5/net1.png)
* изменяем файл для r2
![Part_5.1.10.jpg](part-5/net2.png)
### 5.3. Установка маршрута по-умолчанию  
#### Настроить маршрут по-умолчанию (шлюз) для рабочих станций. Для этого добавить default перед IP роутера в файле конфигураций  
* добавляем шлюз для ws11  
![Part_5.1.10.jpg](part-5/shl1.png)


* для ws21  
![Part_5.1.10.jpg](part-5/shl2.png)


* для ws22  
![Part_5.1.10.jpg](part-5/shl3.png)

* также потребовалось добавить шлюзы для роутеров, чтобы пинг доходил в соседнюю сеть.
* r1
![Part_5.1.10.jpg](part-5/shl4.png)
* r2
![Part_5.1.10.jpg](part-5/shl5.png)

#### Вызвать ip r и показать, что добавился маршрут в таблицу маршрутизации  
* ws11  
![Part_5.1.10.jpg](part-5/ipr1.png)


* ws21  
![Part_5.1.10.jpg](part-5/ipr2.png)


* ws22  
![Part_5.1.10.jpg](part-5/ipr3.png)


* роутеры r1
![Part_5.1.10.jpg](part-5/ipr5.png)


* роутеры r2
![Part_5.1.10.jpg](part-5/ipr4.png)

#### Пропинговать с ws11 роутер r2 и показать на r2, что пинг доходит. Для этого использовать команду:  
`tcpdump -tn -i eth1`  
Изменим eth1 на название нашего адаптера enp0s9 и пингуем:  
* ws11 ping
![Part_5.1.10.jpg](part-5/pingws.png)
* r2  
![Part_5.1.10.jpg](part-5/dump.png)

### 5.4. Добавление статических маршрутов  
#### Добавить в роутеры r1 и r2 статические маршруты в файле конфигураций. Пример для r1 маршрута в сетку 10.20.0.0/26:  
    # Добавить в конец описания сетевого интерфейса eth1:  
    - to: 10.20.0.0  
      via: 10.100.0.12  
* добавленные маршруты для роутеров r1 и r2  
![Part_5.4.1.jpg](part-5/stat.png)
![Part_5.4.1.jpg](part-5/stat1.png)

#### Вызвать ip r и показать таблицы с маршрутами на обоих роутерах.  
* r1
![Part_5.1.10.jpg](part-5/ipr5.png)


* r2
![Part_5.1.10.jpg](part-5/ipr4.png)

#### Запустить команды на ws11:  
`ip r list 10.10.0.0/[маска сети]` и `ip r list 0.0.0.0/0`  
* команда `ip r list 10.10.0.0/[маска сети]`  
![Part_5.1.10.jpg](part-5/ip18.png)


* команда `ip r list 0.0.0.0/0`  
![Part_5.1.10.jpg](part-5/ip0.png)
* Маршрут по умолчанию имеет более низкий приоритет и срабатывает, когда не найден подходящий маршрут в таблице маршрутизации. Для сети 10.10.0.0 мы создали правило, соответственно используется созданный маршрут. Также можно устанавливать метрику, чтобы менять приоритеты маршрутов.  

### 5.5. Построение списка маршрутизаторов  
#### Запустить на r1 команду дампа:  
`tcpdump -tnv -i eth0`  

#### При помощи утилиты traceroute построить список маршрутизаторов на пути от ws11 до ws21  
Прежде всего нужно выключить адаптер внутренней сети для получения интернет соединения, также выключить firewalls `sudo ufw disable` на всех машинах(желательно)
* вызов и вывод traceroute на ws11  
![Part_5.5.1.jpg](part-5/trac.png)


* вызов и вывод tcpdump -tnv -i enp0s9 на r1  
![Part_5.5.1.jpg](part-5/dump09.png)

* Принцип построения пути при помощи traceroute:  
Для определения промежуточных маршрутизаторов traceroute отправляет серию пакетов данных целевому узлу, при этом каждый раз увеличивая на 1 значение поля TTL («время жизни»). Это поле обычно указывает максимальное количество маршрутизаторов, которое может быть пройдено пакетом. Первый пакет отправляется с TTL, равным 1, и поэтому первый же маршрутизатор возвращает обратно сообщение ICMP, указывающее на невозможность доставки данных. Traceroute фиксирует адрес маршрутизатора, а также время между отправкой пакета и получением ответа (эти сведения выводятся на монитор компьютера). Затем traceroute повторяет отправку пакета, но уже с TTL, равным 2, что позволяет первому маршрутизатору пропустить пакет дальше.  
Процесс повторяется до тех пор, пока при определённом значении TTL пакет не достигнет целевого узла. При получении ответа от этого узла процесс трассировки считается завершённым.  

### 5.6. Использование протокола ICMP при маршрутизации  
#### Запустить на r1 перехват сетевого трафика, проходящего через eth0 с помощью команды:  
`tcpdump -n -i eth0 icmp`  

#### Пропинговать с ws11 несуществующий IP (например, 10.30.0.111) с помощью команды:  
`ping -c 1 10.30.0.111`  

* tcpdump на r1  
![Part_5.5.1.jpg](part-5/dump08.png)


* ping на ws11  
![Part_5.5.1.jpg](part-5/unping.png)

## Part 6. Динамическая настройка IP с помощью DHCP  
#### Для r2 настроить в файле /etc/dhcp/dhcpd.conf конфигурацию службы DHCP:  
#### 1) указать адрес маршрутизатора по-умолчанию, DNS-сервер и адрес внутренней сети.  
* вносим изменения в файл _/etc/dhcp/dhcpd.conf_  
![Part_6.1.1.jpg](part-6/dhcpd1.png)

#### 2) в файле resolv.conf прописать nameserver 8.8.8.8.  
* вносим изменения в файл _/etc/resolv.conf_  
![Part_6.1.2.jpg](part-6/resolve1.png)

#### Перезагрузить службу DHCP командой systemctl restart isc-dhcp-server. Машину ws21 перезагрузить при помощи reboot и через ip a показать, что она получила адрес. Также пропинговать ws22 с ws21.  
* перезагружаем службу DHCP  
![Part_6.2.1.jpg](part-6/restart.png)


* Перезагружаем ws21 с помощью команды `sudo reboot` и вызываем команду `ip a`  
![Part_6.2.2.jpg](part-6/ipa2.png) 


* пингуем ws22 с ws21  
![Part_6.2.3.jpg](part-6/ws21.png)

#### Указать MAC адрес у ws11, для этого в etc/netplan/00-installer-config.yaml надо добавить строки: macaddress: 10:10:10:10:10:BA, dhcp4: true  
* вносим изменения в в _/etc/netplan/00-installer-config.yaml_  
![Part_6.3.jpg](part-6/mac.png)

#### Для r1 настроить аналогично r2, но сделать выдачу адресов с жесткой привязкой к MAC-адресу (ws11). Провести аналогичные тесты  
* Снова скачиваем isc-dhcp-server и вносим изменения в файл _/etc/dhcp/dhcpd.conf_  
![Part_6.4.1.jpg](part-6/dhcpd2.png)


* затем редактируем файл _/etc/resolv.conf_  
![Part_6.4.2.jpg](part-6/resolve2.png)


* перезагружаем службу DHCP  
![Part_6.4.3.jpg](part-6/restart1.png)  


* перезагружаем ws11 и вызываем `ip a`  
![Part_6.5.1.jpg](part-6/ws11ip.png)


* пингуем ws22  
![Part_6.5.2.jpg](part-6/ws22.png)

#### Запросить с ws21 обновление ip адреса  
* `ip a` на ws21 до обновления  
![Part_6.5.1.jpg](part-6/ipa2.png)


* вызываем команду `sudo dhclient enp0s8 -r`, потом `sudo dhclient enp0s8` и снова `ip a`  
![Part_6.5.2.jpg](part-6/ipa2.1.png)
* в данном пункте пользовался опцией -r для того, чтобы очистить список ip адресов.  

## Part 7. NAT  
#### В файле /etc/apache2/ports.conf на ws22 и r1 изменить строку Listen 80 на Listen 0.0.0.0:80, то есть сделать сервер Apache2 общедоступным  
* ws22  
![Part_7.1.1.jpg](part-7/ws22port.png)

* r1  
![Part_7.1.2.jpg](part-7/r1port.png)

#### Запустить веб-сервер Apache командой `service apache2 start` на ws22 и r1  
* ws22  
![Part_7.2.1.jpg](part-7/r1start.png) 


* r1  
![Part_7.2.2.jpg](part-7/ws22start.png)

#### Добавить в фаервол, созданный по аналогии с фаерволом из Части 4, на r2 следующие правила:  
1) Удаление правил в таблице filter - iptables -F
2) Удаление правил в таблице "NAT" - iptables -F -t nat
3) Отбрасывать все маршрутизируемые пакеты - iptables --policy FORWARD DROP  
![Part_7.3.jpg](part-7/iptable1.png) 

#### Запускать файл также, как в Части 4  
![Part_7.7.jpg](part-7/chmod.png)

#### Проверить соединение между ws22 и r1 командой ping  
![Part_7.5.jpg](part-7/ping.png)

#### Добавить в файл ещё одно правило:  
4) Разрешить маршрутизацию всех пакетов протокола ICMP  
![Part_7.6.jpg](part-7/iptable2.png) 


#### Проверить соединение между ws22 и r1 командой ping  
![Part_7.8.jpg](part-7/pingws22.png)  

#### Добавить в файл ещё два правила:  
5) Включить SNAT, а именно маскирование всех локальных ip из локальной сети, находящейся за r2 (по обозначениям из Части 5 - сеть 10.20.0.0)
6) Включить DNAT на 8080 порт машины r2 и добавить к веб-серверу Apache, запущенному на ws22, доступ извне сети  
![Part_7.9.jpg](part-7/snatdnat.png)  

#### Проверить соединение по TCP для SNAT, для этого с ws22 подключиться к серверу Apache на r1 командой:  
`telnet [адрес] [порт]`  
![Part_7.11.jpg](part-7/ws22try.png)  

#### Проверить соединение по TCP для DNAT, для этого с r1 подключиться к серверу Apache на ws22 командой `telnet` (обращаться по адресу r2 и порту 8080)  
![Part_7.12.jpg](part-7/r1try.png)  
