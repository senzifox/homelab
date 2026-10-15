# chr (vm 100)

mikrotik chr, routeros 7.23 stable, hostname `senzi-router`  
4 cores, 4g ram, disks 128m (sys) + 2g (`pcie1`, containers etc)  
license p-unlimited  

## interfaces

| name | hw | does |
| --- | --- | --- |
| sfp1 | intel 82599es port0, passthrough | wan, gpon stick ma5671a, dhcp-client; `192.168.1.2/24` for stick management |
| lan-ap | rtl8111 1g, passthrough | trunk to ap: 10, 20, 30, 40 tagged |
| lan-pve | virtio, `tap100i0` in `vmbr0` | pvid 10, untagged |
| br0 | bridge, vlan-filtering | lan-ap, lan-pve, vxlan-hel |
| vlan10..40 | on br0 | vlan gateways |
| veth-awg | veth | container "clearinet", 10.200.1.1/30 |
| vxlan-hel | vxlan vni 100 | l2 to senko-hel, in br0 with pvid 10 |
| wg-hel | "clearinet" | legacy, 10.201.0.1/30 |

## services

- dhcp on all vlans, lease 12h, dns = segment gw
- dns: `allow-remote-requests`, upstream adguard 10.10.1.2 (lxc 102 on node0)
- container: `"clearinet"-mikrotik`, root `/pcie1/awg`, start-on-boot

## firewall

input:
- established/related
- all from vlan10
- dhcp, dns, icmp from all vlans
- 51821/udp from wan (wg-hel)
- vxlan 4789/udp from "clearinet" container
- drop the rest

forward:
- established/related, invalid drop
- vlan10 - anywhere
- vlan20, vlan30, vlan40 - wan only
- dstnat (port forwards)
- "clearinet" container - wan
- vlan40 - anywhere (`via-hel`)
- drop the rest

nat:
- masquerade on wan
- hairpin nat for 10.10.0.0/16
- masquerade "clearinet" container

mangle: vlan40 traffic to dst outside `10.0.0.0/8` - routing mark `via-hel`, default route 10.10.1.7

port forwards --> [overview.md](overview.md#port-forwards-chr)
