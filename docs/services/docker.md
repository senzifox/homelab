# docker (vm 105)

hostname `docker`, `docker-core` in inventory  
10.10.1.5, 6 cores, 16g ram, 378g disk  
dns: adguard (10.10.1.2), ufw allows only ssh, docker-published ports bypass ufw

stacks in `/opt/docker-stack/<name>/`  
nextcloud cron: root crontab, `*/5` `docker exec -u www-data nextcloud-app php cron.php`

## running

| stack | containers | port | does |
| --- | --- | --- | --- |
| nextcloud | nextcloud-app, nextcloud-db (mariadb 10.6), nextcloud-redis | 9000 | nextcloud |
| pelican | pelican-panel-1 | 8080 | pelican panel |
| site-bio | next-app | 3001 | bio site, deploy via github actions (`/_work/docker.home/site-bio`) |
| mikrodash | mikrodash-1 | 3081 | chr dashboard |
| twitch-senzifox | twitch-channel-points-miner-v2 | 5000 | |
| twitch-snays1k | twitch-channel-points-miner-v2 | 5001 | |
| twitch-rixxieq | twitch-channel-points-miner-v2 | 5002 | |
| — | portainer (ce lts) | 8000, 9443 | portainer, agent on senko-hel |

dockerd metrics on 9323, node exporter on 9100

## broken / stopped

| stack | state |
| --- | --- |
| github-runner | restart loop |
| asf | exited 4 months ago |
| ww_bot | exited 7 months ago, moved to senko-hel |
| authelia | exited 7 months ago |
| dozzle | exited 7 months ago |
| homarr | exited 7 months ago |

leftover docker networks from removed stacks: gitea, qbittorrent, twitch, uptime-kuma
