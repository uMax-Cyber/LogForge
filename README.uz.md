<div align="center">

[![English](https://img.shields.io/badge/README-English-blue)](README.md)
[![Русский](https://img.shields.io/badge/README-Русский-red)](README.ru.md)
[![Oʻzbekcha](https://img.shields.io/badge/README-Oʻzbekcha-green)](README.uz.md)

</div>

# Smart Syslog Hub
[![CI](https://github.com/uMax-Cyber/LogForge/actions/workflows/ci.yml/badge.svg)](https://github.com/uMax-Cyber/LogForge/actions/workflows/ci.yml)


![Namoyish](screenshots/demo.svg)
Koʻp vendorli tarmoqlar uchun aqlli filtrlashga ega markazlashtirilgan syslog qabulqilgichi. Loglar manba turiga qarab ajratiladi (fayrvol, tarmoq uskunasi, server), koʻp interfeysli fayrvollarning source-IP-lari toʻgʻri ishlanadi, katta hajm uchun rotatsiya siyosati ham oldindan tayyor.

## Arxitektura

```
Firewalls (multi-interface) ──┐
Network switches/APs ─────────┤──▶ rsyslog (UDP 514) ──▶ Per-source log files
Linux servers ────────────────┘         │
                                        ▼
                                  Logrotate (per-source policy)
```

## Real joylashtirishdan olingan saboqlar

### 1. Fayrvol loglari egress-IP-dan keladi
10+ interfeysli fayrvol syslogni management-IP-dan emas, balki LAN egress-IP-sidan yuborishi mumkin. «Management-IP» boʻyicha yozilgan filtr barcha fayrvol loglarini sezilmaydan oʻtkazib yuboradi.

**Yechimi**: filtrlarni yozishdan oldin haqiqiy source-IP-larni ushlab oling va barcha maʼlum interfeys IP-larini kiritng.

### 2. Fayl huquqlari (jimgina xato)
rsyslog root emas, `syslog` foydalanuvchisi nomidan ishlaydi. Log fayllari `root:root` ga tegishli boʻlsa, tarmoq loglari tashlab yuboriladi, xato esa faqat rsyslogning oʻz logida `Permission denied` sifatida koʻrinadi.

**Yechimi**: `chown syslog:adm /var/log/network.log`

### 3. Filtrlar tartibi muhim
Keng filtr (`startswith "10.0.0."`) aniq filtr (`isequal "10.0.0.254"`) dan oldin yuklansa, aniq qurilmaning loglari shu keng filtrga tushib qoladi.

**Yechimi**: konfig fayllarini aniq filtrlar birinchi yuklanadigan qilib nomlang (59-firewall.conf — 60-network.conf-dan oldin).

### 4. Hajmni rejalashtirish
«Allowed» sessiyalarini ham loglaydigan bitta fayrvol 21 000 qator/daqiqa (~7 GB/kun) chiqarishi mumkin. Logrotate-ni shunga moslab sozlang.

## Konfiguratsiya fayllari

- `config/59-firewall.conf` — aniq IP boʻyicha filtrlar (BIRINCHI yuklanadi)
- `config/60-network-devices.conf` — tarmoq qurilmalari uchun keng filtr
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
