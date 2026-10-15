# network

## physical

```
  isp (gpon)
      │
  ma5671a sfp stick
      │
  ┌───┴──────────────────────── pve ──────────────────────────────┐
  │ 82599es port0 ─┐                                              │
  │                ├─ passthrough ─> chr (vm 100)                 │
  │ rtl8111 ───────┘                  sfp1   wan                  │
  │                                   lan-ap trunk 10,20,30,40 ─┐ │
  │                                   lan-pve (virtio) ── vmbr0 │ │
  │ rtl8125 2.5g (nic1) ── vmbr0                                │ │
  └─────────────────────────────────────────────────────────────┼─┘
                                                                │
                                                     ap lan2 (1g, trunk)
                                                     ap lan3/lan4: access vlan10
```

all node0 guests are on `vmbr0` and reach chr via `lan-pve` (pvid 10)  
`nic1`: 2.5g link to main workstation

## segments

| vlan | subnet | gw | dhcp pool | does | internet |
| --- | --- | --- | --- | --- | --- |
| 10 | 10.10.0.0/16 | 10.10.10.1 | 10.10.10.20-250 | servers, infra, trusted | direct |
| 20 | 10.20.1.0/24 | 10.20.1.1 | .20-.250 | neighbors | direct, no lan access |
| 30 | 10.30.1.0/24 | 10.30.1.1 | .20-.250 | iot | direct, no lan access |
| 40 | 10.40.1.0/24 | 10.40.1.1 | .20-.250 | wifi "clearinet" | via senko-hel |

dns: chr (handed out via dhcp), forwards to adguard (10.10.1.2)

## devices

| device | ip | does | doc |
| --- | --- | --- | --- |
| chr | 10.10.10.1 | router, vm 100 on node0 | [chr.md](chr.md) |
| pve | 10.10.10.2 | proxmox, `vmbr0` | [host.md](../proxmox/host.md) |
| ap | 10.10.10.3 | xiaomi ax3000t, openwrt | [ap.md](ap.md) |

## senko-hel l2 tunnel

senko-hel is in vlan10 as 10.10.1.7

```
senko-hel                                   chr
awg0 10.200.0.2/30 ── "clearinet" :51820 ─> container 10.200.1.2
                                              └ veth-awg 10.200.1.1
vxlan0 (vni 100, :4789) ── over awg0 ──>    vxlan-hel ── br0, pvid 10
  └ 10.10.1.7/16
```

- senko-hel: "clearinet" config, vxlan up in `PostUp`
- chr: "clearinet" in container, config in `/pcie1/awg-conf`
- 51820/udp on chr forwarded to container only from senko-hel ip
- mtu: awg0 1416, vxlan0 1366, tcp mss 1280 both ways
- vlan40: policy routing on chr (`via-hel`) to 10.10.1.7, snat on senko-hel to second public ip
- 3x-ui (10.10.1.4) also snat on senko-hel

### legacy

- `wg-hel` on chr and `wg-msk` on senko-hel - old "clearinet" 10.201.0.0/30 on 51821, down on senko-hel
- vm 106 bridgeSenkoHel - old bridge, stopped, 51820 forward to it disabled

## port forwards (chr)

| port | to | does |
| --- | --- | --- |
| 80, 443/tcp | 10.10.1.3 | nginx |
| 4433/tcp | 10.10.1.4 | 3x-ui |
| 34983/tcp+udp | 10.10.2.2 | qbittorrent |
| 25565/tcp | 10.10.1.7 | minecraft, pelican node0 on senko-hel |
| 51820/udp | container | senko-hel tunnel |

disabled: 2022 - wings, 25565 - test-mc, 51820 - bridgeSenkoHel

## reverse proxy

| where | target | backend |
| --- | --- | --- |
| senko-hel | site | 127.0.0.1:3001 |
| senko-hel | grafana | 127.0.0.1:3000 |
| senko-hel | uptime-kuma | 127.0.0.1:3002 |
| senko-hel | victoriametrics | 127.0.0.1:8428 |
| senko-hel | pelican panel | 10.10.1.5:8080 |
| senko-hel | pelican node0 / node1 | 10.10.1.7:8080 / 10.10.2.4:8080 |
| senko-hel | 2 domains | 301 redirect |
| nginx (10.10.1.3) | site-bio | 10.10.1.5:3001 |
| nginx (10.10.1.3) | obsidian livesync | 10.10.100.12:5984 |
| nginx (10.10.1.3) | pelican node0 / node1 | 10.10.1.7:8080 / 10.10.2.4:8080 |
| gateway (10.10.100.8) | nginx proxy manager | legacy |

adguard (10.10.1.2) still has old nginx sites pointing to `192.168.1.x`
