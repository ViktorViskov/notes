Cloudflare free proxy build in docker container

Create folder with docker compose
```bash
mkdir proxy
cd proxy
nano docker-compose.yml
docker compose up -d
```

https://github.com/kingcc/warproxy
`docker-compose.yml` example
```yml
services:
  warproxy:
    image: ghcr.io/kingcc/warproxy:latest
    container_name: proxy
    network_mode: "host"
    restart: always
    environment:
      - HTTP_PORT=20080
```

Attach to container terminal
```bash
docker exec -ti proxy bash
```

Edith proxy config
```bash
apk add nano
nano wireproxy.conf
```
```bash
BindAddress = 127.0.0.1:20080
```

Config documentation
https://github.com/whyvl/wireproxy

Restart docker service
```bash
docker compose restart
```
