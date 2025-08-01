## Теория
- https://zvlb.github.io/blog/virtualization/
- https://habr.com/ru/articles/657677/

## Практика
Для тех у кого железо тянет(8+ ГБ ОЗУ и 40ГБ+ свободного места) - развернуть PNETLab у себя через VMware
Если не тянет, то все тоже самое, но без создания qemu нод. Просто при выполнении LDAP перейти на наш сервер

- IOL
i86bi_LinuxL2-AdvEnterpriseK9-M_152_May_2018.bin - для свитчей доступа и агрегации
i86bi_LinuxL3-AdvEnterpriseK9-M2_157_3_May_2018.bin - для роутеров
https://github.com/ishare2-org/ishare2-cli
- QEMU
Подготовить образы:
- debian 12 2гб 2 ядра: сначала выполнить установку без gui и web сервера => сделать снапшот в отдельное устройство => установить вручную окружение xfce и по желанию xrdp для нативного(а значит более быстрого) подключения с хостовой Windows системы 
- Windows server 2019r2 standard с gui 2гб 2 ядра 32гб
https://wiki.yandex.ru/users/i.viktorov/pnetlab-dobavlenie-qemu-obrazov/
https://www.eve-ng.net/index.php/documentation/howtos/howto-create-own-windows-server-on-the-eve/
