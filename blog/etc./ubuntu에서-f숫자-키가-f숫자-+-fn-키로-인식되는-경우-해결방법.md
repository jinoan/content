---
title: Ubuntu에서 F숫자 키가 F숫자 + Fn 키로 인식되는 경우 해결방법
date: 2020-11-18 12:11:36
category: etc.
thumbnail: { thumbnailSrc }
draft: false
---

1. 아래 방법으로 해결되는지 확인

   ```bash
   sudo bash -c "echo 2 > /sys/module/hid_apple/parameters/fnmode"
   ```

   

2. 만약 해결된다면 항상 적용되도록 아래 코드를 실행한다.

   ```bash
   echo options hid_apple fnmode=2 | sudo tee -a /etc/modprobe.d/hid_apple.conf
   sudo update-initramfs -u -k all
   sudo reboot # optional
   ```

   

> 출처: https://askubuntu.com/questions/7537/how-can-i-reverse-the-fn-key-on-an-apple-keyboard-so-that-f1-f2-f3-are-us

