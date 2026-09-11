<div align="center">

[![English](https://img.shields.io/badge/README-English-blue)](README.md)
[![Русский](https://img.shields.io/badge/README-Русский-red)](README.ru.md)
[![Oʻzbekcha](https://img.shields.io/badge/README-Oʻzbekcha-green)](README.uz.md)

</div>

# Smart Syslog Hub
[![CI](https://github.com/uMax-Cyber/LogForge/actions/workflows/ci.yml/badge.svg)](https://github.com/uMax-Cyber/LogForge/actions/workflows/ci.yml)


![Namoyish](screenshots/demo.svg)
Koʻp vendorli tarmoqlar uchun aqlli filtrlash bilan markazlashtirilgan syslog qabul qiluvchisi. Loglarni manba qurilma turiga qarab yoʻnaltiradi (fayrvollar, tarmoq uskunalari, serverlar), koʻp interfeysli fayrvollarning source-IP-larini qayta ishlaydi va katta hajmlar uchun sozlangan rotatsiya siyosatlarini oʻz ichiga oladi.

## Arxitektura

```
Firewalls (multi-interface) ──┐
Network switches/APs ─────────┤──▶ rsyslog (UDP 514) ──▶ Per-source log files
Linux servers ────────────────┘         │
                                        ▼
                                  Logrotate (per-source policy)
```

## Asosiy xatolar (real joylashtirishdan)

### 1. Koʻp interfeysli fayrvollar egress-IP dan yuboradi
10+ interfeysli fayrvol syslogni oʻzining LAN egress-IP sidan yuborishi mumkin, management-IP dan EMAS. Sizning «management-IP» filtringiz barcha fayrvol loglarini sezilmaydi oʻtkazib yuboradi.

**Yechimi**: filtrlarni yozishdan oldin haqiqiy source-IP-larni ushlab oling. Barcha maʼlum interfeys IP-larini qoʻshing.

### 2. Fayl huquqlari (jim xato)
rsyslog `syslog` foydanuvchisidan ishlaydi, root dan emas. Agar log fayllari `root:root` ga tegishli boʻlsa, rsyslogning oʻz logida jim `Permission denied` olasiz, tarmoq loglari esa tashlab yuboriladi.

**Yechimi**: `chown syslog:adm /var/log/network.log`

### 3. Filtrlar tartibi muhim
Keng filtr (`startswith "10.0.0."`) aniq filtr (`isequal "10.0.0.254"`) dan oldin yuklansa, aniq qurilmaning loglarini oʻziga tortib oladi.

**Yechimi**: konfig fayllarini aniq filtrlar birinchi yuklanadigan qilib nomlang (59-firewall.conf, 60-network.conf dan oldin).

### 4. Hajmni rejalashtirish
«Allowed» sessiyalarini loglayotgan bitta fayrvol **21 000 qator/daqiqa** (~7 GB/kun) hosil qilishi mumkin. Logrotate-ni bunga mos rejalashtiring.

## Konfiguratsiya fayllari

- `config/59-firewall.conf` — aniq IP filtrlari (BIRINCHI yuklanadi)
- `config/60-network-devices.conf` — keng tarmoq qurilmalari filtri
- `config/logrotate-firewall` — yuqori hajmli fayrvol loglari uchun agressiv rotatsiya

## Litsenziya
MIT

## 📬 Aloqa

Savollaringiz bormi? Yozing: **[allumaxmail@gmail.com](mailto:allumaxmail@gmail.com)**

---

<div align="center">

[![English](https://img.shields.io/badge/README-English-blue)](README.md)
[![Русский](https://img.shields.io/badge/README-Русский-red)](README.ru.md)
[![Oʻzbekcha](https://img.shields.io/badge/README-Oʻzbekcha-green)](README.uz.md)

</div>
