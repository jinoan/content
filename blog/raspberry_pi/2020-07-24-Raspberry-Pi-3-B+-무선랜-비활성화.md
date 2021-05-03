---
title: "Raspberry Pi 3 B+ 무선 랜 비활성화"
date: 2020-07-24T12:23:43+09:00
draft: False
category: "raspberry_pi"
---

유선 랜이 연결되어 있음에도 가끔 wifi로 잡히는 현상이 발생  
라즈베리파이에서 무선 랜을 비활성화해보자.

`/boot/config.txt`에 다음 코드 추가

```
dtoverlay=disable-wifi
# 블루투스를 비활성화 하려면
#dtoverlay=disable-bt
```

라즈베리파이를 재부팅하면 적용된다.