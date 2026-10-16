# homelab

personal infra
stack: proxmox · ansible · mikrotik · nginx · docker

---

## hardware node0

| component | spec |
| --- | --- |
| cpu | amd ryzen 5 5500 |
| ram | 32gb DDR4 3200mhz |
| storage | 512gb nvme + 4tb hdd |
| nic | rtl8125 2.5g (vmbr0), intel 82599es 2x10g sfp+, rtl8111 1g |

details --> [docs/proxmox/host.md](docs/proxmox/host.md)

---

## network topology

| segment | subnet | does |
| --- | --- | --- |
| vlan10 | 10.10.0.0/16 | servers, infra, trusted devices |
| vlan20 | 10.20.1.0/24 | neighbors |
| vlan30 | 10.30.1.0/24 | iot |
| vlan40 | 10.40.1.0/24 | "clearinet" |

| device | ip | does |
| --- | --- | --- |
| chr | 10.10.10.1 | vm 100 on node0, mikrotik chr, wan gpon |
| pve | 10.10.10.2 | node0, proxmox ve |
| ap | 10.10.10.3 | xiaomi ax3000t, openwrt, dumb ap |

ip formula: proxmox id `abc` → `10.10.a.bc`  
exceptions: `10.10.10.*`, legacy hosts in `10.10.100.x`, senko-hel

diagram, vlans, tunnel, port forwarding, reverse proxy --> [docs/network/overview.md](docs/network/overview.md)

---

## services

all guests run debian 13 (trixie), ssh on port 5542


### core

| id | hostname | type | ip | does |
| --- | --- | --- | --- | --- |
| 100 | chr | vm | 10.10.10.1 | router |
| 101 | pbs | lxc | 10.10.1.1 | proxmox backup server |
| 102 | adguard | lxc | 10.10.1.2 | dns |
| 103 | nginx | lxc | 10.10.1.3 | reverse proxy, certbot |
| 104 | 3x-ui | vm | 10.10.1.4 | xray, vpn to lan |
| 105 | docker | vm | 10.10.1.5 | main docker host |
| — | senko-hel | vps | 10.10.1.7 | vps in helsinki, fi, l2 tunnel to vlan10 |

### prod

| id | hostname | type | ip | does |
| --- | --- | --- | --- | --- |
| 202 | torrent | lxc | 10.10.2.2 | qbittorrent |

### legacy

| id | hostname | type | ip | does |
| --- | --- | --- | --- | --- |
| 108 | gateway | lxc | 10.10.100.8 | nginx proxy manager |
| 112 | obsidian-sync | vm | 10.10.100.12 | couchdb for obsidian livesync |

### templates

| id | hostname | type | ip | does |
| --- | --- | --- | --- | --- |
| 9000 | debian-13-template | vm | — | template for `create/vm.yml` |

### to be removed

not maintained, not documented further

| id | hostname | type | ip | does |
| --- | --- | --- | --- | --- |
| 106 | bridgeSenkoHel | vm | 10.10.1.6 | legacy tunnel |
| 203 | test-mc | vm | 10.10.2.3 | minecraft (itzg) |
| 204 | wings | vm | 10.10.2.4 | pelican wings |
| 404 | monitoring | lxc | 10.10.4.4 | — |
| 405 | proxy-p2g | lxc | 10.10.4.5 | — |
| 621 | 621 | vm | 10.10.6.21 | — |

guest resources --> [docs/proxmox/guests.md](docs/proxmox/guests.md)  
docker stacks --> [docs/services/docker.md](docs/services/docker.md)  
senko-hel --> [docs/services/senko-hel.md](docs/services/senko-hel.md)

---

## structure

``` structure
homelab/
  ansible/
    create/ # provision new vm/lxc/vps
    init/ # bootstrap and update any host
    services/ # deploy specific services
  docs/
    proxmox/ # host, guests, template specs
    services/ # per-host service notes
    network/ # topology, chr, ap
    decisions.md # why things are the way they are
```

---

decisions and history --> [decisions.md](docs/decisions.md)
