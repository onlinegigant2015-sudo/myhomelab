# Systemd
`systemd` - это утилита или демон, которая позволяет гибко настривать систему. На `systemd` держится весь Linux.
## Структура Systemd
`systemd` состоит из нескольких процессов:
- `systemctl`
- `journald`
    - `journalctl`
- `udev`
- `networkd`
- `logind`
- `timedated`

Так же у `systemd` существует несколько унитов:
- `socket`
- `service`
- `timer`
- `target`
- `path`
- `mount`
