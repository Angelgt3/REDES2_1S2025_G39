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

