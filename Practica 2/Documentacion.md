# Topologia 



# Vlan

Nombre             | Vlan
-------------------|---------------
ADMIN              |  13       
ESTUDIANTES        |  23    
WEB_SERVERS        |  33    
DHCP_SERVER        |  43    


### **VLANs y Subredes (Grupo 39)**  
| Nombre VLAN      | ID VLAN | Subred            | Gateway         | Máscara            | Rango de IPs útiles           | Hosts disponibles |  
|------------------|---------|-------------------|-----------------|--------------------|-------------------------------|-------------------|  
| **ADMIN**        | 13      | 192.168.39.0/28   | 192.168.39.1    | 255.255.255.240    | 192.168.39.2 - 192.168.39.14  | 14                |  
| **ESTUDIANTES**  | 23      | 192.168.39.64/26  | 192.168.39.65   | 255.255.255.192    | 192.168.39.66 - 192.168.39.126 | 60                |  
| **WLAN_PISO_2**  | 23      | 192.168.39.128/26 | 192.168.39.129  | 255.255.255.192    | 192.168.39.130 - 192.168.39.190 | 60                |  
| **WLAN_PISO_3**  | 23      | 192.168.39.192/26 | 192.168.39.193  | 255.255.255.192    | 192.168.39.194 - 192.168.39.254 | 60                |  
| **WEB_SERVERS**  | 33      | 192.168.39.224/28 | 192.168.39.225  | 255.255.255.240    | 192.168.39.226 - 192.168.39.238 | 14                |  
| **DHCP_SERVERS** | 43      | 192.168.39.240/28 | 192.168.39.241  | 255.255.255.240    | 192.168.39.242 - 192.168.39.254 | 14                |  

<<<<<<< HEAD


# HSRP

conf t
interface Gi0/1
 ip address 192.168.39.1 255.255.255.240
 standby 1 ip 192.168.39.2
 standby 1 priority 110
 standby 1 preempt
 standby 1 track Gi0/0
exit


conf t
interface Gi0/1
 ip address 192.168.39.65 255.255.255.192
 standby 1 ip 192.168.39.2
 standby 1 priority 100
 standby 1 preempt
 standby 1 track Gi0/0
exit
=======
# Para todos
enable
configure terminal
hostname NOMBRE_DISPOSITIVO
no ip domain-lookup
line console 0
  logging synchronous
  exit



service password-encryption
enable secret clase
username admin secret admin  


# SW1
enable
configure terminal
hostname SW1
no ip domain-lookup
enable secret clase
service password-encryption

! Crear VLANs
vlan 13
 name ADMIN
vlan 23
 name ESTUDIANTES
vlan 33
 name WEB_SERVERS
vlan 43
 name DHCP_SERVERS
exit

! Configurar Port-Channels (LACP) 
interface range fa0/5-8
 channel-group 1 mode active
 no shutdown
 exit
interface range fa0/9-12
 channel-group 2 mode active
 no shutdown
 exit
interface range fa0/1-4
 channel-group 3 mode active
 no shutdown
 exit

! Configurar interfaces Port-Channel como trunks
interface Port-channel 1
 switchport mode trunk
 switchport trunk allowed vlan all
 no shutdown
 exit
interface Port-channel 2
 switchport mode trunk
 switchport trunk allowed vlan all
 no shutdown
 exit
interface Port-channel 3
 switchport mode trunk
 switchport trunk allowed vlan all
 no shutdown
 exit

 ! Interfaces VLAN para routing
interface Vlan13
 ip address 192.168.39.1 255.255.255.240   
 no shutdown
 exit
interface Vlan23
 ip address 192.168.39.65 255.255.255.192
 no shutdown
 exit

 ! Configurar DHCP Relay hacia el servidor DHCP (192.168.39.242)
ip helper-address 192.168.39.242   # Enviar solicitudes DHCP a esta IP

! Aplicar helper-address en las VLANs necesarias
interface Vlan13
 ip helper-address 192.168.39.242
 exit
interface Vlan23
 ip helper-address 192.168.39.242
 exit

ip routing 
 router eigrp 100
 network 10.0.39.0 0.0.0.255       
 network 192.168.39.0 0.0.0.15      
 network 192.168.39.64 0.0.0.63     
 no auto-summary
 exit

 ! Conexión a Router0 (VLAN 13 y 23 - Trunk)
interface fa0/13
 switchport mode trunk
 switchport trunk allowed vlan all 
 no shutdown
 exit

! Conexión a Router1 (VLAN 13 y 23 - Trunk)
interface fa0/14
 switchport mode trunk
 switchport trunk allowed vlan all
 no shutdown
 exit

 # SW0
 enable
configure terminal
hostname SW0
no ip domain-lookup
enable secret clase
service password-encryption

! 1. Crear VLANs para servidores
vlan 33
 name WEB_SERVERS
vlan 43
 name DHCP_SERVERS
exit

! 2. Configurar LACP hacia SW1 (4 puertos)
interface range fa0/2-5
 channel-group 1 mode active
 no shutdown
 exit

! 3. Configurar Port-Channel como trunk (permite VLANs 33 y 43)
interface Port-channel 1
 switchport mode trunk
 switchport trunk allowed vlan all
 no shutdown
 exit

 ! 4. Configurar interfaz hacia S0 (Switch Capa 2)
interface fa0/1
 switchport mode trunk
 switchport trunk allowed vlan all
 no shutdown
 exit

! 5. Interfaces VLAN para routing (Gateways)
interface Vlan33
 ip address 192.168.39.225 255.255.255.240
 no shutdown
 exit

interface Vlan43
 ip address 192.168.39.241 255.255.255.240
 no shutdown
 exit

! 6. Habilitar routing IP y EIGRP
ip routing

router eigrp 100
 network 10.0.39.0 0.0.0.255        # Enlace con SW1
 network 192.168.39.224 0.0.0.15    # VLAN 33
 network 192.168.39.240 0.0.0.15    # VLAN 43
 no auto-summary
 exit

! 7. Guardar configuración
end
write memory



# S0
enable
configure terminal
hostname S0
no ip domain-lookup

! 1. Crear VLANs (igual que en SW0)
vlan 33
 name WEB_SERVERS
vlan 43
 name DHCP_SERVERS
exit

! 2. Configurar trunk hacia SW0
interface fa0/3
 switchport mode trunk
 switchport trunk allowed vlan all
 no shutdown
 exit

! 3. Configurar puertos de acceso para servidores
interface FastEthernet0/1
 switchport mode access
 switchport access vlan 33
 no shutdown
 exit

interface FastEthernet0/2
 switchport mode access
 switchport access vlan 43
 no shutdown
 exit

! 4. Guardar configuración
end
write memory



# SW2
enable
configure terminal
hostname SW2
no ip domain-lookup

! 1. Crear VLAN 23 (si no existe)
vlan 23
 name ESTUDIANTES
exit

! 2. Configurar LACP hacia SW1 (4 puertos)
interface range fa0/1-4
 channel-group 1 mode active
 no shutdown
 exit

! 3. Configurar Port-Channel como trunk
interface Port-channel 1
 switchport mode trunk
 switchport trunk allowed vlan all
 no shutdown
 exit

! 4. Configurar interfaz VLAN 23 (Gateway WLAN)
interface Vlan23
 ip address 192.168.39.129 255.255.255.192
 no shutdown
 exit

! 5. Habilitar routing (si es multicapa)
ip routing

! 6. Conexión a RW2 (Router Wireless)
interface fa0/5
 switchport mode access
 switchport access vlan 23
 no shutdown
 exit
>>>>>>> 0b59cfde6099bed615414bc47fafea5be5c7a32e
