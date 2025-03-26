enable
conf t
hostname SW2

ip routing

spanning-tree mode pvst

! Configuración del Port-channel L3 para Fa0/1-4
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