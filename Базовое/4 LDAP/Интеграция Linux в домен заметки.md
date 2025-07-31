Tags: [[стажировка ИТ протех]] 
**Вкратце:**
	
# Теория
	Цели занятия:
	1. Реализовать конфигурацию Router-on-stick с использованием образов Mikrotik RouterOS 6 версии.
	2. Интегрировать Linux машину в домен AD
	3. Реализовать воссоздание рабочего окружения залогинившегося на клиентской машине пользователя из AD

В одном влане: 1 debian клиент  - для проверки автоматического создания рабочего окружения - домашняя папка пользователя, существующего в каталоге AD + share папка

В другом влане: 1 samba debian сервер для хранения шары и домашних папок, и 1 windows server AD DC
![|381x408](Интеграция%20Linux%20в%20домен%20заметки-31.07.2025-14_07.png)
# Заметки по практике
После установки AD оснастки, необходимо повысить сервер до контроллера домена
![|413x336](Интеграция%20Linux%20в%20домен%20заметки-30.07.2025-12_07.png)

Был обнаружен баг либо недостаток, что если debian сервер не имеет сетевую связность с AD DC, то отсутствует возможность войти в какую либо учетку. Вероятно из за плотного вмешательства kreberos в модуль PAM. Деффект устраняется при отключении адаптера, через который сервер связывается с AD DC.
## Настройка Mikrotik'ов
образ можно легко добавить при помощи утили ishare2. Желательно использовать последнюю версию 6, так как у нее гораздо меньшее потребление ресурсов, но при этом достаточный для выполнения задания функционал

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
[admin@MikroTik] /interface bridge port> add bridge=bridge1 interface=ether1
[admin@MikroTik] /interface bridge port> add bridge=bridge1 interface=ether2
[admin@MikroTik] /interface bridge port> add bridge=bridge1 interface=ether3
[admin@MikroTik] /interface bridge port> add bridge=bridge1 interface=ether4
[admin@MikroTik] /interface bridge port> add bridge=bridge1 interface=ether5
```

# Источники инфы
https://www.youtube.com/watch?v=S7QOkcznEVY&ab_channel=SpecialistTV 

https://wikival.bmstu.ru/doku.php?id=%D1%80%D0%B0%D0%B7%D0%B2%D0%B5%D1%80%D1%82%D1%8B%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5_active_directory - дублирую ссылку из описания под видео

настройки на микротике:
- https://mikrotik.moscow/blog/nastroyki/nat-v-routeros/ - NAT
- https://www.manitonetworks.com/mikrotik/2016/3/5/vlan-trunking - создание VLAN интерфейсов на роутере
- https://help.mikrotik.com/docs/spaces/ROS/pages/328068/Bridging+and+Switching - перевод роутера фактически в режим коммутации
- https://www.youtube.com/watch?v=n_XU_mvxfLM&ab_channel=KalTek - разбор конфигурации router on stick
