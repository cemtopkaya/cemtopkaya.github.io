---
layout: post
title:  "Debian Paket Havuzu (aptly, reprepro)"
date:   2024-08-23 11:57:24 +0000
categories: linux networking
tags: linux networking sysctl ipv6
---

## reprepro

[https://wikitech.wikimedia.org/wiki/Reprepro](https://wikitech.wikimedia.org/wiki/Reprepro)

[https://docs.debops.org/en/latest/ansible/roles/reprepro/defaults-detailed.html](https://docs.debops.org/en/latest/ansible/roles/reprepro/defaults-detailed.html)

[https://www.porcheron.info/setup-your-debianubuntu-repository-with-reprepro/](https://www.porcheron.info/setup-your-debianubuntu-repository-with-reprepro/)

[https://manpages.debian.org/unstable/reprepro/reprepro.1.en.html](https://manpages.debian.org/unstable/reprepro/reprepro.1.en.html)

```shell
$ tree
.
├── docker-compose.yml
├── Dockerfile
└── volume
    └── reprepro
        ├── conf
        │   ├── distributions
        │   ├── distributions_old
        │   ├── incoming
        │   └── options
        ├── db
        │   ├── checksums.db
        │   ├── contents.cache.db
        │   ├── packages.db
        │   ├── references.db
        │   ├── release.caches.db
        │   └── version
        ├── default
        ├── dists
        │   └── xenial
        │       ├── oldstable
        │       │   └── binary-amd64
        │       │       ├── Packages
        │       │       ├── Packages.gz
        │       │       └── Release
        │       ├── Release
        │       ├── stable
        │       │   └── binary-amd64
        │       │       ├── Packages
        │       │       ├── Packages.gz
        │       │       └── Release
        │       ├── testing
        │       │   └── binary-amd64
        │       │       ├── Packages
        │       │       ├── Packages.gz
        │       │       └── Release
        │       └── unstable
        │           └── binary-amd64
        │               ├── Packages
        │               ├── Packages.gz
        │               └── Release
        ├── incoming
        │   ├── dists
        │   │   └── xenial
        │   │       └── testing
        │   └── tmp
        │       └── xenial
        │           └── testing
        └── pool
```

```Dockerfile
$ cat Dockerfile
FROM ubuntu:focal
RUN apt update
RUN apt install -y nano gnupg reprepro wget curl iputils-ping net-tools nano nginx
CMD tail -f /dev/null
```

```yaml
$ cat docker-compose.yml
version: "2"

volumes:
  reprepro-nfs:
    driver_opts:
      type: "nfs"
      o: "addr=192.168.57.27,nolock,soft,rw"
      device: ":/mnt/nfs/reprepro"

services:
  reprepro:
    image: reprepro
    build:
      context: .
      dockerfile: Dockerfile
      args:
        buildno: 1
    ports:
      - "8889:80"
    container_name: rrr
    working_dir: /var/repositories
    command: tail -f /dev/null
    volumes:
      # nginx ayarları
      - ./volume/reprepro/default:/etc/nginx/sites-available/default

      # reprepro conf dizini (icinde incomig, distributions, options... dosyaları var)
      - ./volume/reprepro/conf:/var/repositories/conf/

      # dışarıdan gelen ve taranarak repoya alınacak deb dosyalarının karşılandığı ana dizin
      - ./volume/reprepro/incoming:/var/repositories/incoming

      # dağıtımların Release dosyalarının tutulduğu ana dizin
      - ./volume/reprepro/dists:/var/repositories/dists

      # taradıkça güncellenen veritabanı
      - ./volume/reprepro/db:/var/repositories/db

      # .deb paketlerinin dizinler halinde saklandığı ana dizin
      - ./volume/reprepro/pool:/var/repositories/pool

      # NFS Volume
      - reprepro-nfs:/var/repositories
```

## APTLY

```shell
~/docker/aptly$ tree
.
├── docker-compose.yml
├── dockerfiles
│   ├── Dockerfile
│   └── Dockerfile.1.5
├── multi-component.sh
├── README.md
└── volume
    ├── aptly_files
    │   ├── aptly.conf
    │   ├── index.html
    │   ├── package_move.sh
    │   ├── scan.sh
    │   ├── startup.sh
    │   └── supervisord.conf
    ├── changelogs
    │   └── pool
    │       └── main
    │           ├── _
    │           │   ├── changelog
    │           │   └── changelog.Debian.gz
    │           ├── c
    │           │   ├── certificate
    │           │   │   └── certificate_1.0.0
    │           │   │       └── changelog
    │           │   ├── cpp-jwt
    │           │   │   └── cpp-jwt_1.1.1
    │           │   │       └── changelog
    │           │   ├── curl
    │           │   │   └── curl_7.88.1-8ubuntu1
    │           │   │       └── changelog
    │           ├── f
    │           │   ├── freediameter-cinar-common
    │           │   │   ├── freediameter-cinar-common_1.4.0-10
    │           │   │   │   └── changelog
    │           │   │   └── freediameter-cinar-common_1.4.0-11
    │           │   │       └── changelog
    │           │   └── freediameter-cinar-dev
    │           │       ├── freediameter-cinar-dev_1.4.0-10
    │           │       │   └── changelog
    │           │       └── freediameter-cinar-dev_1.4.0-11
    │           │           └── changelog
    │           ├── g
    │           │   ├── g3log
    │           │   │   └── g3log_2.1.2
    │           │   │       └── changelog
    │           │   ├── g3log2
    │           │   │   ├── g3log2_2.3.1
    │           │   │   │   └── changelog
    │           │   └── grpc
    │           ├── libx
    │           │   ├── libxerces-c3.1
    │           │   │   └── libxerces-c3.1_3.1.3+debian-1
    │           │   │       └── changelog
    │           │   └── libxerces-c-dev
    │           │       └── libxerces-c-dev_3.1.3+debian-1
    │           │           └── changelog
    │           ├── m
    │           │   └── mongo-cxx-driver
    │           │       └── mongo-cxx-driver_3.6.7-20230912
    │           │           └── changelog
    │           ├── n
    │           │   └── nlohmann-json
    │           │       └── nlohmann-json_3.4.0
    │           │           └── changelog
    │           ├── o
    │           │   └── opentelemetry-cpp
    │           │       ├── opentelemetry-cpp_0.0.0
    │           │       │   └── changelog
    │           │       ├── opentelemetry-cpp_1.2.0
    │           │       │   └── changelog
    │           │       ├── opentelemetry-cpp_1.2.1
    │           │       │   └── changelog
    │           │       └── opentelemetry-cpp_1.9.1
    │           │           └── changelog
    │           ├── t
    │           │   └── thrift
    │           │       ├── thrift_0.15.0
    │           │       │   └── changelog
    │           │       ├── thrift_0.15.0-28092023
    │           │       │   └── changelog
    │           │       └── thrift_0.19.0
    │           │           └── changelog
    │           └── x
    │               └── xsd
    │                   └── xsd_4.0.0-1
    │                       └── changelog
    ├── incoming
    │   ├── bionic-oldstable
    │   ├── bionic-stable
    │   ├── bionic-testing
    │   ├── bionic-unstable
    │   ├── focal-oldstable
    │   ├── focal-stable
    │   ├── focal-testing
    │   │   ├── ddeb
    │   │   ├── debug-symbols
    │   │   │   ├── wharf-dbgsym
    │   │   │   └── wharf-dpdk-dbgsym
    │   │   └── debug-symbols_1.3.0fbefe290d075
    │   ├── focal-unstable
    │   ├── jammy-oldstable
    │   ├── jammy-stable
    │   ├── jammy-testing
    │   ├── jammy-unstable
    │   │   ├── CinarNnefNorthboundDeviceTriggering
    │   │   │   └── cinarnnefnorthbounddevicetriggering.16.202306.dbg
    │   │   └── CinarNnefNorthboundDeviceTriggering_1.0.0
    │   ├── xenial-oldstable
    │   ├── xenial-stable
    │   ├── xenial-testing
    │   └── xenial-unstable
    ├── nginx
    │   ├── conf.d
    │   │   ├── back.gif
    │   │   ├── changelogs.conf
    │   │   ├── folder.gif
    │   │   └── unknown.gif
    │   ├── default
    │   └── mime.types
    └── ui
        ├── dist
        │   ├── bundle.js
        │   └── bundle.js.map
        ├── index.html
        └── vendor
            └── mdl
                ├── material.blue-pink.min.css
                ├── material.min.css
                ├── material.min.css.map
                ├── material.min.js
                └── material.min.js.map
```

```Dockerfile
$ cat dockerfiles/Dockerfile.1.5
FROM ubuntu:lunar

ENV DEBIAN_FRONTEND=noninteractive

RUN apt update
RUN apt install --yes \
           wget \
           gnupg \
           nginx \
           nano \
           net-tools \
           supervisor \
           dpkg-dev \
           inotify-tools

RUN wget -qO - https://www.aptly.info/pubkey.txt | apt-key add -
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys A0546A43624A8331
RUN apt-get update
RUN apt-get install aptly -y

RUN mkdir /repo-scripts

VOLUME ["/repo-scripts"]

ENTRYPOINT ["/repo-scripts/startup.sh"]

LABEL ubuntu=lunar
LABEL aptly=1.5
LABEL aciklama="debian paketi deposu"
LABEL repo="http://gitlab.ulakhaberlesme.com.tr/ulak-environment/ulak-aptly-test.git"
```

```yaml
$ cat docker-compose.yml
version: "2"

networks:
  aptly_network:
    driver: bridge
    ipam:
      config:
        - subnet: 12.15.0.0/24
          gateway: 12.15.0.1

volumes:
  aptly-nfs:
    driver_opts:
      type: "nfs"
      o: "addr=192.168.57.27,nolock,soft,rw"
      device: ":/mnt/nfs/aptly"

services:
  aptly:
    image: registry.ulakhaberlesme.com.tr/ulak/aptly:1.5
    build:
      context: dockerfiles
      dockerfile: Dockerfile.1.5
      args:
        buildno: 1
    networks:
      aptly_network:
        ipv4_address: 12.15.0.2
    ports:
      - "8888:80"
      - "8889:8080"
      - "8088:88"
    container_name: aptly
    working_dir: /var/repositories
    privileged: true
    environment:
    - COMPONENTS=(unstable testing stable oldstable)
    - DISTRIBUTIONS=(xenial bionic focal jammy)
    volumes:
      # Incoming klasörü volume
      - ./volume/incoming:/incoming

      # nginx ayarları
      - ./volume/nginx/default:/etc/nginx/sites-available/default
      - ./volume/nginx/conf.d:/etc/nginx/conf.d
      # - ./volume/nginx/mime.types:/etc/nginx/mime.types

      # scan.sh & startup.sh & move package volume
      - ./volume/aptly_files/scan.sh:/repo-scripts/scan.sh
      - ./volume/aptly_files/startup.sh:/repo-scripts/startup.sh
      - ./volume/aptly_files/package_move.sh:/repo-scripts/package_move.sh

      # supervisord.conf volume
      - ./volume/aptly_files/supervisord.conf:/etc/supervisor/supervisord.conf

      # aptly root directory
      # - ./volume/aptly:/var/repositories/aptly

      # aptly.conf file
      - ./volume/aptly_files/aptly.conf:/root/.aptly.conf

      # index.html file
      - ./volume/aptly_files/index.html:/var/repositories/aptly/public/index.html

      # Aptly Web UI
      - ./volume/ui:/var/repositories/aptly/public/ui

      # Changelog Files
      - ./volume/changelogs:/usr/share/nginx/changelogs

      # NFS Volume
      - aptly-nfs:/var/repositories/aptly

#    logging:
#      driver: loki
#      options:
#        loki-url: "http://172.19.0.88:32301/loki/api/v1/push"
#        loki-external-labels: "job=aptly-prod,container_name={{.Name}}"
```