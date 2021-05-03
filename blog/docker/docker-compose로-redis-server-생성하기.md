---
title: Docker Compose로 Redis Server 생성하기
date: 2020-10-26 10:10:48
category: docker
thumbnail: { thumbnailSrc }
draft: false
---

- Redis 이미지 및 컨테이너 생성을 위한 `docker-compose.yml` 파일

  ```yaml
  version: "3"
  
  services:
      redis6379:
          container_name: redis6379
          image: redis:latest
          restart: always
          container_name: redis
          hostname: redis6379
          network_mode: host
          ports:
              - 6379:6379
          volumes:
              - ~/Desktop/redis/6379/data:/data
              - ~/Desktop/redis/6379/conf/redis.conf:/usr/local/etc/redis/redis.conf
              - ~/Desktop/redis/6379/acl/users.acl:/etc/redis/users.acl
          command: redis-server /usr/local/etc/redis/redis.conf
  ```

  

- volumes에 생성한 경로에 미리 설정한 redis.conf 파일과 users.acl 파일을 넣어야 한다.

  [Redis configuration - Redis](https://redis.io/topics/config)

  여기서 redis 버전에 맞는 `redis.conf` 파일을 받을 수 있다.

  `~/Desktop/redis/6379/conf` 폴더 안에 받은 파일을 저장한다.

  users.acl 파일에는 redis 계정 관련 정보가 들어있다.

  `redis.conf` 안에 아래 한 줄을 추가한다.

  ```
  aclfile /etc/redis/users.acl
  ```

   

  `~/Desktop/redis/6379/acl` 폴더 안에 `users.acl` 파일을 생성하고 아래처럼 계정을 생성한다.

  ```
  user default on +@all ~* >{password 부분인데 상당히 길게 하는것을 권장}
  user {특정 user id} on -@all +get +set +select ~* >{password}  # get set select 명령 권한만 있는 특정 user 생성
  ```

  

- Redis 이미지 및 컨테이너 생성

  ```bash
  $ docker-compose up -d --build redis6379
  ```

