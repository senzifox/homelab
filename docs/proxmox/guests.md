# guests

all disks on `local` (nvme), all guests in `vmbr0`

## vm

| id | name | cores | ram | disk | onboot | status | notes |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 100 | chr | 4 | 4g | 128m + 2g | yes | running | q35, passthrough 03:00.0, 05:00.0 |
| 104 | 3x-ui.home | 1 | 1g | 3g | yes | running | cloud-init |
| 105 | docker.home | 6 | 16g (balloon 4g) | 378g | yes | running | ovmf, pve firewall on net0 |
| 106 | bridgeSenkoHel | 1 | 512m | 10g | no | stopped | cloud-init, legacy |
| 112 | obsidian-sync | 2 | 4g | 10g | yes | running | linked clone of 9000 |
| 203 | test-mc.home | 4 | 8g | 42g | yes | running | cloud-init, legacy |
| 204 | wings.home | 12 | 16g | 42g | yes | running | cloud-init, legacy |
| 621 | 621 | 3 | 4g | 4g | no | stopped | cloud-init |
| 9000 | debian-13-template | 2 | 2g | 3g | — | template | --> [main_template_spec.md](main_template_spec.md) |

## lxc

all unprivileged, from `debian-13-standard_13.1-2`

| id | hostname | cores | ram | swap | disk | onboot | status | mounts / features |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 101 | pbs.home | 2 | 4g | 512m | 8g | yes | running | nesting, `HDD/proxmox/pbs_data` - `/mnt/pbs` |
| 102 | adguard.home | 1 | 1g | 0 | 8g | yes | running | nesting |
| 103 | nginx.home | 1 | 512m | 512m | 2g | yes | running | pve firewall on net0 |
| 108 | gateway.home | 1 | 2g | 512m | 8g | yes | running | nesting, keyctl (docker) |
| 202 | torrent.home | 1 | 2g | 0 | 8g | yes | running | nesting, `HDD/shared/torrents` - `/downloads`, startup up=30 |
| 404 | monitoring.home | 2 | 2g | 0 | 20g | no | stopped | nesting, gw `10.10.19.1` |
| 405 | proxy-p2g | 1 | 512m | 512m | 2g | no | stopped | — |

## per guest

| host | inside | ports |
| --- | --- | --- |
| pbs | proxmox backup server, datastore `hdd` | 8007, 9100 |
| adguard | adguard home, nginx-ui, legacy nginx, exporters (adguard, nginx, node) | 53, 3000, 9000, 80/443/8080, 9100/9113/9618 |
| nginx | nginx + certbot (cloudflare dns) | 443 |
| 3x-ui | x-ui + xray | 8080 (panel), 4433 |
| docker | --> [services/docker.md](../services/docker.md) | — |
| torrent | qbittorrent-nox, node exporter | 8080 (web), 34983 (peers), 9100 |
| test-mc | itzg/minecraft-server in docker, `/opt/docker-stack/test-mc` | 25565 |
| wings | pelican wings, node1 | 8080, 2022 |
| gateway | nginx proxy manager in docker | 80, 81, 443 |
| obsidian-sync | couchdb in docker, `/opt/obsidian-sync` | 5984 |
