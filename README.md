# docker-torrust-actix

use torrust-actix tracker server in docker / container

在docker容器中使用torrust-actix tracker服务器

## 介绍

torrust-actix是一个衍生自torrust项目的公共BT tracker 服务器。其使用rust语言编写，并且提供了即用的windows可执行文件、源码和docker镜像。

但是官方项目提供的docker镜像说明不足。这里提供一个可以立即使用的docker镜像

## 下载

默认会基于alpine构建镜像，你可以使用以下命令拉取镜像

```bash
docker pull ryjer/torrust-actix:alpine
```

## 镜像说明

该镜像运行之后，内置的配置文件会在`6969/tcp`和`6969/udp`接口提供tracker服务

并在`8080/tcp`接口提供api服务。所以你应该暴露这3个接口来提供服务，至少应该保留`6969/tcp`或`6969/udp`其中的一个。

torrust-actix在运行时需要一个在相同目录下的配置文件，本镜像内置了默认配置文件，你可以不映射配置文件直接运行。但是也可以将自定义的配置文件映射到镜像中，镜像中的配置文件路径为`/app/config.toml`

## 运行镜像

你可以使用docker命令运行该镜像

```bash
docker run -itd --name torrust-actix \
--restart=unless-unstopped \
-p 6969:6969/tcp \
-p 6969:6969/udp \
-p 8080:8080/tcp \
ryjer/torrust-actix:alpine
```

也可以使用以下compose文件，将其命名为`torrust-actix-compose.yaml`

```yaml
version: '3.5'

services:
  tracker:
    image: ryjer/torrust-actix:alpine
    container_name: torrust-actix
    restart: unless-stopped
    environment:
      - TZ=Asia/Shanghai
    ports:
      - 6969:6969/tcp
      - 6969:6969/udp
      - 8080:8080/tcp
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 2048M
    logging:
      driver: "json-file"
      options:
        max-size: "16m"
```

然后使用以下命令部署该compose文件对应的镜像

```bash
docker compose -f torrust-actix-compose.yaml up -d
```


