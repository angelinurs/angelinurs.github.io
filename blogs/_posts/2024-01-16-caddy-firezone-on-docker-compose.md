---
layout: post
title: caddy, firezone on docker compose
description: >
  Let's construct a firezone which connects to reverse proxy - caddy with docker compose
sitemap: false
hide_last_modified: true
categories: [docker compose, vpn, firezone, caddy, reverse proxy]
tags:       [docker compose, vpn, firezone, caddy, reverse proxy]
---

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Recommended Specifications

- ubuntu 22.04 live server amd64
- caddy:2
- firezone/firezone:${VERSION:-latest}

## Reference site

- [Install Mattermost via Docker] &rarr; docs.mattermost.com
- [mattermost / docker] &rarr; github
- [Deploy Mattermost in Docker Swarm, Behind Caddy v2.4.5] &rarr; tuneit.me

## install network-manager

```sh
# install network-manager
sudo apt-get install network-manager -yq
```

{:.related-posts.faded}

## setup

### docker

- docker run

```sh

docker run --name mattermost-preview -d --publish 8065:8065 --add-host dockerhost:127.0.0.1 mattermost/mattermost-preview

# check to listening port
netstat -anp | grep LISTEN  # all of using port
netstat -anp | grep 8065 # using 8065 port

```

- Create service file
  
```sh
# file: /etc/systemd/system/mattermost.service
[Unit]
Wants=docker.service
After=docker.service

[Service]
RemainAfterExit=yes
ExecStart=/usr/bin/docker start mattermost-preview
ExecStop=/usr/bin/docker stop mattermost-preview

[Install]
WantedBy=multi-user.target
```

- Register as a Service

```sh
# registry as a service
systemctl enable mattermost.service 
# start service
systemctl start mattermost 
# Checking the status of the Mattermost service
systemctl status mattermost 
```

{:.related-posts.faded}

### docker-compose

- docker-compose.yml

```yaml

x-deploy: &manager-role-deploy
  placement: 
    constraints: [node.role == manager] 
  replicas: 1 
  update_config: 
    parallelism: 2 
    delay: 10s 
  restart_policy: 
    condition: on-failure

version: "3.7"

services:
# caddy
  caddy:
    image: caddy:2
    ports:
      - "9980:80"
      - "9443:443"
    networks:
      - caddy
    volumes:
      - /data/ci-cd/caddy/caddyfile:/etc/caddy/caddyfile
      - /data/ci-cd/caddy/caddydata:/data
      - /data/ci-cd/caddy/caddyconfig:/config
      - /data/ci-cd/caddy/caddylogs:/var/log/caddy
      - /data/ci-cd/mattermost/data:/mattermost/data
    deploy:
      <<: *manager-role-deploy

  # mattermost
  mattermost:
    image: mattermost/mattermost-preview
    depends_on:
      - backend-db
    volumes:
      - /data/ci-cd/mattermost/config:/mattermost/config
      - /data/ci-cd/mattermost/data:/mattermost/data
      - /data/ci-cd/mattermost/logs:/mattermost/logs
      - /data/ci-cd/mattermost/plugins:/mattermost/plugins
    ports:
      - '8065:8065'
    environment:
      # set same as db credentials and dbname
      - MM_USERNAME=mmuser
      - MM_PASSWORD=mmuser_password
      - MM_DBNAME=mattermost

      # - DOMAIN=mattermost.example.com
      - DOMAIN=192.168.161.250
      ## Below one can find necessary settings to spin up the Mattermost container
      - MM_SQLSETTINGS_DRIVERNAME=postgres
      # - MM_SQLSETTINGS_DATASOURCE=postgres:${POSTGRES_USER}:${POSTGRES_PASSWORD}@postgres:5432/${POSTGRES_DB}?sslmode=disable&connect_timeout=10
      - MM_SQLSETTINGS_DATASOURCE=postgres://mmuser:password@backend-db:5432/mattermost?sslmode=disable&connect_timeout=10      
      # - MM_SERVICESETTINGS_SITEURL=https://mattermost.tuneit.me
      - MM_SERVICESETTINGS_SITEURL=http://192.168.161.250:8065
      - TZ=Asia/Seoul
    networks:
      - caddy
    deploy:
      placement:
        constraints: [node.role == worker]
      replicas: 1
      update_config:
        parallelism: 2
        delay: 10s
      restart_policy:
        condition: on-failure
  # db
  backend-db:
    image: postgres:alpine
    volumes:
      - ./data/ci-cd/db/postgres:/var/lib/postgresql/data
    secrets:
      - post_db
      - post_user
      - post_password
    environment:
      - TZ=Asia/Seoul
      - POSTGRES_DB_FILE=/run/secrets/post_db
      - POSTGRES_USER_FILE=/run/secrets/post_user
      - POSTGRES_PASSWORD_FILE=/run/secrets/post_password
    networks:
      - caddy
    deploy:
      <<: *manager-role-deploy

secrets:
  post_db:
    file: /data/ci-cd/mattermost-postgres/post_db.txt # mattermost
  post_user:
    file: /data/ci-cd/mattermost-postgres/post_user.txt # mmuser
  post_password:
    file: /data/ci-cd/mattermost-postgres/post_password.txt # dlwltjxl1!
volumes:
  mattermostdata:
    driver: "local"
  mattermostlogs:
    driver: "local"
  mattermostplugins:
    driver: "local"
  postgres:
    driver: "local"

networks:
  caddy:
    external: true
```

{:.related-posts.faded}

- Caddyfile

```sh
# file: Caddyfile
{
        email gipark@easycerti.com
            default_sni mattermost
            cert_issuer acme
            # Production acme directory
            acme_ca https://acme-v02.api.letsencrypt.org/directory
            # Staging acme directory
            #acme_ca https://acme-staging-v02.api.letsencrypt.org/directory
            servers {
                protocol {
                    experimental_http3
                    allow_h2c
                    strict_sni_host
                }
                timeouts {
                    read_body   10s
                    read_header 10s
                    write       10s
                    idle        2m
                }
                max_header_size 16384
            }
        }

        192.168.161.250 {
            log {
                output file /var/log/caddy/mattermost.log {
                    roll_size 20mb
                    roll_keep 2
                    roll_keep_for 6h
                }
                format console
                level error
            }

            reverse_proxy mattermost:8065
            encode gzip zstd
        }
```

{:.related-posts.faded}

- running

```sh

# connection `static_ether` as static ip
docker compose up -d

```

{:.related-posts.faded}

[Install Mattermost via Docker]:https://docs.mattermost.com/install/install-docker.html
[mattermost / docker]:https://github.com/mattermost/docker#mattermost-docker-setup
[Deploy Mattermost in Docker Swarm, Behind Caddy v2.4.5]:https://tuneit.me/caddy/deploy-mattermost-in-docker-swarm-behind-caddy/
