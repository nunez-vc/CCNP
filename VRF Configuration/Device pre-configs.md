# VRF Configuration Device Pre-configurations
```
- VRF allows one physical router or Layer 3 switch to have multiple separate routing tables.
- VRF is like creating multiple virtual routers inside one physical router.
```
## Switch 10
```
configure terminal
  hostname SW10
  no logging console
  no ip domain-lookup
  line vty 0 4
    exec-timeout 0 0
    exit
  line con 0
    exec-timeout 0 0
    exit
  vlan 100
    exit
  interface e0/0
    switchport trunk encapsulation dot1q
    switchport mode trunk
    no shutdown
    exit
  interface e0/1
    switchport mode access
    switchport access vlan 10
    no shutdown
    end
write
```

## Switch 20
```
configure terminal
  hostname SW20
  no logging console
  no ip domain-lookup
  line vty 0 4
    exec-timeout 0 0
    exit
  line con 0
    exec-timeout 0 0
    exit
  vlan 101
    exit
  interface e0/0
    switchport trunk encapsulation dot1q
    switchport mode trunk
    no shutdown
    exit
  interface e0/1
    switchport mode access
    switchport access vlan 11
    end
write
```

## ISP
```
configure terminal
  hostname ISP
  no logging console
  no ip domain-lookup
  line vty 0 4
    exec-timeout 0 0
    exit
  line con 0
    exec-timeout 0 0
    exit
  interface e0/1
    ip add 10.10.1.2 255.255.255.0
    ip ospf 100 area 0.0.0.0
    no shutdown
    exit
  interface e0/2
    ip add 10.10.2.2 255.255.255.0
    ip ospf 100 area 0.0.0.0
    no shutdown
    exit
  router ospf 100
    router-id 1.1.1.1
    end
write
```

## Router 10
```
configure terminal
  hostname R10
  no logging console
  no ip domain-lookup
  line vty 0 4
    exec-timeout 0 0
    exit
  line con 0
    exec-timeout 0 0
    exit
  ip vrf ENCOR
  interface e0/0
    no shutdown
    exit
  interface e0/0.10
    encapsulation dot1q 100
    ip vrf forwarding ENCOR
    ip add 10.10.1.1 255.255.255.0
    no shutdown
    exit
  interface e0/1
    ip add 10.10.1.1 255.255.255.0
    ip ospf 100 area 0.0.0.0
    no shutdown
    exit
  router ospf 100
    router-id 10.10.10.10
    exit
  crypto isakmp policy 10
    encryption aes
    hash md5
    authentication pre-share
    group 2
    exit
  crypto isakmp key cisco address 10.10.2.1
  crypto ipsec transform-set MYSET esp-aes esp-md5-hmac
    mode tunnel
    exit
  crypto ipsec profile MYPROFILE
  set transform-set MYSET
  interface tunnel0
    ip vrf forwarding ENCOR
    ip add 10.10.10.1 255.255.255.0
    tunnel source Ethernet0/1
    no shutdown
    end
write
```

## Router 20
```
configure terminal
  hostname R20
  no logging console
  no ip domain-lookup
  line vty 0 4
    exec-timeout 0 0
    exit
  line con 0
    exec-timeout 0 0
    exit
  ip vrf ENCOR
  interface e0/0
    no shut
  interface 0/0.11
    encapsulation dot1q 101
    ip vrf forwarding ENCOR
    ip add 10.11.2.1 255.255.255.0
    no shutdown
    exit
  interface 0/2
    ip add 10.10.2.1 255.255.255.0
    ip ospf 100 area 0.0.0.0
    no shutdown
    exit
  router ospf 100
    router-id 20.20.20.20
    exit
  crypto isakmp policy 10
    encryption aes
    hash md5
    authentication pre-share
    group 2
    exit
  crypto isakmp key cisco address 10.10.1.1
  crypto ipsec transform-set MYSET esp-aes esp-md5-hmac
    mode tunnel
    exit
  crypto ipsec profile MYPROFILE
  set transform-set MYSET
  interface tunnel0
    ip vrf forwarding ENCOR
    ip add 10.10.10.2 255.255.255.0
    tunnel source Ethernet0/2
    no shutdown
    end
write
```




























