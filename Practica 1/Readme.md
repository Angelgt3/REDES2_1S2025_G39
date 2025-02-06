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