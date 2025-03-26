# Topologia 



# Vlan

Nombre             | Vlan
-------------------|---------------
ADMIN              |  13       
ESTUDIANTES        |  23    
WEB_SERVERS        |  33    
DHCP_SERVER        |  43    


## Tabla de VLANs (Edificio de Ingeniería)

| Nombre VLAN      | ID VLAN          | Subred            | Gateway         | Máscara            | Rango de IPs útiles           | Hosts |
|------------------|------------------|-------------------|-----------------|--------------------|-------------------------------|-------|
| ADMIN            | 13      | 192.168.39.0/28    | 192.168.39.1     | 255.255.255.240    | 192.168.39.2 - 192.168.39.14    | 14    |
| ESTUDIANTES      | 23      | 192.168.39.16/26   | 192.168.39.17    | 255.255.255.192    | 192.168.39.18 - 192.168.39.78   | 60    |
| WLAN_PISO2       | 53      | 192.168.39.80/26   | 192.168.39.81    | 255.255.255.192    | 192.168.39.82 - 192.168.39.142  | 60    |
| WLAN_PISO3       | 63      | 192.168.39.144/26  | 192.168.39.145   | 255.255.255.192    | 192.168.39.146 - 192.168.39.206 | 60    |

## Biblioteca Central (Red 192.168.100.0/24)

| VLAN           | Subred              | Gateway          | Máscara            | Rango de IPs útiles              | Hosts |
|----------------|---------------------|------------------|--------------------|----------------------------------|-------|
| WEB_SERVERS    | 192.168.100.0/25    | 192.168.100.1    | 255.255.255.128    | 192.168.100.2 - 192.168.100.126  | 126   |
| DHCP_SERVERS   | 192.168.100.128/25  | 192.168.100.129  | 255.255.255.128    | 192.168.100.130 - 192.168.100.254| 126   |

# HSRP
```bash
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
```

# SW3
```bash
enable
conf t
hostname SW3

ip routing

spanning-tree mode pvst

interface Port-channel3
switchport trunk encapsulation dot1q
 no switchport
  ip address 10.0.39.9 255.255.255.252

interface range FastEthernet0/1-4
switchport trunk encapsulation dot1q
 no switchport
 no ip address
 channel-group 3 mode active

interface FastEthernet0/5
 no switchport
 ip address 192.168.39.145 255.255.255.192
 ip helper-address 192.168.100.130
 duplex auto
 speed auto

interface Vlan1
 no ip address

router eigrp 1
 network 10.0.39.8 0.0.0.3
 network 192.168.39.144 
 
 no auto-summary

end
```

# SW2 
```bash
enable
conf t
hostname SW2

ip routing

spanning-tree mode pvst


interface Port-channel1
switchport trunk encapsulation dot1q
 no switchport
  ip address 10.0.39.1 255.255.255.252

interface range FastEthernet0/1-4
switchport trunk encapsulation dot1q
 no switchport
 no ip address
 channel-group 1 mode active

interface FastEthernet0/5
 no switchport
 ip address 192.168.39.81 255.255.255.192
 ip helper-address 192.168.100.130
 duplex auto
 speed auto

interface Vlan1
 no ip address

router eigrp 1
 network 10.0.39.0 0.0.0.3
 network 192.168.39.80
 no auto-summary

end
```


# SW1 
```bash
enable
conf t
hostname SW1

ip routing

spanning-tree mode pvst

! Configuración del Port-channel hacia SW2 (Fa0/5-8)
interface Port-channel1 
switchport trunk encapsulation dot1q
 no switchport
 ip address 10.0.39.2 255.255.255.252
!

! Interfaces físicas hacia SW2 (Fa0/5-8)
interface range FastEthernet0/5-8
switchport trunk encapsulation dot1q
 no switchport
 no ip address             
 channel-group 1 mode active  
!

! Configuración hacia SW0 
interface Port-channel2   
switchport trunk encapsulation dot1q
 no switchport
 ip address 10.0.39.6 255.255.255.252
!

interface range FastEthernet0/1-4
switchport trunk encapsulation dot1q
 no switchport
 no ip address
 channel-group 2 mode active 
!

! Configuración del Port-channel hacia SW3 (Fa0/9-12)
interface Port-channel3
switchport trunk encapsulation dot1q
 no switchport
 ip address 10.0.39.10 255.255.255.252
!

! Interfaces físicas hacia SW3 (Fa0/9-12)
interface range FastEthernet0/9-12
switchport trunk encapsulation dot1q
 no switchport
 no ip address             
 channel-group 3 mode active  
!

interface Vlan1
 no ip address
 shutdown
!

router eigrp 1
 network 10.0.39.0 0.0.0.3  
 network 10.0.39.4 0.0.0.3  
 network 10.0.39.8 0.0.0.3
 no auto-summary
!

end
```

# SW0
```bash
enable
conf t
hostname SW0

ip routing

spanning-tree mode pvst

! Configurar VLANs
vlan 33
 name WEB_SERVERS
vlan 43
 name DHCP_SERVER

! Configurar puerto trunk 
interface FastEthernet0/1
switchport trunk encapsulation dot1q
 switchport mode trunk
 no ip address

! Configuración del Port-channel2 hacia SW1 (Fa0/2-5)
interface Port-channel2
switchport trunk encapsulation dot1q
 no switchport
 ip address 10.0.39.5 255.255.255.252
 no shutdown

! Configurar interfaces físicas para LACP (Fa0/2-5)
interface range FastEthernet0/2-5
switchport trunk encapsulation dot1q
 no switchport
 no ip address
 channel-group 2 mode active
 no shutdown

! Configurar SVIs
interface Vlan33
 ip address 192.168.100.1 255.255.255.128
 no shutdown

interface Vlan43
 ip address 192.168.100.129 255.255.255.128
 no shutdown

! Configuración EIGRP
router eigrp 1
 network 10.0.39.4 0.0.0.3    
 network 192.168.100.0 0.0.0.127
 network 192.168.100.128 0.0.0.127
 no auto-summary

end
```


# S0
```bash
enable
conf t
hostname S0

spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
 switchport access vlan 33
 switchport mode access
!
interface FastEthernet0/2
 switchport access vlan 43
 switchport mode access
!
interface FastEthernet0/3
 switchport mode trunk
!
interface FastEthernet0/4
 switchport mode trunk
!
interface FastEthernet0/5
 switchport mode trunk
!
interface FastEthernet0/6
 switchport mode trunk
!
interface FastEthernet0/7
 switchport mode trunk
!
interface FastEthernet0/8
 switchport mode trunk
!
interface FastEthernet0/9
 switchport mode trunk
!
interface FastEthernet0/10
 switchport mode trunk
!
interface FastEthernet0/11
 switchport mode trunk
!
interface FastEthernet0/12
 switchport mode trunk
!
interface FastEthernet0/13
 switchport mode trunk
!
interface FastEthernet0/14
 switchport mode trunk
!
interface FastEthernet0/15
 switchport mode trunk
!
interface FastEthernet0/16
 switchport mode trunk
!
interface FastEthernet0/17
 switchport mode trunk
!
interface FastEthernet0/18
 switchport mode trunk
!
interface FastEthernet0/19
 switchport mode trunk
!
interface FastEthernet0/20
 switchport mode trunk
!
interface FastEthernet0/21
 switchport mode trunk
!
interface FastEthernet0/22
 switchport mode trunk
!
interface FastEthernet0/23
 switchport mode trunk
!
interface FastEthernet0/24
 switchport mode trunk
!

interface Vlan1
 no ip address
 shutdown
!



```

