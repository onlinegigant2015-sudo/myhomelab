# Теория
- Broadcast (на .255) RouterOS обрабатывает как input — потому что он адресован всей подсети, включая роутер. Unicast → input или forward в зависимости от адреса назначения.
- 
## Цепочки в Firewall
### Входящие
- Input - пакеты, которые направлены на роутер - IP роутера.
- Forward - пакеты, которые направлены сквозь роутер в другую сеть - транзит.
### Исходящие
- Output - пакеты, направлены из роутера - рождены из роутера.
## Фильтрация Firewall
Правила фильтрации в Firewall на RouterOS проверяются сверху-вниз, процесс проверки заканчивается, как только правило сработало.
Чтобы добавить правило в Firewall, необходимо выполнить следующую команду
`ip firewall filter add chain=... (connection-state=...) protocol=... (dst-port=...) (in-Interface=...) action=... (comment=...)`
- `chain=` - для какой цепочки пакетов предназначено правило
- `connection-state`- применяется, когда нужно фильтровать определенные пакеты. Всего существуют 4 вида пакетов:
  - `new` - SYN пакеты, которые только направляют запрос на подключение.
  - `established` - пакеты, с которыми уже установлено подключение.
  - `related` - дополнительные пакеты, которые связаны с `established` пакетами.
  - `invalid` - некорректный пакет
- `protocol` - пакеты будут фильтроваться по определенному протоколу.
- `dst-port` - его нужно указывать всегда в связке с протоколами TCP и UDP, данная команда манипулирует доступом к определенным портам. Например `dst-port=443 protocol=tcp` - это доступ к сервису SSTP.
- `action` - действие, которое будет направлена на пакет. Существуют несколько видов действий:
  - `accept` - разрешение подключения
  - `drop` - блокирование подключения без обратной связи. Запрашивающая сторона видит только timeout. Актуально для фильтра пакетов из внешнего интернета. timeout 
  - `reject` - блокирование подключение с обратной связью. Актуально для локальной сети. refused/RST
  - `log` - логирование событий в журнал. Это действие не останавливает проверку. Так как критичных действий он не совершает.
- `in-interface` - с какого интерфейса пакет пришел.
     # Настройки фильтров для лабораторной работы
```
INPUT:
#разрешение подключения для established/related соединений
0 Allow established/related
  chain=input
  action=accept
  connection-state=established,related
#разрешение подключения для ICMP пакетов 
1 Allow ICMP
    chain=input
    action=accept
    protocol=ICMP
#разрешение SSH подключения
2 Allow SSH
    chain=input
    action=accept
    protocol=tcp
    dst-port=22
#разрешение Winbox подключения
3 Allow Winbox
    chain=input
    action=accept
    protocol=tcp
    dst-port=8291
#разрешение Web подключения
4 Allow Web connection
    chain=input
    action=accept
    protocol=tcp
    dst-port=80
#разрешение SSTP подключения
5 Allow SSTP
    chain=input
    action=accept
    protocol=tcp
    dst-port=443
#Логирование блокированных подключений
6 Log
    chain=input
    action=log
    log-prefix="DROPPED: "
#Блокировка остальных подключений
7 Drop all other input
    chain=input
    action=drop
FORWARD:
#Разрешение подключения для established/related соединений
8 Allow established/related
  chain=forward
  action=accept
  connection-state=established,related
  log=no
  log-prefix=""
#Доступ из LAN в WAN
9 Allow LAN to WAN
  chain=forward
  action=accept
  in-interface=!ether1
  log=no
  log-prefix=""
#Блокировка остальных подключений
10 Drop all other Forward
    chain=forward
    action=drop
    log=no
    log-prefix=""
```
# Мои ошибки
- Опечатка eher1 → RouterOS не найдёт интерфейс.
- Правило accept ≠ сервис работает: если на порту никто не слушает — kernel отвечает RST (refused).
- Быстрый False в TNC = refused, медленный = drop/timeout.
