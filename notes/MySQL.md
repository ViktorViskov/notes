Docker compose example
```yml
services:
  db:
    image: mysql
    container_name: mysql-db
    restart: always
    ports:
      - 3306:3306
    environment:
      MYSQL_ROOT_PASSWORD: ${DB_PASSWORD}
      MYSQL_DATABASE: db-name
```


[[MariaDB]]
[[DB backup docker service example]]