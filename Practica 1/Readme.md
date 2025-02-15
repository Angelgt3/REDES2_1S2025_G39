# Manual de Configuración de Red

## Tabla de IP's

Dispositivo | Vlan          | IP            | Máscara de Subred
------------|---------------|---------------|-------------------
PC1         | Primaria      | 192.168.13.1  | 255.255.255.0
PC2         | Primaria      | 192.168.13.2  | 255.255.255.0
Laptop0     | Basicos       | 192.168.23.1  | 255.255.255.0
PC4         | Diversificado | 192.168.33.1  | 255.255.255.0
PC5         | Diversificado | 192.168.33.2  | 255.255.255.0
PC6         | Primaria      | 192.168.13.3  | 255.255.255.0
PC7         | Primaria      | 192.168.13.4  | 255.255.255.0
Laptop1     | Diversificado | 192.168.33.3  | 255.255.255.0
PC3         | Basicos       | 192.168.23.2  | 255.255.255.0
PC8         | Basicos       | 192.168.23.3  | 255.255.255.0

## Tabla VLAN

No          | Vlan          | Puerta de enlace
------------|---------------|-------------------
13          | Primaria      | 
23          | Basicos       | 
33          | Diversificado | 

## Configuración de VTP

### Para el servidor VTP
```bash
enable
configure terminal
vtp domain g39
vtp mode server
```

### Para los clientes VTP
```bash
enable
configure terminal
vtp domain g39
vtp mode client
```

### Creación de VLANs
```bash
enable
configure terminal
vlan 13
name Primaria
exit
vlan 23
name Basicos
exit
vlan 33
name Diversificado
exit
```

## Configuración de Puertos

### Puertos de Acceso
```bash
interface FastEthernet0/1
switchport mode access
switchport access vlan 13  # 13 - Primaria, 23 - Basicos, 33 - Diversificado
exit
```

### Puertos Troncales 
```bash
interface range FastEthernet0/1-3
switchport mode trunk
exit
```


# Configuración de protocolos de enrutamiento en routers Cisco

Este archivo contiene un resumen de los comandos utilizados para configurar OSPF, EIGRP y RIP en los routers de la topología.

## Configuración de OSPF
```bash
router ospf 1
 network 10.0.13.0 0.0.0.255 area 0
 network 192.168.13.0 0.0.0.255 area 0
```

## Configuración de RIP
```bash
router rip
 version 2
 network 10.0.23.0
 network 192.168.23.0
 no auto-summary
```

## Configuración de EIGRP
```bash
router eigrp 100
 network 10.0.33.0 0.0.0.255
 network 192.168.33.0 0.0.0.255
 no auto-summary
```

## Verificación de la Configuración
Para verificar que los protocolos están funcionando correctamente, se pueden usar los siguientes comandos:

```bash
show ip route             # Muestra la tabla de enrutamiento
show ip ospf neighbor     # Verifica vecinos OSPF
show ip rip database      # Verifica la base de datos de RIP
show ip eigrp neighbors   # Verifica vecinos EIGRP
show running-config       # Muestra la configuración actual
```





