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

! Configurar puerto trunk (sin cambios)
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

! Configurar SVIs (sin cambios)
interface Vlan33
 ip address 192.168.100.1 255.255.255.128
 no shutdown

interface Vlan43
 ip address 192.168.100.129 255.255.255.128
 no shutdown

! Configuración EIGRP (actualizada para Port-channel2)
router eigrp 1
 network 10.0.39.4 0.0.0.3    
 network 192.168.100.0 0.0.0.127
 network 192.168.100.128 0.0.0.127
 no auto-summary

end