# Последовательность действий

## Настройка Server Office

### Domain Controller

1. Установка Windows Server 2025
2. Выдача IP-адреса: 10.0.1.3/24
3. Установка компонентов **DNS-сервер** и **Active Directory**

### Nginx Server

1. Установка чистой Debian 13
2. Выдача IP-адреса (*/etc/network/interfaces*): 10.0.1.2/24
3. Установка nginx

### Mikrotik Switch

1. Установка RouterOS
2. Выдача IP-адреса для взаимодействия с маршрутизатором на интерфейс `eth0`: 192.168.1.2/24
3. Выдача IP-адреса для взаимодействия с серверами на интерфейс `VLAN 10`: 10.0.1.1/24
4. Новое правило `firewall/nat` для возможности серверам выходить в Интернет:
```
ip/firewall/nat/add chain=srcnat out-interface=eth0 in-interface=eth1 action=masquerade 
```
5. Новое правило маршрутизации для возможности серверам выходить в Интернет:
```
ip/route/add dst-address=0.0.0.0/0 gateway=192.168.1.1
```
\- это правило перенаправляет любой пакет на маршрутизатор

### Mikrotik Router (Servers)

1. Установка RouterOS
2. Добавление DHCP-клиента на интерфейс `eth0` для получения IP-адреса от провайдера
3. Выдача IP-адреса для взаимодействия с коммутатором на интерфейс `eth1`: 192.168.1.1/24
4. Новое правило `firewall/nat` для возможности серверам выходить в Интернет:
```
ip/firewall/nat/add chain=srcnat out-interface=eth0 in-interface=eth1 action=masquerade 
```
5. Создание WireGuard интерфейса `wg0` с прослушкой порта 13231:
```
interface/wireguard add mtu=1420 listen-port=13231
```
6. Выдача IP-адреса для WireGuard на интерфейс `wg0`: 10.1.1.1/24
7. Создание WireGuard Peer:
```
interface/wireguard/peer add interface=wg0 public-key="КЛЮЧ_КЛИЕНТСКОГО_РОУТЕРА" allowed-address=10.1.1.2/24
```
9. Новое правило маршрутизации для возможности клиентам взаимодействовать с серверами:
```
ip/route add dst-address=10.0.1.0/24 gateway=192.168.1.2
```
10. Новое правило `firewall/nat` для возможности клиентам взаимодействовать с серверами:
```
ip/firewall/nat/add chain=srcnat out-interface=eth1 in-interface=wg0 action=masquerade 
```


## Настройка Client Office

### Mikrotik Router (Clients)

1. Установка RouterOS
2. Добавление DHCP-клиента на интерфейс `eth0` для получения IP-адреса от провайдера
3. Выдача IP-адреса для взаимодействия с клиентами на интерфейс `eth1`: 10.0.2.1/24
4. Новое правило `firewall/nat` для возможности клиентам выходить в Интернет:
```
ip/firewall/nat/add chain=srcnat out-interface=eth0 in-interface=eth1 action=masquerade 
```
5. Создание WireGuard интерфейса `wg0` с прослушкой порта 13231:
```
interface/wireguard add mtu=1420 listen-port=13231
```
6. Выдача IP-адреса для WireGuard на интерфейс `wg0`: 10.1.1.2/24
7. Создание WireGuard Peer:
```
interface/wireguard/peer add interface=wg0 public-key="КЛЮЧ_СЕРВЕРНОГО_РОУТЕРА" endpoint-address=172.20.30.11 endpoint-port=13231 allowed-address=10.1.1.0/24,10.0.1.0/24
```
8. Новое правило маршрутизации для возможности клиентам взаимодействовать с серверами:
```
ip/route add dst-address=10.0.1.0/24 gateway=10.1.1.1
```
9. Новое правило `firewall/nat` для возможности клиентам взаимодействовать с серверами:
```
ip/firewall/nat/add chain=srcnat out-interface=wg0 in-interface=eth1 action=masquerade 
```


### Windows Client

1. Установка Windows 10
2. Настройка сети на получение адреса по DHCP
3. Настройка DNS-сервера: 10.0.1.3
4. Выполнить присоединение к домену

## Проверка работоспособности

1. WireGuard

2. Домен

3. 
