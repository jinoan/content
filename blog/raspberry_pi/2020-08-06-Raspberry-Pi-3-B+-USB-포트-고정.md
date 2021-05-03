---
title: "Raspberry Pi 3 B+ USB 포트 고정"
date: 2020-08-06T11:30:26+09:00
draft: false
category: "raspberry_pi"
---

라즈베리파이에서 시리얼 통신할 때 USB 포트가 변경되면서 끊어지는 현상 발생.  
USB 장치의 고유 정보를 이용해서 포트가 변경돼도 변경된 포트로 통신이 이어지도록 이름을 지정해 줄 수 있다.

1. 터미널에서 `dmesg | grep ttyUSB` 입력

   ```bash
   $ dmesg | grep ttyUSB
   [    5.652316] usb 1-1.1.2: FTDI USB Serial Device converter now attached to ttyUSB0
   ```

   여기서 필요한 정보

   - 출력문 중에서 `usb` 다음에 오는 문자. 여기서는 `1-1.1.2`
   - 자동 할당된 USB 포트. 여기서는 `ttyUSB0`

2. 터미널에서 `udevadm info --name=/dev/ttyUSB0 --attribute-walk` 입력

   ```bash {hl_lines=[5, 9, 32]}
   looking at parent device '/devices/platform/soc/3f980000.usb/usb1/1-1/1-1.1/1-1.1.2':
       KERNELS=="1-1.1.2"
       SUBSYSTEMS=="usb"
       DRIVERS=="usb"
       ATTRS{idProduct}=="6001"
       ATTRS{devnum}=="4"
       ATTRS{bNumConfigurations}=="1"
       ATTRS{devpath}=="1.1.2"
       ATTRS{serial}=="AR0JXJ3Q"
       ATTRS{authorized}=="1"
       ATTRS{manufacturer}=="FTDI"
       ATTRS{busnum}=="1"
       ATTRS{bmAttributes}=="a0"
       ATTRS{avoid_reset_quirk}=="0"
       ATTRS{bMaxPacketSize0}=="8"
       ATTRS{quirks}=="0x0"
       ATTRS{devspec}=="  (null)"
       ATTRS{configuration}==""
       ATTRS{bDeviceSubClass}=="00"
       ATTRS{tx_lanes}=="1"
       ATTRS{rx_lanes}=="1"
       ATTRS{product}=="FT232R USB UART"
       ATTRS{urbnum}=="5488424"
       ATTRS{ltm_capable}=="no"
       ATTRS{version}==" 2.00"
       ATTRS{bMaxPower}=="90mA"
       ATTRS{removable}=="removable"
       ATTRS{bcdDevice}=="0600"
       ATTRS{bDeviceProtocol}=="00"
       ATTRS{bDeviceClass}=="00"
       ATTRS{bNumInterfaces}==" 1"
       ATTRS{idVendor}=="0403"
       ATTRS{bConfigurationValue}=="1"
       ATTRS{maxchild}=="0"
       ATTRS{speed}=="12"
   ```

   `looking at parent device` 라고 나오는 줄의 끝 문자열이 `1-1.1.2` 인 부분을 찾는다.  
여러 속성 중에서 장치를 특정할 수 있는 정보를 기억해 둘 것.
   
   예를 들어 `ATTRS{idProduct}=="6001"` 이 부분을 기억해 둔다.
   
   일반적으로 `idProduct`, `idVendor`, `serial` 을 사용.
   
3. USB 포트 이름 규칙 생성
   
   `/etc/udev/rules.d/` 폴더 안에 `.rules` 파일 생성.  
   예를 들어 `sudo nano /etc/udev/rules.d/10-usb-serial.rules`  
   파일명에서 숫자 10은 rules 파일 실행 우선순위를 나타내는데 굳이 안 따라도 된다.
   
   파일 안에 다음처럼 입력하고 저장
   
   ```
   SUBSYSTEM=="tty", ATTRS{idProduct}=="6001", ATTRS{idVendor}=="0403", ATTRS{serial}=="AR0JXJ3Q", SYMLINK+="ttyUSB_DEV1"
   ```
   
   `SYMLINK` 에 있는 `ttyUSB_DEV1` 이 앞으로 사용하게 될 포트 명이다. 당연히 다른 이름으로 해도 됨.
   
4. `sudo udevadm trigger` 명령으로 변경사항 적용

5. `ls -l /dev/ttyUSB*` 을 쳐서 변경사항 확인

   ```bash
   $ ls -l /dev/ttyUSB*
   crw-rw---- 1 root dialout 188, 0 Aug  5 03:19 /dev/ttyUSB0
   lrwxrwxrwx 1 root root         7 Aug  5 03:19 /dev/ttyUSB_DEV1 -> ttyUSB0
   ```



> 참고
>
> - https://www.freva.com/2019/06/20/assign-fixed-usb-port-names-to-your-raspberry-pi/
> - https://mokga.tistory.com/54

