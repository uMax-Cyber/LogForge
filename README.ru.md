<div align="center">

[![English](https://img.shields.io/badge/README-English-blue)](README.md)
[![Русский](https://img.shields.io/badge/README-Русский-red)](README.ru.md)
[![Oʻzbekcha](https://img.shields.io/badge/README-Oʻzbekcha-green)](README.uz.md)

</div>

# Smart Syslog Hub
[![CI](https://github.com/uMax-Cyber/LogForge/actions/workflows/ci.yml/badge.svg)](https://github.com/uMax-Cyber/LogForge/actions/workflows/ci.yml)


![Демонстрация](screenshots/demo.svg)
Централизованный приёмник syslog с интеллектуальной фильтрацией для мультивендорных сетей. Маршрутизирует логи по типу устройства-источника (файрволы, сетевое оборудование, серверы), обрабатывает source-IP файрволов с множеством интерфейсов и включает политики ротации, настроенные под большие объёмы.

## Архитектура

```
Firewalls (multi-interface) ──┐
Network switches/APs ─────────┤──▶ rsyslog (UDP 514) ──▶ Per-source log files
Linux servers ────────────────┘         │
                                        ▼
                                  Logrotate (per-source policy)
```

## Ключевые подводные камни (из реального внедрения)

### 1. Файрволы с множеством интерфейсов шлют с egress-IP
Файрвол с 10+ интерфейсами может отправлять syslog со своего LAN egress-IP, а НЕ с management-IP. Ваш фильтр по «management-IP» молча пропустит все логи файрвола.

**Решение**: перехватите реальные source-IP до написания фильтров. Добавьте все известные IP интерфейсов.

### 2. Права на файлы (тихий сбой)
rsyslog работает от пользователя `syslog`, а не root. Если файлы логов принадлежат `root:root`, вы получите молчаливый `Permission denied` в собственном логе rsyslog, а сетевые логи будут отбрасываться.

**Решение**: `chown syslog:adm /var/log/network.log`

### 3. Порядок фильтров важен
Широкий фильтр (`startswith "10.0.0."`), загруженный раньше конкретного (`isequal "10.0.0.254"`), перехватит логи конкретного устройства.

**Решение**: назовите конфиги так, чтобы конкретные фильтры загружались первыми (59-firewall.conf раньше 60-network.conf).

### 4. Планирование объёма
Один файрвол, логирующий сессии «Allowed», может генерировать **21 000 строк/минуту** (~7 ГБ/день). Планируйте logrotate соответственно.

## Файлы конфигурации

- `config/59-firewall.conf` — фильтры по конкретным IP (загружаются ПЕРВЫМИ)
- `config/60-network-devices.conf` — широкий фильтр сетевых устройств
- `config/logrotate-firewall` — агрессивная ротация для высоконагруженных логов файрволов

## Лицензия
MIT

## 📬 Контакты

Вопросы? Пишите: **[allumaxmail@gmail.com](mailto:allumaxmail@gmail.com)**

---

<div align="center">

[![English](https://img.shields.io/badge/README-English-blue)](README.md)
[![Русский](https://img.shields.io/badge/README-Русский-red)](README.ru.md)
[![Oʻzbekcha](https://img.shields.io/badge/README-Oʻzbekcha-green)](README.uz.md)

</div>
