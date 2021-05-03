---
title: "Raspberry Pi 3 B+ 고정IP 설정"
date: 2020-07-17T18:00:54+09:00
draft: False
category: "raspberry_pi"
---

`/etc/dhcpcd.conf` 파일에 아래 내용 추가

interface eth0
static 고정아이피
static 넷마스크
static DNS

```
interface eth0
static ip_address=192.168.0.10/25
static routers=192.168.0.1
static domain_name_servers={DNS1} {DNS2}
```

