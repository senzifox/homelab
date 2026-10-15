# ap

xiaomi mi router ax3000t (mediatek filogic), openwrt 24.10.2  
10.10.10.3/16 in vlan10, gw and dns 10.10.10.1  
dumb ap mode: dhcp, firewall, routing on chr, ap only bridges wifi into vlans  

## ports

| port | vlan | does |
| --- | --- | --- |
| lan2 | 10, 20, 30, 40 tagged | trunk to chr `lan-ap`, 1g |
| lan3, lan4 | 10 untagged | access, unused |
| wan | — | unused |

## wifi

| radio | band | channel | ssid | vlan | security |
| --- | --- | --- | --- | --- | --- |
| radio1 | 5g | auto, he80 | main, hidden | 10 | wpa3 sae |
| radio1 + radio0 | 5g + 2.4g | | for neighbors | 20 | wpa2, 802.11r |
| radio0 | 2.4g | 11, he20 | for iot, hidden | 30 | wpa2 |
| radio1 | 5g | | finland clearinet | 40 | wpa2/wpa3 mixed |
