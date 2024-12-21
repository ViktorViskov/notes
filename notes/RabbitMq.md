### Docker compose 
```yml
services:
  rabbit_mq:
    image: rabbitmq:management-alpine
    container_name: rabbit_mq
    restart: unless-stopped
    network_mode: "host"
    environment:
      - RABBITMQ_DEFAULT_USER=quene
      - RABBITMQ_DEFAULT_PASS=MBDGQ3x1dW3iY2fwOvJyXv
    logging:
      driver: "none"
```
#### Access to managment web ui
`http://ip:15672`