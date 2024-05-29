```yaml
networks:
  some_network:
    name: some_network
    external: true

services:
  example_1:
	container_name: example_2
    image: image-name
    restart: always
    ports:
      - '9000:9000'
    environment:
      - ENV_VARIABLE=${ENV_VARIABLE}
    volumes:
      - 'some_volume:/media/folder'
    networks:
      - some_network

  example_2:
    container_name: example_2
    restart: unless-stopped
    network_mode: "host"
    build: .
      
volumes:
  some_volume:
    driver: local
```

[[docker]]