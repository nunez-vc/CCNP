# Site-to-Site IPsec VPN Cheat Sheet
## IKEv2 Proposal
```
This defines the IKEv2 Phase 1 Parameters.

configure terminal
 crypto ikev2 proposal <IKEV2-PROPOSAL-NAME>
 encryption <ENCRYPTION-ALGORITHM>
 integrity <HASH-ALGORITHM>
 group <DH-GROUP>

encrpytion options:
des
3des
aes-cbc-128/192/256
aes-gcm-128/256

integrity options:
md5
sha1/256/384/512

group options:
1,2,5,14,15,16,19,20,21,24
```

## IKEv2 Policy
```
This tells the router which IKEv2 proposal to use.

configure terminal
 crypto ikev2 policy <IKEV2-POLICY-NAME>
  proposal <PROPOSAL-NAME>
```

## IKEv2 Keyring
```
This defines the remote peer and the shared authentication key.

configure terminal
 crypto ikev2 keyring <IKEV2-KEYRING-NAME>
  peer <PEER-NAME>
   address <REMOTE-PEER-WAN-IP>
   pre-shared-key local <PRE-SHARED-KEY>
   pre-shared-key remote <PRE-SHARED-KEY>
```

## IKVEv2 Profile
```
This binds the identity matching, authentication method and keyring together.

crypto ikev2 profile <IKEV2-PROFILE-NAME>
 match identity remote address <REMOTE-PEER-WAN-IP> 255.255.255.255
 authentication local pre-share
 authentication remote pre-share
 keyring local <KEYRING-NAME>
```

## IPsec Transform Set
```
This defines how IPsec encrypts and authenticates the actual user data.

crypto ipsec transform-set <TRANSFORM-SET-NAME> <ESP-ENCRYPTION> <ESP-HASH>
 mode tunnel
```

## IPsec Profile
```
This connects the IPsec transform set with the IKEv2 Profile.

crypto ipsec profile <IPSEC-PROFILE-NAME>
 set transform-set <TRANSFORM-SET-NAME>
 set ikev2-profile <IKEV2-PROFILE-NAME>
```

## Virtual Tunnel Interface 
```
This creates the route-based VPN tunnel and protects it using IPsec.

interface tunnnel<TUNNEL-NUMBER>
 ip address <IP> <SUBNET-MASK>
 tunnel source <LOCAL-WAN-INTERFACE-OR-IP>
 tunnel destination <REMOTE-PEER-WAN-IP>
 tunnel mode ipsec ipv4
 tunnel protection ipsec profile <IPSEC-PROFILE-NAME>
 ip mtu <MTU-SIZE>
 no shutdown
```

## Static routing over the VPN
```
ip route <DESTINATION-NETWORK> <SUBNET-MASK> Tunnel<TUNNEL-NUMBER>
```



