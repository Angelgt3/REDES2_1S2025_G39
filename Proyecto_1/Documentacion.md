# Documentación de Configuración de Red

## Deshabilitar la resolución de nombres DNS:
```bash
enable
configure terminal
no ip domain-lookup
```
## Creación de VLANs:
```bash
enable
configure terminal
vlan 40
name VLAN_Naranja_EdificioIZQ
exit
vlan 41
name VLAN_Naranja_EdificioDER
exit
vlan 42
name VLAN_Verde_EdificioIZQ
exit
vlan 43
name VLAN_Verde_EdificioDER
exit
vlan 44
name VLAN_Azul_Admin
exit
```
## Asignar IP a la VLAN de administración:
```bash
interface Vlan44
ip address 192.168.39.1 255.255.255.0
no shutdown
exit
```
## Resumen de IPs para la VLAN de Administración:

Edificio        | IP de Administración
----------------|---------------
Core-Edificio1  | 192.168.39.1/24
Core-Edificio2  | 192.168.39.2/24
Core-Edificio3  | 192.168.39.3/24
Core-Edificio4  | 192.168.39.4/24

## Configurar LACP (Lado Izquierdo):
```bash
interface range fa0/1 - 3
channel-group 1 mode active
exit

interface range gi1/0/1 - 3
channel-group 1 mode active
exit
```

## Configurar el Port-Channel como troncal:
```bash
interface port-channel 1
switchport mode trunk
switchport trunk allowed vlan 40,41,42,43,44
exit
```

## Configuración de PAGP (Lado Derecho):
```bash
interface range fa0/1 - 3
channel-group 2 mode desirable
exit
```

## Configurar el Port-Channel como troncal:
```bash
interface port-channel 2
switchport mode trunk
switchport trunk allowed vlan 40,41,42,43,44
exit
```

### Comandos para verificar:
```bash
show etherchannel summary
show interfaces trunk
```

## Configuración de VTP:
```bash
vtp domain G39
vtp mode server
vtp password redes2

vtp domain G39
vtp mode client
vtp password redes2
```

### Verificar
```bash
show vtp status
```

## Configurar STP:

```bash
enable
configure terminal
spanning-tree mode rapid-pvst
spanning-tree vlan 40 priority 4096
spanning-tree vlan 41 priority 8192
spanning-tree vlan 42 priority 12288
spanning-tree vlan 43 priority 16384
exit
show spanning-tree
```

## Configuracion OSPF

```bash
enable
conf t
ip routing
router ospf 1
 no network 192.168.39.0 0.0.0.255 area 0
 no network 192.168.39.0 0.0.0.31 area 0
 no network 192.168.39.32 0.0.0.31 area 0
 no network 10.0.39.0 0.0.0.255 area 0
 network 192.168.39.0 0.0.0.255 area 0
exit

interface vlan 40
 ip address 192.168.39.1 255.255.255.224
 ip ospf 1 area 0
interface vlan 41
 ip address 192.168.39.33 255.255.255.224
 ip ospf 1 area 0
interface vlan 42
 ip address 192.168.39.65 255.255.255.224
 ip ospf 1 area 0
interface vlan 43
 ip address 192.168.39.97 255.255.255.224
 ip ospf 1 area 0
interface vlan 44
 ip address 192.168.39.129 255.255.255.224
 ip ospf 1 area 0

interface port-channel1
 switchport trunk allowed vlan 40-44
interface fa0/4
 switchport trunk allowed vlan 40-44
exit

```

## LACP

```bash
int range fa 0/1-3
channel-protocol lacp
channel-group 4 mode active
no shut
```


## Configuracion EIGRP

```bash
enable
conf t
no ip domain-lookup
hostname SW8_G15
ip routing
int gi 1/0/1
no switchport
ip add 10.0.39.1 255.255.255.252
no shut
exit
int gi 1/0/2
no switchport
ip add 10.0.39.5 255.255.255.252
no shut
exit
int gi 1/1/1
no switchport
ip add 10.0.39.9 255.255.255.252
no shut
exit
int gi 1/1/2
no switchport
ip add 10.0.39.13 255.255.255.252
no shut
exit
router eigrp 39
no auto-summary
network 10.0.39.0 0.0.0.3
network 10.0.39.4 0.0.0.3
network 10.0.39.8 0.0.0.3
network 10.0.39.12 0.0.0.3
no auto-summary
exit
do wr
```
