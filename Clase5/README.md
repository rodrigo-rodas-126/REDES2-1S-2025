## Ejemplos Clase 5

## Comandos Importantes

### Agregacion de enlaces
```shell
# Definimos el rango de interfaces fisicas que van a componer la interfaz logica
interface range fa0/3-5
# Establecemos el protocolo a utilizar
channel-protocol <lacp | pagp>
# Establecemos el grupo de enlaces y lo identificamos con un id 
# y establecemos el modo
channel-group <id> mode <active | passive | auto | desirable >
# Mostramos los grupos de enlaces configurados
show etherchannel
show etherchannel port-channel
# Mostramos la configuracion de un grupo de enlaces en especifico
show interfaces port-channel <id>
```

### Lista de Accesos (ACL)
#### Estandar
```shell
access-list <access-list-number> { deny | permit | remark } <source> <source-wildcard>
```

#### Extendida
```shell
access-list <numero> <permit | deny> <protocolo> <origen> <wildcard> [operador] [puerto] <destino> <wildcard> [operador] [puerto]
```

## Comandos Ejemplos Clase 5

### Tabla IP's

|Nombre|IP|CIDR|VLAN|
|-|-|-|-|
|PC0|192.168.10.10|/24|10|
|PC1|192.168.30.10|/24|30|
|PC4|192.168.20.10|/24|20|
|PC5|192.168.40.10|/24|40|
|Interfaz VLAN 10|192.168.10.1|/24|-|
|Interfaz VLAN 20|192.168.20.1|/24|-|
|Interfaz VLAN 30|192.168.30.1|/24|-|
|Interfaz VLAN 40|192.168.40.1|/24|-|
|Port-channel 1|10.0.10.1|/30|-|
|Port-channel 1|10.0.10.2|/30|-|
|Port-channel 2|10.0.20.1|/30|-|
|Port-channel 2|10.0.20.2|/30|-|

## CAPA DISTRIBUCION

### SWD3
```shell
enable
conf ter
vtp version 2
vtp mode server
vtp domain REDES
vlan 10
name REDES1
exit
vlan 30
name AYD1
exit
interface range fa0/1-24
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan all
exit

do write
```

### SWD6
```shell
enable
conf ter
vtp version 2
vtp mode server
vtp domain REDES
vlan 20
name REDES2
exit
vlan 40
name AYD2
exit
interface range fa0/1-24
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan all
exit
do write
```

### SWD1 - SWD2 - SWD4 - SWD5
```shell
enable
conf ter
vtp version 2
vtp mode client
vtp domain REDES
interface range fa0/1-24
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan all
exit
do write
```

## CAPA ACCESO

### SW1
```shell
enable
conf ter
vtp version 2
vtp mode client
vtp domain REDES
interface fa0/2
switchport mode access
switchport access vlan 10
exit
interface range fa0/3-24
switchport mode trunk
switchport trunk allowed vlan all
exit
interface fa0/1
switchport mode trunk
switchport trunk allowed vlan all
exit
do write
```

### SW2
```shell
enable
conf ter
vtp version 2
vtp mode client
vtp domain REDES
interface fa0/3
switchport mode access
switchport access vlan 30
exit
interface range fa0/4-24
switchport mode trunk
switchport trunk allowed vlan all
exit
interface range fa0/1-2
switchport mode trunk
switchport trunk allowed vlan all
exit
do write
```

### SW3
```shell
enable
conf ter
vtp version 2
vtp mode client
vtp domain REDES
interface fa0/2
switchport mode access
switchport access vlan 20
exit
interface range fa0/3-24
switchport mode trunk
switchport trunk allowed vlan all
exit
interface fa0/1
switchport mode trunk
switchport trunk allowed vlan all
exit
do write
```

### SW4
```shell
enable
conf ter
vtp version 2
vtp mode client
vtp domain REDES
interface fa0/2
switchport mode access
switchport access vlan 40
exit
interface range fa0/3-24
switchport mode trunk
switchport trunk allowed vlan all
exit
interface fa0/1
switchport mode trunk
switchport trunk allowed vlan all
exit
do write
```

## CAPA CORE

### SWC1
```shell
enable
conf ter
ip routing
interface range fa0/1-2
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan all
exit
interface vlan 10
ip address 192.168.10.1 255.255.255.0
no shutdown
exit
interface vlan 30
ip address 192.168.30.1 255.255.255.0
no shutdown
exit
# Definimos el rango de interfaces fisicas que van a componer la interfaz logica
interface range fa0/3-5
no switchport
# Establecemos el protocolo a utilizar LACP
channel-protocol lacp
# Establecemos el grupo de enlaces con id 1 y establecemos el modo activo
channel-group 1 mode active
exit
# Ingresamos al grupo de enlaces con id 1
interface port-channel 1
# Definimos la IP al grupo de enlaces con id 1
ip address 10.0.10.1 255.255.255.252
exit
router ospf 1
network 192.168.10.0 0.0.0.255 area 1
network 192.168.30.0 0.0.0.255 area 1
network 10.0.10.0 0.0.0.3 area 1
exit
# Bloquea los pings de la red 192.168.10.0 (VLAN 10) hacia 192.168.20.0 (VLAN 20).
access-list 100 deny icmp 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255 echo
# Permitir las respuestas ping de la red 192.168.10.0 (VLAN 10) hacia 192.168.20.0 (VLAN 20).
access-list 100 permit icmp 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255 echo-reply
# Permite todo el tráfico IP
access-list 100 permit ip any any
# Aplicar la ACL en la interfaz de salida
interface port-channel 1
ip access-group 100 out
exit
do write
```

### SWC2
```shell
enable
conf ter
ip routing
interface range fa0/1-2
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan all
exit
interface vlan 20
ip address 192.168.20.1 255.255.255.0
no shutdown
exit
interface vlan 40
ip address 192.168.40.1 255.255.255.0
no shutdown
exit
# Definimos el rango de interfaces fisicas que van a componer la interfaz logica
interface range fa0/3-5
no switchport
# Establecemos el protocolo a utilizar PAGP
channel-protocol pagp
# Establecemos el grupo de enlaces con id 2 y establecemos el modo automatico
channel-group 2 mode auto
exit
# Ingresamos al grupo de enlaces con id 2
interface port-channel 2
# Definimos la IP al grupo de enlaces con id 2
ip address 10.0.20.1 255.255.255.252
exit
router ospf 1
network 192.168.20.0 0.0.0.255 area 1
network 192.168.40.0 0.0.0.255 area 1
network 10.0.20.0 0.0.0.3 area 1
exit
# Bloquea los pings de la red 192.168.20.0 (VLAN 20) hacia 192.168.10.0 (VLAN 10).
access-list 101 permit icmp 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255 echo
# Permitir las respuestas ping de la red 192.168.20.0 (VLAN 20) hacia 192.168.10.0 (VLAN 10).
access-list 101 deny icmp 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255 echo-reply
# Permite todo el tráfico IP
access-list 101 permit ip any any
# Aplicar la ACL en la interfaz de salida
interface port-channel 2
ip access-group 101 out
exit
do write
```

### SWC0
```shell
enable
conf ter
ip routing
# Definimos el rango de interfaces fisicas que van a componer la interfaz logica
interface range fa0/1-3
no switchport
# Establecemos el protocolo a utilizar LACP
channel-protocol lacp
# Establecemos el grupo de enlaces con id 1 y establecemos el modo pasivo
channel-group 1 mode passive
exit
# Ingresamos al grupo de enlaces con id 1
interface port-channel 1
# Definimos la IP al grupo de enlaces con id 1
ip address 10.0.10.2 255.255.255.252
exit
# Definimos el rango de interfaces fisicas que van a componer la interfaz logica
interface range fa0/4-6
no switchport
# Establecemos el protocolo a utilizar PAGP
channel-protocol pagp
# Establecemos el grupo de enlaces con id 2 y establecemos el modo deseado
channel-group 2 mode desirable
exit
# Ingresamos al grupo de enlaces con id 2
interface port-channel 2
# Definimos la IP al grupo de enlaces con id 2
ip address 10.0.20.2 255.255.255.252
exit
router ospf 1
network 10.0.10.0 0.0.0.3 area 1
network 10.0.20.0 0.0.0.3 area 1
exit
do write
```
