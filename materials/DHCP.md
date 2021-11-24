## Протокол **DHCP**
**DHCP** — протокол прикладного уровня модели TCP/IP, служит для назначения IP-адреса клиенту.
IP-адрес можно назначать вручную каждому клиенту, то есть компьютеру в локальной сети.
Но в больших сетях это очень трудозатратно, к тому же, чем больше локальная сеть, тем выше возрастает вероятность ошибки при настройке.
Поэтому для автоматизации назначения IP был создан протокол **DHCP**.

Получение адреса проходит в четыре шага.
Этот процесс называют *DORA* по первым буквам каждого шага:
- Discovery
- Offer
- Request
- Acknowledgement

<img src="../misc/images/dhcp.png" alt="network_route" width="500"/>

#### Опции **DHCP**
Для работы в сети клиенту требуется не только IP, но и другие параметры **DHCP** — например, маска подсети, шлюз по умолчанию и адрес сервера.
Опции представляют собой пронумерованные пункты, строки данных, которые содержат необходимые клиенту сервера параметры конфигурации.
Дадим описания некоторым опциям:
- Option 1 — маска подсети IP; 
- Option 3 — основной шлюз; 
- Option 6 — адрес сервера DNS (основной и резервный); 
- Option 51 определяет, на какой срок IP-адрес предоставляется в аренду клиенту; 
- Option 55 — список запрашиваемых опций. Клиент всегда запрашивает опции для правильной конфигурации. Отправляя сообщение с Option 55, клиент выставляет список запрашиваемых числовых кодов опций в порядке предпочтения. **DHCP**-сервер старается отправить ответ с опциями в том же порядке. 

##  Force **DHCP** Client (**dhclient**) to renew IP address
You need to use Dynamic Host Configuration Protocol Client i.e., dhclient command.
The client normally doesn’t release the current lease as it is not required by the DHCP protocol.
Some cable ISPs require their clients to notify the server if they wish to release an assigned IP address.
The **dhclient** command, provides a means for configuring one or more network interfaces using the Dynamic Host Configuration Protocol, BOOTP protocol, or if these protocols fail, by statically assigning an address.
Let's see Linux command to force DHCP client release IP address.

*Warning: Releasing your IP address always brings down your network interface/WiFi.
So be careful with remote systems.*

The *-r* flag explicitly releases the current lease, and once the lease has been released, the client exits.
For example, open terminal application and type the command:
```shell
$ sudo dhclient -r
```

Now obtain fresh IP address using **DHCP** on Linux:
```shell
$ sudo dhclient
```

To renew or release an IP address for the concrete interface, for example eth0, enter:
```shell
$ sudo dhclient -r eth0
$ sudo dhclient eth0
```