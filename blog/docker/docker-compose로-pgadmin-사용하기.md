---
title: Docker Compose로 pgAdmin 사용하기
date: 2020-10-26 13:10:95
category: docker
thumbnail: { thumbnailSrc }
draft: false
---

- pgAdmin 이미지 및 컨테이너 생성을 위한 `docker-compose.yml` 파일

  ```yaml
  version: '3'
  
  services:
      pgadmin:
          container_name: pgadmin
          image: dpage/pgadmin4
          restart: always
          networks:
              - UDN_Database
          ports:
              - 10080:80
          volumes:
              - ~/Desktop/pgsql/pgadmin:/var/lib/pgadmin
          environment:
              - PGADMIN_DEFAULT_EMAIL=myid@example.com
              - PGADMIN_DEFAULT_PASSWORD=examplePassword
          
  networks:
      My_Database:
          external:
              name: My_Database
  ```

- pgAdmin의 저장공간에 접근하기 위해  `~/Desktop/pgsql/pgadmin` 폴더 권한 설정을 해줘야 함

  ```bash
  $ sudo chmod 777 ~/Desktop/pgsql/pgadmin
  ```

- `docker-compose up -d -—build pgadmin` 하면 pgadmin이 서비스되는 Docker 환경이 생성됨

- `~/Desktop/pgsql/pgadmin/storage/myid_example.com` 폴더 접근을 위해 권한 변경 (query tool 기능으로 저장한 sql 파일들이 이 폴더에 저장됨)

  ```bash
  $ sudo chmod 777 ~/Desktop/pgsql/pgadmin/storage/myid_example.com
  ```

