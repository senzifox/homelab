# homelab

personal infra
stack: proxmox · ansible · nginx · docker

---

## hardware node0

| component | spec |
| --- | --- |
| cpu | amd ryzen 5 5500 |
| ram | 32gb DDR4 3200mhz |
| storage | 512gb nvme + 4tb hdd |

---

## network topology

| segment | subnet | does |
| --- | --- | --- |
| vlan10 | 10.10.0.0/16 | servers, infra, trusted devices |
| vlan20 | 10.20.1.0/24 | neighbors |
| vlan30 | 10.30.1.0/24 | iot |

ip formula: proxmox id `abc` → `10.10.a.bc`  

---

## services

### core

| hostname | type | ip | does |
| --- | --- | --- | --- |
| pbs | lxc | 10.10.1.1 | proxmox backup server |
| adguard | lxc | 10.10.1.2 | dns server |
| 3x-ui | vm | 10.10.1.4 | vpn to lan |
| docker-core | vm | 10.10.1.5 | main docker host |
| bridgeSenkoHel | vm | 10.10.1.6 | bridge to senko-hel |
| senko-hel | vps | 10.10.1.7 | vps in fi, helsinki |

### prod

| hostname | type | ip | does |
| --- | --- | --- | --- |
| torrent | lxc | 10.10.2.2 | qbittorrent |

---

## structure

``` structure
homelab/
  ansible/
    create/ # provision new vm/lxc/vps
    init/ # bootstrap and update any host
    services/ # deploy specific services
  docs/
    proxmox/ # template specs
    decisions.md # why things are the way they are
```

---

decisions and history --> [decisions.md](docs/decisions.md)
