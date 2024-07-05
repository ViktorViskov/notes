Open source server to storage media files 

Docker compose file
```yaml
services:
  minio:
    container_name: minio_storage
    image: docker.io/bitnami/minio:2024
    restart: unless-stopped
    ports:
      - '9000:9000'
      - '9001:9001'
    environment:
      - MINIO_ROOT_USER=${MINIO_USER}
      - MINIO_ROOT_PASSWORD=${MINIO_PASS}
    volumes:
      - '/storage/hdd:/bitnami/minio/data'
```

[[backend]]
