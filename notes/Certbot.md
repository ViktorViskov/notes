Simple utility to create ssl serteficate for web server
### `docker-compose.yml`
```yml
services:
  certbot:
    container_name: certbot
    restart: unless-stopped
    ports:
      - "80:80"
    build: ./
    volumes:
      - ./configs:/configs
      - ./configs/update_cert.sh:/update_cert.sh
      - ./ssl:/ssl
```
### `Dockerfile`
```Dockerfile
FROM alpine:3.19
LABEL maintainer="carrergt@gmail.com"

RUN apk update; apk add certbot

CMD ["crond","-f"]
```
### `conf.ini`
#### using certbot intern web server
```
email = carrergt@gmail.com
agree-tos = true
non-interactive = true

domains = website.com

authenticator = standalone
```
#### create only file for accept
```
email = carrergt@gmail.com
agree-tos = true
non-interactive = true

domains = website.com

authenticator = webroot
webroot-path = /static
```
### update script
```shell
#!/bin/sh

certbot certonly --config /configs/conf.ini --expand
cp /etc/letsencrypt/live/tid.mercantec.dk/* /ssl/
```