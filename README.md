# demo2026
Модуль 1 Задание 
1. **Настройка рабочих машин (HQ-SRV, BR-SRV, HQ-CLI)**

**Настройка имён HQ-SRV, BR-SRV, HQ-CLI**
Настройка имени устройства HQ-SRV: hostnamectl set-hostname hq-srv.au-team.irpo; exec bash

Настройка имени устройства BR-SRV: hostnamectl set-hostname br-srv.au-team.irpo; exec bash

Настройка имени устройства HQ-CLI: hostnamectl set-hostname hq-cli.au-team.irpo; exec bash

2. **Настройка ip-адресации рабочих машин**

**Таблица подсетей**

Сеть 		Адрес подсети 		Пул-адресов

SRV-Net 	192.168.100.0/27 	192.168.100.1 - 192.168.100.30

CLI-Net 	192.168.200.0/27 	192.168.200.1 - 192.168.200.30

BR-Net 		192.168.0.0/28 		192.168.0.1 - 192.168.0.14

Vlan999	 	192.168.99.0/29 	192.168.99.1 - 192.168.99.6

ISP-HQ 		172.16.1.0/28 		172.16.1.1 - 172.16.1.14

ISP-BR 		172.16.2.0/28 		172.16.2.1 - 172.16.2.14

**Таблица адресации**
Имя устройства 	Интерфейс 	IPv4		Маска/Префикс 	Шлюз

ISP 		

ens18 		(DHCP) 		/24 		(DHCP)

ens19 		172.16.1.1 	/28

ens20 		172.16.2.1 	/28

HQ-RTR 		

HQ-RTR-ISP 	172.16.1.2 	/28 		172.16.1.1

HQ-RTR-SRV	192.168.100.1 	/27

HQ-RTR-CLI	192.168.200.1 	/27

BR-RTR 		

BR-RTR-ISP 	172.16.2.2 	/28 		172.16.2.1

BR-RTR-SRV 	192.168.0.1 	/28

HQ-SRV 		ens18 		192.168.100.30 	/27 		192.168.100.1

BR-SRV 		ens18 		192.168.0.14 	/28 		192.168.0.1

HQ-CLI 		ens18 		(DHCP)	 	/27 		(DHCP)

**Приводим файлы options, ipv4address, ipv4route в директории /etc/net/ifaces/<имя интерфейса>/ к следующему виду:**



**Файл options в HQ-SRV:**

echo "BOOTPROTO=static" > /etc/net/ifaces/ens18/options

echo "TYPE=eth" >> /etc/net/ifaces/ens18/options

echo "CONFIG\_WIRELESS=no" >> /etc/net/ifaces/ens18/options

echo "SYSTEMD\_BOOTPROTO=static" >> /etc/net/ifaces/ens18/options

echo "CONFIG\_IPV4=yes" >> /etc/net/ifaces/ens18/options

echo "DISABLED=no" >> /etc/net/ifaces/ens18/options

echo "NM\_CONTROLLED=no" >> /etc/net/ifaces/ens18/options

echo "SYSTEMD\_CONTROLLED=no" >> /etc/net/ifaces/ens18/options



**Файл options в BR-SRV:**

echo "BOOTPROTO=static" > /etc/net/ifaces/ens18/options

echo "TYPE=eth" >> /etc/net/ifaces/ens18/options

echo "CONFIG\_WIRELESS=no" >> /etc/net/ifaces/ens18/options

echo "SYSTEMD\_BOOTPROTO=static" >> /etc/net/ifaces/ens18/options

echo "CONFIG\_IPV4=yes" >> /etc/net/ifaces/ens18/options

echo "DISABLED=no" >> /etc/net/ifaces/ens18/options

echo "NM\_CONTROLLED=no" >> /etc/net/ifaces/ens18/options

echo "SYSTEMD\_CONTROLLED=no" >> /etc/net/ifaces/ens18/options





**Файл options в HQ-CLI:**

echo "BOOTPROTO=dhcp" > /etc/net/ifaces/ens18/options

echo "TYPE=eth" >> /etc/net/ifaces/ens18/options

echo "CONFIG\_WIRELESS=no" >> /etc/net/ifaces/ens18/options

echo "SYSTEMD\_BOOTPROTO=dhcp4" >> /etc/net/ifaces/ens18/options

echo "CONFIG\_IPV4=yes" >> /etc/net/ifaces/ens18/options

echo "DISABLED=no" >> /etc/net/ifaces/ens18/options

echo "NM\_CONTROLLED=no" >> /etc/net/ifaces/ens18/options

echo "SYSTEMD\_CONTROLLED=no" >> /etc/net/ifaces/ens18/options



**Файл ipv4address в HQ-SRV:**

echo "192.168.100.30/27" > /etc/net/ifaces/ens18/ipv4address



**Файл ipv4address в BR-SRV:**

echo "192.168.0.14/28" > /etc/net/ifaces/ens18/ipv4address



**Файл ipv4route в HQ-SRV:**

echo "default via 192.168.100.1" > /etc/net/ifaces/ens18/ipv4route



**Файл ipv4route в BR-SRV:**

echo "default via 192.168.0.1" > /etc/net/ifaces/ens18/ipv4route



**Настройка роутеров (HQ-RTR, BR-RTR)**



**Настройка имени устройства HQ-RTR:**

en

conf

hostname hq-rtr.au-team.irpo

end

wr mem



**Настройка имени устройства BR-RTR:**

en

conf

hostname br-rtr.au-team.irpo

end

wr mem



**Настройка ip-адресации на HQ-RTR (с разделением на VLAN)**

en

conf

int HQ-RTR-ISP

ip address 172.16.1.2/28

port te0

service-inst 0

enc untagged

conn ip int HQ-RTR-ISP

exit



int HQ-RTR-SRV

ip address 192.168.100.1/27

port te1

service-inst 100

enc dot1q 100

rewrite pop 1

conn ip int HQ-RTR-SRV

exit



int HQ-RTR-CLI

ip address 192.168.200.1/27

port te1

service-inst 200

enc dot1q 200

rewrite pop 1

conn ip int HQ-RTR-CLI

end

wr mem



**Адресация на BR-RTR (без разделения на VLAN)**

en

conf

int BR-RTR-ISP

ip address 172.16.2.2/28

port te0

service-inst 0

enc untagged

conn ip int BR-RTR-ISP

exit



int BR-RTR-SRV

ip address 192.168.0.1/28

port te1

service-inst 0

enc untagged

conn ip int  BR-RTR-SRV

end

wr mem



**Добавление маршрута по умолчанию в HQ-RTR:** ip route 0.0.0.0 0.0.0.0 172.16.1.1



**Добавление маршрута по умолчанию в BR-RTR:** ip route 0.0.0.0 0.0.0.0 172.16.2.1



**Модуль 1 Задание 2**



1. **Настройка интерфейсов, смотрящих в сторону HQ-RTR и BR-RTR (ens19, ens20)**



**Файл ipv4address в ens19: echo "172.16.1.1/28" > etc/net/ifaces/ens19/ipv4address**



**Файл ipv4address в ens20: echo "172.16.2.1/28" > etc/net/ifaces/ens20/ipv4address**



**Файл options в /etc/net/ifaces/ens19 приводим к следующему виду:**

echo "BOOTPROTO=static" > /etc/net/ifaces/ens19/options

echo "TYPE=eth" >> /etc/net/ifaces/ens19/options

echo "NM\_CONTROLLED=no" >> /etc/net/ifaces/ens19/options

echo "DISABLED=no" >> /etc/net/ifaces/ens19/options

echo "CONFIG\_WIRELESS=no" >> /etc/net/ifaces/ens19/options

echo "SYSTEMD\_BOOTPROTO=static" >> /etc/net/ifaces/ens19/options

echo "CONFIG\_IPV4=yes" >> /etc/net/ifaces/ens19/options

echo "SYSTEMD\_CONTROLLED=no" >> /etc/net/ifaces/ens19/options



**Файл options в /etc/net/ifaces/ens20 приводим к следующему виду:**

echo "BOOTPROTO=static" > /etc/net/ifaces/ens20/options

echo "TYPE=eth" >> /etc/net/ifaces/ens20/options

echo "NM\_CONTROLLED=no" >> /etc/net/ifaces/ens20/options

echo "DISABLED=no" >> /etc/net/ifaces/ens20/options

echo "CONFIG\_WIRELESS=no" >> /etc/net/ifaces/ens20/options

echo "SYSTEMD\_BOOTPROTO=static" >> /etc/net/ifaces/ens20/options

echo "CONFIG\_IPV4=yes" >> /etc/net/ifaces/ens20/options

echo "SYSTEMD\_CONTROLLED=no" >> /etc/net/ifaces/ens20/options



**2. Включение маршрутизации**



Обновляем системные пакеты: apt-get update



Устанавливаем текстовый редактор: apt-get install nano



В файле /etc/net/sysctl.conf изменяем строку net.ipv4.ip\_forward (0 меняем на 1): nano /etc/net/sysctl.conf



Изменения в файле sysctl.conf применяем следующей командой: sysctl -p /etc/sysctl.conf



**3. Настройка NAT на ISP**



Обновляем системные пакеты: apt-get update



Устанавливаем iptables: apt-get install iptables



Прописываем  следующие настройки для iptables:

iptables -t nat -A POSTROUTING -o ens18 -j MASQUERADE -s 172.16.1.0/28

iptables -t nat -A POSTROUTING -o ens18 -j MASQUERADE -s 172.16.2.0/28



Сохраняем изменения в правилах:

iptables-save -f /etc/sysconfig/iptables

sysctl -p /etc/sysctl.conf



Запускаем и добавляем его в автозагрузку: systemctl enable --now iptables



Перезапускаем его: systemctl restart iptables



**Модуль 1 Задание 3**



1. **Создание пользователя sshuser на HQ-SRV и BR-SRV**



Создаем пользователя с идентификатором 1010 (-u) и принадлежностью к группе wheel (-G), добавляем право в файл /etc/sudoers и задаем ему пароль:

useradd -u 1010 -G wheel sshuser

echo "sshuser ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

passwd sshuser



**2. Создание пользователя net\_admin на HQ-RTR и BR-RTR**



Создаем пользователя и задаем ему роль:

en

conf

username net\_admin

password P@$$word

role admin

end

wr mem



**Модуль 1 Задание 4: выполнено в 1 задании**



**Модуль 1 Задание 5**



1. **Настраиваем параметры службы ssh**

Назначаем порт для подключения: echo "Port 2024" >> /etc/openssh/sshd\_config



Устанавливаем максимальное количество попыток входа: echo "MaxAuthTries 2" >> /etc/openssh/sshd\_config



Устанавливаем парольную защиту: echo "PasswordAuthentication yes" >> /etc/openssh/sshd\_config



Разрешаем доступ пользователю sshuser: echo "AllowUsers(4 пробела)sshuser" >> /etc/openssh/sshd\_config



Настраиваем предупреждение безопасности: echo "Banner /etc/openssh/bannermotd" >> /etc/openssh/sshd\_config



Настраиваем текст для предупреждения: echo "Authorized access only" > /etc/openssh/bannermotd



**2. Перезапускаем службу ssh: systemctl restart sshd**



**Модуль 1 Задание 6**



1. **Создание туннеля на HQ-RTR**

**Создаем интерфейс GRE-туннеля на HQ-RTR, назначаем ему IP-адрес и выставляем mtu. Генерируем туннель:**

en

conf

int tunnel.0

ip address 172.16.0.1/30

ip mtu 1400

ip tunnel 172.16.1.2 172.16.2.2 mode gre

end

wr mem



**2. Создание туннеля на BR-RTR**

**Создаем интерфейс GRE-туннеля на BR-RTR, назначаем ему IP-адрес и выставляем mtu. Генерируем туннель:**

en

conf

int tunnel.0

ip address 172.16.0.2/30

ip mtu 1400

ip tunnel 172.16.2.2 172.16.1.2 mode gre

end

wr mem



**Модуль 1 Задание 7**



1. **Настройка OSPF на HQ-RTR**

Создаем процесс OSPF, указываем идентификатор маршрутизатора, объявляем сети и указываем пассивные интерфейсы:

conf

router ospf 1

router-id 1.1.1.1

network 172.16.0.0/30 area 0

network 192.168.100.0/27 area 0

network 192.168.200.0/27 area 0

passive-interface default

no passive-interface tunnel.0

end

wr mem



**2. Настройка OSPF на BR-RTR**

Маршрутизация OSPF на BR-RTR настраивается аналогично, после нее можно произвести проверку OSPF-соседей:

conf

router ospf 2

router-id 2.2.2.2

network 172.16.0.0/30 area 0

network 192.168.0.0/28 area 0

passive-interface default

no passive-interface tunnel.0

end

wr mem

sh ip ospf neighbor



**Модуль 1 Задание 8**



1. **Настройка NAT на HQ-RTR**

Указываем внутренние и внешние интерфейсы, создаем пул и правило трансляции адресов, указывая внешний интерфейс:

conf

int HQ-RTR-ISP

ip nat outside

int HQ-RTR-SRV

ip nat inside

int HQ-RTR-CLI

ip nat inside

exit

ip nat pool HQ 192.168.100.1-192.168.100.30,192.168.200.1-192.168.200.30

ip nat source dynamic inside-to-outside pool HQ overload interface HQ-RTR-ISP

wr mem



**2. Настройка NAT на BR-RTR**

conf

int BR-RTR-ISP

ip nat outside

int BR-RTR-SRV

ip nat inside

exit

ip nat pool BR 192.168.0.1-192.168.0.14

ip nat source dynamic inside-to-outside pool BR overload interface BR-RTR-ISP

wr mem



**Модуль 1 Задание 9**



Создаем пул для DHCP-сервера на HQ-RTR, настраиваем и привязываем к интерфейсу:

conf

ip pool HQ-CLI 192.168.200.1-192.168.200.30

dhcp-server 1

pool HQ-CLI 1

mask 27

gateway 192.168.200.1

dns 192.168.100.30

domain-name au-team.irpo

exit

interface HQ-RTR-CLI

dhcp-server 1

end

wr mem



pool HQ-CLI 1 - привязка пула

mask 27 - указание маски для выдаваемых адресов из пула

gateway 192.168.200.1 - указание шлюза по умолчанию для клиентов

dns 192.168.100.2 - указание DNS-сервера для клиентов

domain-name au-team.irpo - указание DNS-суффикса для офиса HQ



**Модуль 1 задание 10**



1. Добавляем dns сервера гугла для установки утилиты

mcedit /etc/resolv.conf

Добавляем следующую строку в него:

nameserver 8.8.8.8



2\. Обновим пакеты и установим её командами:

apt-get update

apt-get install dnsmasq



3\. Открываем конфигурационный файл: nano /etc/dnsmasq.conf



И прописываем следующие настройки

listen-address=192.168.100.30

domain=au-team.irpo

strict-order

no-resolv

server=8.8.8.8

server=8.8.4.4

address=/isp.au-team.irpo/(ip-адрес порта ens18 у isp)

address=/hq-rtr.au-team.irpo/192.168.100.1

address=/hq-srv.au-team.irpo/192.168.100.30

address=/hq-cli.au-team.irpo/192.168.200.2

address=/br-rtr.au-team.irpo/192.168.0.1

address=/hq-srv.au-team.irpo/192.168.0.14



4\. Сохраните файл /etc/dnsmasq.conf.



5\. Перезапустите сервис: systemctl restart dnsmasq



6\. Проверьте работу сервиса: systemctl status dnsmasq



7\. После настройки dns-сервера прописываем всем машинам -"HQ-SRV, HQ-RTR, BR-RTR, BR-SRV", кроме HQ-CLI (так как настроили для него dhcp) в /etc/resolv.conf:

nameserver 192.168.100.30



**Модуль 1 Задание 11**



1. **Настройка часового пояса на HQ-SRV, BR-SRV, HQ-CLI**

timedatectl set-timezone Asia/Yekaterinburg

timedatectl status



**2. Настройка часового пояса на HQ-RTR, BR-RTR**

conf

ntp timezone utc+5

do show ntp timezone

wr mem

