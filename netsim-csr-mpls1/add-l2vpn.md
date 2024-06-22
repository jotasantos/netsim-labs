RIGHT NOW: ANALYSING VPNV4 ANNOUNCEMENTS, OSPF EXTENDED COMMUNITY
NEXT : REST OF THE MPLS PARAMS
NEXT: LABEL ANALYSIS, L2VPN
NEXT: EVPN VXLAN

Certainly! Here's the corrected configuration for adding the L2VPN "bob" on top of the existing L3VPN setup, ensuring that MPLS is enabled on the PE nodes' interfaces facing the P nodes, not the CE devices.

### Additions for the PE Nodes (PE1, PE2, PE3)

1. **Create the VRF for L2VPN "bob".**

```shell
vrf definition bob
 rd 65000:<unique_value_per_pe>
 route-target export 65000:<unique_value_per_pe>
 route-target import 65000:<unique_value_per_pe>
```

2. **Enable MPLS on the interface connected to P nodes (core facing).**

```shell
interface <interface_to_p_node>
 mpls ip
```

3. **Add the necessary BGP configurations for the L2VPN.**

```shell
router bgp 65000
 address-family l2vpn evpn
  neighbor <peers> activate
  advertise-all-vni
 exit-address-family
```

4. **Configure the L2VPN EVPN.**

```shell
l2vpn evpn
 vni <vni_id>
  rd 65000:<unique_value_per_pe>
  route-target import 65000:<unique_value_per_pe>
  route-target export 65000:<unique_value_per_pe>
  vlan aware bundle <bundle_name>
   auto route-target
   rd auto
   route-target import auto
   route-target export auto
   interface <interface_to_ce>
  exit
 exit
```

### Additions for the P Node

Ensure MPLS is enabled on all interfaces facing the PE nodes.

```shell
mpls ldp
 interface <interface_name>
  mpls ip
```

### Additions for the CE Nodes (CE1, CE2, CE3)

Assuming the CE nodes need to participate in the L2VPN, configure VLANs and ensure they can connect to the PE nodes.

1. **Configure VLAN interfaces.**

```shell
interface <interface_to_pe>
 switchport mode trunk
 switchport trunk allowed vlan <vlan_id>
```

2. **Configure IP address on VLAN interfaces if needed.**

```shell
interface vlan <vlan_id>
 ip address <ip_address> <subnet_mask>
```

### Configuration Snippets

Here are specific configuration snippets for each node involved:

#### PE1

```shell
vrf definition bob
 rd 65000:1
 route-target export 65000:1
 route-target import 65000:1

interface GigabitEthernet0/0
 mpls ip

router bgp 65000
 address-family l2vpn evpn
  neighbor <PE2_IP> activate
  neighbor <PE3_IP> activate
  advertise-all-vni
 exit-address-family

l2vpn evpn
 vni 10001
  rd 65000:1
  route-target import 65000:1
  route-target export 65000:1
  vlan aware bundle bob_bundle
   auto route-target
   rd auto
   route-target import auto
   route-target export auto
   interface GigabitEthernet0/1
  exit
 exit
```

#### PE2

```shell
vrf definition bob
 rd 65000:2
 route-target export 65000:2
 route-target import 65000:2

interface GigabitEthernet0/0
 mpls ip

router bgp 65000
 address-family l2vpn evpn
  neighbor <PE1_IP> activate
  neighbor <PE3_IP> activate
  advertise-all-vni
 exit-address-family

l2vpn evpn
 vni 10002
  rd 65000:2
  route-target import 65000:2
  route-target export 65000:2
  vlan aware bundle bob_bundle
   auto route-target
   rd auto
   route-target import auto
   route-target export auto
   interface GigabitEthernet0/1
  exit
 exit
```

#### PE3

```shell
vrf definition bob
 rd 65000:3
 route-target export 65000:3
 route-target import 65000:3

interface GigabitEthernet0/0
 mpls ip

router bgp 65000
 address-family l2vpn evpn
  neighbor <PE1_IP> activate
  neighbor <PE2_IP> activate
  advertise-all-vni
 exit-address-family

l2vpn evpn
 vni 10003
  rd 65000:3
  route-target import 65000:3
  route-target export 65000:3
  vlan aware bundle bob_bundle
   auto route-target
   rd auto
   route-target import auto
   route-target export auto
   interface GigabitEthernet0/1
  exit
 exit
```

#### P Node

```shell
mpls ldp
 interface GigabitEthernet0/0
  mpls ip
 interface GigabitEthernet0/1
  mpls ip
 interface GigabitEthernet0/2
  mpls ip
 interface GigabitEthernet0/3
  mpls ip
```

### Summary

These configuration snippets should help you integrate the L2VPN "bob" into your existing L3VPN setup. Adjust IP addresses, RD, and RT values as necessary to fit your network design. Make sure to configure MPLS on the core-facing interfaces of the PE nodes, not the CE-facing interfaces.