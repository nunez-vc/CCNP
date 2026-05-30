# VRF Cheat Sheet
## 1. Create a VRF
```
1. Create a VRF
configure terminal
 vrf definition <VRF-NAME>
 address-family ipv4
 exit-address-family
 end

***For older devices use: ip vrf <VRF-NAME>
```

## 2. Assign VRF to an Interface
```
configure terminal
 interface <INTERFACE-ID>
  vrf forwarding <VRF-NAME>
  ip address <IP> <SUBNET MASK>
  no shutdown
  end
```

## 3. Assigning VRF to a Tunnel
```
configure terminal
 interface Tunnel<TUNNEL-NUMBER>
  vrf forwarding <VRF-NAME>
  ip address <IP> <SUBNET MASK>
  tunnel source <INTERFACE-ID>
  tunnel destination <IP>
  tunnel protection ipsec profile <PROFILE-NAME>
  no shutdown
  end

***For older devices use: ip vrf forwarding <VRF-NAME>
```

## 4. Configure a Static Route Inside a VRF
```
configure terminal
 ip route vrf <VRF-NAME> <DESTINATION NETWORK> <SUBNET MASK> <NEXT HOP IP>
```
