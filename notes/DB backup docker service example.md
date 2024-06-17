### Docker compose example
```yaml
services:
  db-backup:
    build: .
    restart: unless-stopped
    volumes:
      - ./backup/backup-files:/backup
      - ./backup/backup_script.sh:/etc/periodic/weekly/backup_script.sh
    environment:
      - DB_CONNECTION_STRING=${DB_CONNECTION_STRING}
```

### Docker file
```Dockerfile
FROM alpine:3.19
LABEL maintainer="carrergt@gmail.com"

RUN apk update; apk add mariadb-client

CMD ["crond","-f"]
```

### Script
```shell
#!/bin/sh
CURRENT_DATE=$(date +%Y-%m-%d_%H-%M-%S)
FILE_NAME="backup_$CURRENT_DATE.sql"

# DB_CONNECTION_STRING=mariadb+pymysql://user:password@localhost:3306/db_name
PROTO="$(echo $DB_CONNECTION_STRING | sed -r 's|://.*||')"
USER="$(echo $DB_CONNECTION_STRING | sed -r 's|.*://([^:]*):.*|\1|')"
PASSWORD="$(echo $DB_CONNECTION_STRING | sed -r 's|.*://[^:]*:([^@]*)@.*|\1|')"
HOST="$(echo $DB_CONNECTION_STRING | sed -r 's|.*@([^:]*):.*|\1|')"
PORT="$(echo $DB_CONNECTION_STRING | sed -r 's|.*:([0-9]*)/.*|\1|')"
DATABASE="$(echo $DB_CONNECTION_STRING | sed -r 's|.*/([^/?]*)|\1|')"

mariadb-dump -h $HOST -P $PORT -u $USER -p$PASSWORD $DATABASE > /backup/$FILE_NAME
```

[[MariaDB]]
[[MySQL]]
[[PostgreSQL]]