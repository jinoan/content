---
title: "Raspberry Pi 3 B+에서 Python 자동 실행"
date: 2020-07-17T18:19:45+09:00
draft: False
category: "raspberry_pi"
---

실행하고자 하는 파이썬 파일명과 경로가 `/home/pi/Desktop/filename.py` 라고 가정

1. Shell script에 파이썬 파일 실행 코드 작성

   (쉘 스크립트 파일명과 경로가 `~/Desktop/run.sh` 라고 가정)

   ```sh
   cd /home/pi/Desktop/  # 파이썬 파일이 있는 경로로 이동
   nohup python3 filename.py &
   ```

2. 홈(`/home/pi`)에 있는 `.profile` 파일에 아래 코드를 추가

   ```sh
   # insert this code in /home/pi/.profile
   pid=$(ps aux | grep python3 | grep filename.py | awk '{print $2}')
   if [ -z "$pid" ]; then
       /bin/sh /home/pi/Desktop/run.sh
   fi
   ```

   