# node0

proxmox ve 9.2, kernel 7.0 (pve), debian 13 (trixie)  
10.10.10.2/16, gw 10.10.10.1 (chr)

## hardware

| component | spec |
| --- | --- |
| cpu | amd ryzen 5 5500, 6c/12t |
| ram | 32gb DDR4 3200mhz |
| nvme0n1 | colorful cn600 512gb, lvm `pve` (root 468g, swap 8g) |
| sda | seagate st4000dm004 4tb, ext4, `/mnt/pve/HDD` |

| pci | nic | used by |
| --- | --- | --- |
| 03:00.0 | intel 82599es 10g sfp+, port0 | passthrough to chr, wan |
| 03:00.1 | intel 82599es 10g sfp+, port1 | unused |
| 04:00.0 | realtek rtl8125 2.5g | `nic1` - `vmbr0`, link to main workstation |
| 05:00.0 | realtek rtl8111 1g | passthrough to chr, trunk to ap |

## network

one bridge `vmbr0` on `nic1`, not vlan-aware, all guests in `10.10.0.0/16`  
vlans live on chr --> [network/chr.md](../network/chr.md)  
cluster firewall disabled

## storage

| name | type | path | content |
| --- | --- | --- | --- |
| local | dir | `/var/lib/vz` (nvme) | guest disks, templates, iso, snippets |
| HDD | dir | `/mnt/pve/HDD/proxmox` | import |
| pbs_storage | pbs | 10.10.1.1, datastore `hdd` | backup |

### /mnt/pve/HDD

| dir | does |
| --- | --- |
| `proxmox/pbs_data` | pbs datastore, bind mount to lxc 101 |
| `shared/torrents` | downloads, bind mount to lxc 202 |
| `shared/docs`, `shared/Obsidian` | shared files |
| `backups` | laptop, steam, veeam etc |
| `docker-data` | old gitea and nextcloud data |
| `ownCloudMFG` | archive |

## backups

- vzdump job: all guests, daily 05:00, snapshot, to `pbs_storage`
- pbs: prune daily without keep rules, gc daily
- `keep-all=1` on `pbs_storage` and `HDD`

## shares

| protocol | share | path | access |
| --- | --- | --- | --- |
| smb | `HDD_Storage` | `/mnt/pve/HDD/` | senzi |
| smb | `media` | `/media/backup` | shareuser |
| nfs | — | `/mnt/pve/HDD` | 192.168.1.102, legacy |

## running on host

| port | service |
| --- | --- |
| 8006 | pve web ui |
| 9100 | prometheus-node-exporter |
| 9633 | smartctl-exporter |
| 5201 | iperf3 |
| 139, 445 | samba |
| 2049 | nfs |

## pve users

| user | does |
| --- | --- |
| root@pam | admin |
| ansible@pve | api token for `ansible/create/*` |
| api@pve | — |
