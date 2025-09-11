Выполнение практической части этого модуля тесно связано с модулем LDAP

Теория:
- Протоколы и способы взаимодействия с накопителями:SATA, SCSI и NVMe, в т.ч. в виртуальных(VHD, VMDK, qcow2, проброс) и сетевых средах(NFS, SMB/CIFS, FC)
- Типы файловых хранилищ: LVM, ZFS, кластерное хранение
- Виды RAID массивов
Возможные источники информации:
- https://habr.com/ru/companies/slurm/articles/517502/
- https://servermall.ru/blog/nas-san-i-das-chem-otlichayutsya-i-chto-vybrat/?srsltid=AfmBOorRW6Akj3ucLN0OPbMU35YJLeE8FHUazMTY70FvR7fr6FCt1Eau
- https://habr.com/ru/companies/slurm/articles/472776/

Практика:
- Развернуть NAS хранилище OpenMediaVault для дальнейшего использования в доменной инфраструктуре(рекомендовано заранее указать верные сетевые настройки из модуля LDAP)
- На той же машине, где развернут OMV, развернуть oxidized для бэкапа конфигов с устройств Mikrotik. можно ориентироваться на https://blog.ty-wu.com/posts/rosconfigbackup/ (да статья на китайском, но гугл переводчик в помощь, также можно воспользоваться документацией oxidized на github) либо https://interface31.ru/tech_it/2023/06/ustanavlivaem-i-nastraivaem-sistemu-upravleniya-konfiguraciyami-setevogo-oborudovaniya-oxidized.html

