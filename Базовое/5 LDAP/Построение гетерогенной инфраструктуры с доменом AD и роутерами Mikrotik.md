# Теория

- https://habr.com/ru/articles/538662/ - обзорная статься по протоколу LDAP
- https://habr.com/ru/companies/astralinux/articles/866974/ - для формирования общего понимания отличий административных параметров в Windows и Linux системах
- https://www.aldpro.ru/professional/ALD_Pro_Module_05/Domain_performance.html - про службу sssd
- https://habr.com/ru/articles/718632/ - Ввод Linux в домен AD + kerberos
- https://interface31.ru/tech_it/2015/03/autentifikaciya-v-sistemah-windows-chast-1-ntlm.html - устаревший протокол NTLM, но все еще относительно широко используемый
- [[LDAP и Kerberos часть 1]] - Приложенный конспект лекции Кетова
- https://docs.altlinux.org/ru-RU/alt-domain/11.0/html/alt-domain/sssd-winbind.html - документация к Альт домену может прояснить непонятные моменты по Samba

# Практика

    Цели:
    1. Реализовать конфигурацию Router-on-stick с использованием образов Mikrotik RouterOS 6 версии.
    2. Интегрировать Linux машину в домен AD
    3. Реализовать воссоздание рабочего окружения пользователя, учетная запись которого находится в каталоге AD

В одном влане: 1 debian клиент - для проверки автоматического создания рабочего окружения - домашняя папка пользователя, существующего в каталоге AD + share папка

В другом влане: 1 NAS smb debian сервер для хранения шары и домашних папок, и 1 windows server AD DC
![|366x414](Интеграция%20Linux%20в%20домен%20заметки-01.08.2025-12_08.png)

# Заметки по практике

После установки AD оснастки, необходимо повысить сервер до контроллера домена

Был обнаружен баг либо недостаток, что если debian сервер не имеет сетевую связность с AD DC, то отсутствует возможность войти в какую либо учетку. Вероятно из за плотного вмешательства kerberos в модуль PAM. Деффект устраняется при отключении адаптера(на программном уровне достаточно), через который сервер связывается с AD DC. Объяснение:
Ответчик PAM работает немного иначе. Локальный кэш будет использоваться только в том случае, если служба находится в режиме офлайн. А если связь с сервером есть, то при входе пользователя в систему Ответчик запросит проверку аутентичности у контроллера и обновит информацию об участии пользователя в группах, даже если срок действия кэша еще не истек. В качестве примеров клиентских приложений, использующих PAM, можно привести `login`, `su` и `sshd`.

## Настройка Mikrotik'ов

образ можно легко добавить при помощи утили ishare2. Желательно использовать последний релиз 6-ой версии, так как у нее гораздо меньшее потребление ресурсов, но при этом достаточный для выполнения задания функционал

- https://wiki.merionet.ru/articles/komandnaya-stroka-mikrotik - краткий обзор по работе в терминале mikrotik

Создание vlan на интерфейсе роутера

```shell
[admin@MikroTik] > interface vlan
[admin@MikroTik] /interface vlan> add comment="client" interface=ether2 name="VLAN 10" vlan-id=10
[admin@MikroTik] /interface vlan> add comment="server" interface=ether2 name="VLAN 20" vlan-id=20
[admin@MikroTik] /interface vlan> / ip address
[admin@MikroTik] /ip address> add address=172.16.1.1/24 comment="client GW" interface="VLAN 10"
[admin@MikroTik] /ip address> add address=172.16.2.1/24 comment="server GW" interface="VLAN 20"
```

Перевод всех интерфейсов в один бридж для имитации деятельности коммутатора

```shell
[admin@MikroTik] > interface bridge add name=bridge1 vlan-filtering=yes
[admin@MikroTik] > interface bridge port
[admin@MikroTik] /interface bridge port> add bridge=bridge1 interface=ether2
[admin@MikroTik] /interface bridge port> add bridge=bridge1 interface=ether3
[admin@MikroTik] /interface bridge port> add bridge=bridge1 interface=ether4
[admin@MikroTik] /interface bridge port> add bridge=bridge1 interface=ether5
```

# Полезные ресурсы

https://www.youtube.com/watch?v=S7QOkcznEVY&ab_channel=SpecialistTV - многие действия из этого видео можно повторить для успешного выполнения задания

https://wikival.bmstu.ru/doku.php?id=%D1%80%D0%B0%D0%B7%D0%B2%D0%B5%D1%80%D1%82%D1%8B%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5_active_directory - дублирую ссылку из описания под видео

Ресурсы, которые могут пригодиться во время настройки mikrotik:

- https://mikrotik.moscow/blog/nastroyki/nat-v-routeros/ - NAT
- https://www.manitonetworks.com/mikrotik/2016/3/5/vlan-trunking - создание VLAN интерфейсов на роутере
- https://help.mikrotik.com/docs/spaces/ROS/pages/328068/Bridging+and+Switching - перевод роутера фактически в режим коммутации
- https://www.youtube.com/watch?v=n_XU_mvxfLM&ab_channel=KalTek - разбор конфигурации router on stick
