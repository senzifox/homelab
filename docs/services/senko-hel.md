# senko-hel

vps in fi, helsinki, debian 13 (trixie), 2 cores, 4g ram, 79g disk  
public ip in [inventories/private.ini](../../ansible/inventories/example_private.ini), 10.10.1.7 in lan  
how it joins lan --> [network/overview.md](../network/overview.md#senko-hel-l2-tunnel)

## system

| service | does |
| --- | --- |
| awg0 | "clearinet" tunnel home + vxlan0 |
| nginx + certbot (cloudflare dns) | reverse proxy on 80/443 |
| wings | pelican wings, node0 (8080, 2022) |
| actions.runner.senzifox-site | self-hosted runner for site |
| actions.runner.senzifox-senzi-bot | self-hosted runner for bot |
| vnstat | traffic accounting |

## docker

| stack | containers | port | path |
| --- | --- | --- | --- |
| mailserver | docker-mailserver | 25, 143, 465, 587, 993, 995 | `/opt/docker-stack/mailserver` |
| monitoring | grafana, victoriametrics, vmagent, uptime-kuma, node-exporter | 127.0.0.1: 3000, 8428, 3002 | `/opt/docker-stack/monitoring` |
| site | site | 127.0.0.1:3001 | deployed by runner |
| senzi-bot | bot, worker, telegram-bot-api, redis | — | deployed by runner |
| — | ww_bot | — | |
| — | portainer agent | 2443 (only from 10.10.1.5) | |

reverse proxy --> [network/overview.md](../network/overview.md#reverse-proxy)

## ufw

public: 80/443, mail (25, 587, 993, 995), 8080, 25565, 24454/udp, 5201, 5203, 12345  
lan only: 2022, 8080  
vxlan 4789/udp - only from 10.200.1.1
