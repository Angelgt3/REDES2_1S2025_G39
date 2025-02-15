# Manual de Configuración de Red

## Tabla de IP's

Dispositivo | Vlan          | IP            | Máscara de Subred
------------|---------------|---------------|-------------------
PC1         | Primaria      | 192.168.13.1  | 255.255.255.0
PC2         | Primaria      | 192.168.13.2  | 255.255.255.0
Laptop0     | Basicos       | 192.168.23.1  | 255.255.255.0
PC4         | Diversificado | 192.168.33.1  | 255.255.255.0
PC5         | Diversificado | 192.168.33.2  | 255.255.255.0
PC6         | Primaria      | 192.168.43.3  | 255.255.255.0
PC7         | Primaria      | 192.168.43.4  | 255.255.255.0
Laptop1     | Diversificado | 192.168.63.3  | 255.255.255.0
PC3         | Basicos       | 192.168.53.2  | 255.255.255.0
PC8         | Basicos       | 192.168.53.3  | 255.255.255.0

## Tabla VLAN

No          | Vlan          
------------|---------------
13          | Primaria      
23          | Basicos       
33          | Diversificado 
43          | Primaria          - Lado derecho
53          | Basicos           - Lado derecho
63          | Diversificado     - Lado derecho
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

## Cambio de Hostname
```bash
enable
configure terminal
hostname SWA_G39 # A por el numero de switch que es
exit
```

## Configuración de Password
```bash
enable
configure terminal
enable secret redes2grupo39
exit
```

## Configuración de Spanning Tree (Rapid PVST - Lado Izquierdo)
```bash
spanning-tree mode rapid-pvst
spanning-tree vlan 1 root primary
spanning-tree vlan 1 root secondary
spanning-tree portfast
spanning-tree bpduguard enable
switchport nonegotiate
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address 0011.2233.4455
```

## Configuración de Spanning Tree (PVST - Lado Derecho)
```bash
spanning-tree mode pvst
spanning-tree vlan 1 root primary
spanning-tree vlan 1 root secondary
spanning-tree portfast
spanning-tree bpduguard enable
switchport nonegotiate
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address 0011.2233.4455
```
## Configuración de Seguridad de Puertos
```bash
interface FastEthernet0/1
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address 0011.2233.4455
exit
```

## Configuración de protocolos de enrutamiento en routers Cisco

Este archivo contiene un resumen de los comandos utilizados para configurar OSPF, EIGRP y RIP en los routers de la topología.

### Configuración de OSPF
```bash
router ospf 1
 network 10.0.13.0 0.0.0.255 area 0
 network 192.168.13.0 0.0.0.255 area 0
```

### Configuración de RIP
```bash
router rip
 version 2
 network 10.0.23.0
 network 192.168.23.0
 no auto-summary
```

### Configuración de EIGRP
```bash
router eigrp 100
 network 10.0.33.0 0.0.0.255
 network 192.168.33.0 0.0.0.255
 no auto-summary
```

### Verificación de la Configuración
Para verificar que los protocolos están funcionando correctamente, se pueden usar los siguientes comandos:

```bash
show ip route             # Muestra la tabla de enrutamiento
show ip ospf neighbor     # Verifica vecinos OSPF
show ip rip database      # Verifica la base de datos de RIP
show ip eigrp neighbors   # Verifica vecinos EIGRP
show running-config       # Muestra la configuración actual
```

---

# Elección de Escenario con Mejor Resultado de Convergencia

## Pasos 
1. Se identificó el enlace activo Forwarding y el enlace bloqueado Blocked en cada VLAN con:
`show spanning-tree`

2. Se deshabilitó el enlace principal en cada VLAN para simular una falla de conexión:
`int fa0/1`  
`shutdown`

3. Se observó cuánto tardó en activarse el enlace alternativo en cada protocolo.

## Resultados Obtenidos

| Escenario | Protocolo Spanning-Tree | Red Primaria | Red Básicos | Red Diversificado | Tiempo de Convergencia |
|-----------|-------------------------|--------------|-------------|-------------------|------------------------|
| 1         | PVST                    | Activo       | Bloqueado   | Activo            | 35-50 segundos         |
| 2         | Rapid PVST              | Activo       | Bloqueado   | Activo            | 1-4 segundos           |


Se observó que el protocolo PVST tiene un tiempo de convergencia lento debido a los estados intermedios de Listening y Learning. En cambio el protocolo Rapid PVST reduce drásticamente este tiempo al eliminar dichos estados y usar un sistema de Proposal/Agreement. Por ello, Rapid PVST es la mejor opción para garantizar una red estable y de rápida recuperación ante fallos.