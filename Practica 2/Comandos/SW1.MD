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

interface FastEthernet0/13
 no switchport
 ip address 10.0.39.21 255.255.255.252
 duplex auto
 speed auto
!

interface FastEthernet0/14
 no switchport
 ip address 10.0.39.17 255.255.255.252
 duplex auto
 speed auto
!

interface Vlan1
 no ip address
 shutdown
!

router eigrp 1
 network 10.0.39.0 0.0.0.3  
 network 10.0.39.4 0.0.0.3  
 network 10.0.39.8 0.0.0.3
 network 10.0.39.16 0.0.0.3
 network 10.0.39.20 0.0.0.3
 no auto-summary
!

end