## Phase 1: Enable Basic OSPF Routing
### R4: PE-SanJuan
```
configure terminal
 no router ospf 1
 router ospf 1
  network 172.16.0.0 0.0.255.255 area 0
  network 10.1.1.8 0.0.0.3 area 0
```
### R3: PCore-SanJuan
```
configure terminal
 no router ospf 1
 router ospf 1
  network 172.16.0.0 0.0.255.255 area 0
  network 10.1.1.8 0.0.0.3 area 0
  network 10.1.1.4 0.0.0.3 area 0
````
### R2: PCore-Makati
```
configure terminal
 no router ospf 1
 router ospf 1
  network 172.16.0.0 0.0.255.255 area 0
  network 10.1.1.0 0.0.0.3 area 0
  network 10.1.1.4 0.0.0.3 area 0
```
### R1: PE-Makati 
```
configure terminal
 no router ospf 1
 router ospf 1
  network 172.16.0.0 0.0.255.255 area 0
  network 10.1.1.0 0.0.0.3 area 0
```

## Phase 2: Basic MPLS Configuration
```
MPLS (Multiprotocol Label Switching) is a high performance packet forwarding technology that uses labels instead of traditional IP routing lookups.
```
### R4: PE-San Juan
```
configure terminal
 ip cef
 mpls ip
  interface Ethernet 1/2
  mpls ip
  mpls label protocol ldp
  mpls mtu override 1512
```
### R3: PCore-SanJuan
```
configure terminal
 ip cef  
 mpls ip
  interface Ethernet 1/1
   mpls ip
   mpls label protocol ldp
   !mpls mtu override 1512
   exit
  interface Ethernet 1/2
   mpls ip
   mpls label protocol ldp
   mpls mtu override 1512
 ```
### R2: PCore-Makati
```
configure terminal
 ip cef
 mpls ip
  interface Ethernet 1/1
   mpls ip
   mpls label protocol ldp
   !mpls mtu override 1512
  interface Ethernet 1/2
   mpls ip
   mpls label protocol ldp
   mpls mtu override 1512
```
### R1: PE-Makati
```
configure terminal
 ip cef
 mpls ip
  interface Ethernet 1/0
   mpls ip
   mpls label protocol ldp
   mpls mtu override 1512
```
### How to verify:
```
R2#show mpls ldp neighbor (lib)
    Peer LDP Ident: 172.16.1.1:0; Local LDP Ident 172.16.1.2:0
        TCP connection: 172.16.1.1.646 - 172.16.1.2.23066
        State: Oper; Msgs sent/rcvd: 8/8; Downstream
        Up time: 00:00:53
        LDP discovery sources:
          FastEthernet1/1, Src IP addr: 192.168.1.1
        Addresses bound to peer LDP Ident:
          192.168.1.1     172.16.1.1      
    Peer LDP Ident: 172.16.1.3:0; Local LDP Ident 172.16.1.2:0
        TCP connection: 172.16.1.3.41657 - 172.16.1.2.646
        State: Oper; Msgs sent/rcvd: 8/8; Downstream
        Up time: 00:00:40
        LDP discovery sources:
          FastEthernet1/2, Src IP addr: 192.168.1.6
        Addresses bound to peer LDP Ident:
          192.168.1.6     172.16.1.3      

R2#show mpls forwarding-table (fib)
Local  Outgoing    Prefix            Bytes tag  Outgoing   Next Hop    
tag    tag or VC   or Tunnel Id      switched   interface              
16     Pop tag     172.16.1.1/32     0          Fa1/1      192.168.1.1  
17     Pop tag     172.16.1.3/32     0          Fa1/2      192.168.1.6  
SW2P#

R2#show mpls ldp bindings (lfib)
  tib entry: 172.16.1.1/32, rev 2
        local binding:  tag: 16
        remote binding: tsr: 172.16.1.1:0, tag: imp-null
        remote binding: tsr: 172.16.1.3:0, tag: 16
  tib entry: 172.16.1.2/32, rev 6
        local binding:  tag: imp-null
        remote binding: tsr: 172.16.1.1:0, tag: 17
        remote binding: tsr: 172.16.1.3:0, tag: 17
  tib entry: 172.16.1.3/32, rev 4
        local binding:  tag: 17
        remote binding: tsr: 172.16.1.1:0, tag: 16
        remote binding: tsr: 172.16.1.3:0, tag: imp-null
  tib entry: 192.168.1.0/30, rev 8
        local binding:  tag: imp-null
        remote binding: tsr: 172.16.1.1:0, tag: imp-null
        remote binding: tsr: 172.16.1.3:0, tag: 18

R2#show ip route (routing table)
     172.16.0.0/32 is subnetted, 3 subnets
O       172.16.1.1 [110/2] via 192.168.1.1, 00:15:15, FastEthernet1/1
O       172.16.1.3 [110/2] via 192.168.1.6, 00:15:15, FastEthernet1/2
C       172.16.1.2 is directly connected, Loopback0
     192.168.1.0/30 is subnetted, 2 subnets
C       192.168.1.0 is directly connected, FastEthernet1/1
C       192.168.1.4 is directly connected, FastEthernet1/2
```
## Phase 3: VRF Configuration
```
Virtual Router Forwarder: "Virtual/Logical Router" allows you to have overlapping VPN networks.
RD (Route Distinguisher) - makes routes unique inside MPLS VPN.
RT (Route-target) - controls route import/exoirt between VRFs.
```
### R4: PE-SanJuan
```
configure terminal
 ip vrf clientBDOsanjuan
  rd 999:1
  route-target 64999:1
 ip vrf clientBPIsanjuan
  rd 999:2
  route-target 64999:2
 ip vrf clientPNBsanjuan
  rd 999:3
  route-target 64999:3
 ip vrf clientBPOsanjuan
  rd 888:4
  route-target 69666:4
 ip vrf clientBSPsanjuan
  rd 888:5
  route-target 69666:5
```
### R1: PE-Makati
```
configure terminal
 ip vrf clientBDOmakati
  rd 999:1
  route-target 64999:1
 ip vrf clientBPImakati
  rd 999:2
  route-target 64999:2
 ip vrf clientPNBmakati
  rd 999:3
  route-target 64999:3
 ip vrf clientBPOmakati
  rd 888:4
  route-target 69666:4
 ip vrf clientBSPmakati
  rd 888:5
  route-target 69666:5
```
### How to verify:
```
R4#show ip vrf
  Name                             Default RD          Interfaces
  clientBDOsanjuan                 999:1               
  clientBPIsanjuan                 999:2               
  clientPNBsanjuan                 999:3               

R1#show ip vrf
  Name                             Default RD          Interfaces
  clientBDOmakati                  999:1               
  clientBPImakati                  999:2               
  clientPNBmakati                  999:3    
```

## Phase 4: Configuring VRF interfaces on Provider Edge Routers
### R4: PE-SanJuan
```
configure terminal
 interface Ethernet1/1
   ip vrf forwarding clientBPIsanjuan
   ip address 10.1.1.2 255.255.255.252
   no shut
 interface Ethernet1/0
   ip vrf forwarding clientPNBsanjuan
   ip address 10.1.1.2 255.255.255.252
   no shut
 interface Ethernet0/1
   ip vrf forwarding clientBSPsanjuan
   ip address 10.1.1.2 255.255.255.252
   no shut
 interface Ethernet0/0
   ip vrf forwarding clientBPOsanjuan
   ip address 10.1.1.2 255.255.255.252
   no shut
end
```
### R1: PE-Makati
```
configure terminal
 interface Ethernet1/2
   ip vrf forwarding clientBPImakati
   ip address 10.1.1.6 255.255.255.252
   no shut
 interface Ethernet1/1
   ip vrf forwarding clientPNBmakati
   ip address 10.1.1.6 255.255.255.252
   no shut
 interface Ethernet0/1
   ip vrf forwarding clientBSPmakati
   ip address 10.1.1.6 255.255.255.252
   no shut
 interface Ethernet0/0
   ip vrf forwarding clientBPOmakati
   ip address 10.1.1.6 255.255.255.252
   no shut
end
```
### How to verify:
```
R1#sh ip int br
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet1/0                10.1.1.1        YES NVRAM  up                    up      
Ethernet1/1                10.1.1.6        YES manual up                    up      
Ethernet1/2                10.1.1.6        YES manual up                    up      
Ethernet1/3                209.9.9.1       YES NVRAM  up                    up      
Loopback0                  172.16.1.1      YES manual up                    up   

Note: Make sure Real routers, can ping Virtual Routers!
```

## Phase 5: MP-BGP Configuration Multiprotocol BGP
```
BGP (handles 32bits:normal packet) vs mBGP(multi Protocol BGP = 96bits= 32 + 64bits RD)
```
### R4: PE-SanJuan
```
configure terminal
 no router bgp 64999
 router bgp 64999
  !kill normal BGP, enable mBGP
  no bgp default ipv4-unicast
  neighbor 172.16.1.1 remote-as 64999
  neighbor 172.16.1.1 update-source lo0
  address-family vpnv4
  neighbor 172.16.1.1 activate
  neighbor 172.16.1.1 send-community both
  exit-address-family
  !activate the neighbor and exchange vpnv4 routes (32+64)
```
### R1: PE-Makati peering with lo of R2
```
configure terminal
 no router bgp 64999
 router bgp 64999
 no bgp default ipv4-unicast
 !disables IPv4 routing to handle VPN routes
 neighbor 172.16.1.4 remote-as 64999
 neighbor 172.16.1.4 update-source lo0
 address-family vpnv4
 neighbor 172.16.1.4 activate
 neighbor 172.16.1.4 send-community both
 exit-address-family
 !activate the neighbor and exchange vpnv4 routes (32+64)
```
### How to verify:
```
show ip bgp neighbors 172.16.1.3
PE-A#show ip bgp vpnv4 all summary 
For address family: VPNv4 Unicast
BGP router identifier 172.16.1.4, local AS number 64999
BGP table version is 7, main routing table version 7
4 network entries using 624 bytes of memory
4 path entries using 336 bytes of memory
4/4 BGP path/bestpath attribute entries using 672 bytes of memory
4 BGP extended community entries using 580 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 2212 total bytes of memory
BGP activity 4/0 prefixes, 4/0 paths, scan interval 60 secs

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
172.16.1.1      4        64999      18      18        7    0    0 00:13:11        2
```

## Phase 6: Individual Virtual/Logical Router VRF Configuration
### R4: PE-SanJuan
```
Note: Logical router of PNB = clientPNBSanJuan/Makati

configure terminal
 router eigrp 90
  address-family ipv4 vrf clientPNBsanjuan
  autonomous-system 90
  network 10.0.0.0
  no auto-summary
  end
```
### R1: PE-Makati
```
configure terminal
 router eigrp 90
  address-family ipv4 vrf clientPNBmakati
  autonomous-system 90
  network 10.0.0.0
  no auto-summary
  end
```
### R4: PE-SanJuan
```
Note: Logical Router of BPI = clientBPIsanjuan/makati

configure terminal
 router ospf 69 vrf clientBPIsanjuan
 network 0.0.0.0 0.0.0.0 area 0
 end
```
### R1: PE-Makati
```
configure terminal
 router ospf 69 vrf clientBPImakati
 network 0.0.0.0 0.0.0.0 area 0
 end
```

## Phase 7: Route Redistributions
```
RIP - mBGP-RIP/EIRGP-mBGP - EIGRP/OSPF - mBGP - OSPF
```
## Phase 7.1: EIGRP - mBGP - EIGRP
### R4: PE-SanJuan
```
configure terminal
 router bgp 64999
  address-family ipv4 vrp clientPNBsanjuan
  redistribute eigrp 90 metric 1
  exit
 router eigrp 90
  address-family ipv4 vrf clientPNBsanjuan
  autonomous-system 90
  network 10.0.0.0
  no auto-summary
  redistribute bgp 64999 metric 100000 10 1 255 1512
  !seed metric, sh int fa1/2 ; bw de/10 load rely mtu
  exit-address-family
```
### R1: PE-Makati
```
configure terminal
 router bgp 64999
  address-family ipv4 vrf clientPNBmakati
  redistribute eigrp 90 metric 1
  exit
 router eigrp 90
  address-family ipv4 vrf clientPNBmakati
  autonomous-system 90
  network 10.0.0.0
  no auto-summary
  redistribute bgp 64999 metric 100000 10 1 255 1512
  exit-address-family
```
## Phase 7.2: OSPF - mBGP - OSPF
### R4: PE-SanJuan
```
configure terminal
 router bgp 64999
  address-family ipv4 vrf clientBPIsanjuan
  no redistribute ospf 1
  redistribute ospf 69
  no auto-summary
  exit
 router bgp 64999
  redistribute bgp 64999 metric-type 1 subnets
  network 0.0.0.0 0.0.0.0 area 0
  end
```
### P1: PE-Makati
```
configure terminal
 router bgp 64999
  address-family ipv4 vrf clientBPImakati
  no redistribute ospf 1
  redistribute ospf 69
  no auto-sumary
  exit
 router ospf 69 vrf clientBPImakati
  redistribute bgp 64999 metric-type 1 subnets
  network 0.0.0.0 0.0.0.0 area 0
  end
```

## Phase 8: IGP Configuration on Clients
### On all PNB (EIGRP) Routers:
```
configure terminal
 router eigrp 90
  network 10.0.0.0
  no auto-summary
```
### On all BPI (OSPF) Routers:
```
configure terminal
 no router ospf 1
 router ospf 69
  network 0.0.0.0 0.0.0.0 area 0
```

## Phase 9: VPNs on Clients
```
Task: Create a site to site ipsec VPN tunnel between siteA with WAN of 10.1.1.1/30 and LAN A one of 172.16.10.1/24 and siteB with WAN of 10.1.1.5/30 and LAN B of 172.16.20.1/24.

IKE Phase 1 – Routers exchange keys securely (like a private handshake).
IKE Phase 2 – They agree on how to encrypt real data (the “data tunnel”).
Crypto Map – Tells router which traffic to encrypt (based on ACL).
IPsec SA (Security Association) – The live, active tunnel where packets get encrypted.
Encapsulation – Each packet from LAN A → LAN B gets wrapped (encrypted) before it leaves the WAN.
```
### PNB-SanJuan
```
configure terminal
 hostname PNBsanjuan
 interface Ethernet1/1
  description WAN to SiteB
  ip address 10.1.1.1 255.255.255.252
  no shutdown
  exit
 interface Ethernet0/1
  description LAN A
  ip address 172.16.10.1 255.255.255.0
  no shutdown
  exit
 ip route 0.0.0.0 0.0.0.0 10.1.1.2  --> Default route to internet
 access-list 110 permit ip 171.16.10.0 0.0.0.255 172.16.20.0 0.0.0.255
 crypto isakmp policy 10
  encrpytion aes
  hash sha256
  authentication pre-share
  group 14
  lifetime 86400
 crypto isakmp key VPN123 address 10.1.1.5
 crypto ipsec transform-set MYSET esp-aes esp-sha-hmac
  mode tunnel
 crypto map VPN-MAP 10 ipsec-isakmp
  set peer 10.1.1.5
  set transform-set MYSET
  match address 110
  exit
 interface Ethernet1/1
  crypto map VPN-MAP
  exit
 ip route 172.16.20.0 255.255.255.0 10.1.1.5
 end
```
### How to verify:
```
PNBsanjuan#show crypto isakmp sa
IPv4 Crypto ISAKMP SA
dst             src             state          conn-id status
10.1.1.5        10.1.1.1        QM_IDLE           1001 ACTIVE
```
### PNB-Makati
```
configure terminal
 hostname PNBmakati
 interface Ethernet1/3
  description WAN to SiteA
  ip address 10.1.1.5  255.255.255.252
  no shutdown
  exit
 interface Ethernet1/0
  description to LAN B
  ip address 172.16.20.1 255.255.255.0
  no shutdown
  exit
 ip route 0.0.0.0 0.0.0.0 10.1.1.6 --> Default route to internet
 access-list 110 permit ip 172.16.20.0 0.0.0.255 172.16.10.0 0.0.0.255
 crypto isakmp policy 10
  encrpytion aes
  hash sha256
  authentication pre-share
  group 14
  lifetime 86400
 crypto isakmp key VPN123 address 10.1.1.1
 crypto ipsec transform-set MYSET esp-aes esp-sha-hmac
  mode tunnel
 crypto map VPAN-MAP 10 ipsec-isakmp
  set peer 10.1.1.1
  set transform-set MYSET
  match address 110
 interface Ethernet 1/3
  crypto map VPN-MAP
 ip route 172.16.10.0 255.255.255.0 10.1.1.1
 end
```
### How to Verify:
```
PNBmakati#show crypto isakmp sa
IPv4 Crypto ISAKMP SA
dst             src             state          conn-id status
10.1.1.5        10.1.1.1        QM_IDLE           1001 ACTIVE
```
## GRE site-to-site VPN setup
```
Classic CCNP-level “GRE over IPsec” site-to-site VPN setup. It combines GRE (Generic Routing Encapsulation) and IPsec encryption so that: You get routing protocols + multiprotocol support via GRE, and encryption/authentication via IPsec:
```
### BPI-SanJuan
```
configure terminal
 hostname BPIsanjuan
 description WAN to Makati
 interface Ethernet1/1
  ip address 10.1.1.1 255.255.255.252
  no shutdown
  exit
 interface Ethernet1/0
  description LAN A
  ip address 172.16.10.1 255.255.255.0
  no shutdown
  exit
 ip route 0.0.0.0 0.0.0.0 10.1.1.2 --> Default route to Internet
 end
```
### Configure GRE Tunnel in BPI-SanJuan
```
configure terminal
 interface tunnel 10
  description GRE Tunnel to Makati
  ip address 192.168.100.1 255.255.255.252
  ip mtu 1400
  tunnel source 10.1.1.1
  tunnel destination 10.1.1.5
  keepalive 10 3
  no shutdown
  exit
 access-list 110 permit gre host 10.1.1.1 host 10.1.1.5  
```
### Configure ISAKMP (IKE Phase 1) in BPI-SanJuan
```
configure terminal
 crypto isakmp policy 10
  encryption aes
  hash sha256
  authentication pre-share
  group 14
  lifetime 86400
 crypto isakmp key GREVPN123 address 10.1.1.5
 end
 ```
### Define the IPsec Transform Set (Phase 2) in BPI-SanJuan
```
configure terminal
 crypto ipsec transform-set GRE-SET esp-aes esp-sha-hmac
  mode transport
 crypto map GRE-MAP 10 ipsec-isakmp
  set peer 10.1.1.5
  set transform-set GRE-SET
  match address 110
 interface Ethernet1/1
  crypto map GRE-MAP
  exit
!Now, GRE traffic between 10.1.1.1 and 10.1.1.5 will be encrypted by IPsec.
!Do static routing or dynamic routing protocols like OSPF, but for this one we will use static routing for simplicity
 ip route 172.16.20.0 255.255.255.0 192.168.100.2
```

### BPI-Makati
```
configure terminal
 hostname BPImakati
 interface Ethernet1/3
  description WAN to San Juan
  ip address 10.1.1.5 255.255.255.252
  no shutdown
  exit
 interface Ethernet1/0
  description LAN B
  ip address 172.16.20.1 255.255.255.0
  no shutdown
  exit
 ip route 0.0.0.0 0.0.0.0 10.1.1.6 --> Default route to Internet
```









