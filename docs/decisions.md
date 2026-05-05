
# decisions

there will be a history of the decisions made here

## 2026.03.29

фулл переписал create/lxc.yml, теперь без костылей
community.general.proxmox устарел, переехал в community.proxmox
надо мигрировать create/lxc.yml и vm.yml когда руки дойдут

## 2026.04.12

написан create/vps.yml для машин хостящихся не у меня
обновлена структура inventory, теперь это несколько файлов ибо публичный репо, там айпи и тд
добавил комментарий в example_secrets.yml по примеру с inventories/example.private

## 2026.04.15

оказалось что все сломалось, inventory более не ворк, ссш на lxc тоже, когда же фиксить....

## 2026.04.16

починил переменные, оказалось просто переносом переменных
перенес lxc.yml на community.proxmox.proxmox
более правильно задал путь к secrets.yml в vps,vm,lxc.yml
ansible-lint по всем плейбукам пробежал, кайф

## 2026.05.05

ввёл vps в хельсинках (senko-hel) в основную vlan10 подсеть, l2 туннель (amneziawg, vxlan)
обновил README.md и inventory на текущее состояние
