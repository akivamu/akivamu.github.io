---
layout: post
title: Build and install Python from source
tags:
- Python
- Open Source
- IOT
---

https://gist.github.com/SeppPenner/6a5a30ebc8f79936fa136c524417761d

## Use docker image (optional)

```shell
docker pull debian:stretch

docker run -it debian:stretch /bin/bash
```

Raspbian stretch docker

```shell
docker pull balenalib/rpi-raspbian:stretch

docker run -it balenalib/rpi-raspbian:stretch /bin/bash
```

## Install dependencies

```shell
apt-get update -y
apt-get install build-essential tk-dev libncurses5-dev libncursesw5-dev libreadline6-dev libdb5.3-dev libgdbm-dev libsqlite3-dev libssl-dev libbz2-dev libexpat1-dev liblzma-dev zlib1g-dev libffi-dev -y
apt-get install wget -y
```

```shell
wget https://www.python.org/ftp/python/3.7.3/Python-3.7.3.tar.xz
tar xf Python-3.7.3.tar.xz
cd Python-3.7.3
./configure
make -j 4
make altinstall
```
