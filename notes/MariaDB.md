### Packages

##### Debian
```shell
mariadb-client
```

##### Alpine
```
mariadb-client
```

### Connect example
```shell
mariadb -h 192.168.0.2 -P 3306 -u root -pSomePassword db_name
```
```shell
mariadb -h $HOST -P $PORT -u $USER -p$PASSWORD $DATABASE
```

### Dump usage

##### Create
```shell
mariadb-dump -h $HOST -P $PORT -u $USER -p$PASSWORD $DATABASE > backup.sql
```

##### Restore
```shell
mariadb -h $HOST -P $PORT -u $USER -p$PASSWORD $DATABASE < backup.sql
```

#### Docker compose example
```
services:

  db:
    image: mariadb:10.6
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: test
    ports: 
      - "3306:3306"
```

[[MariaDB]]
[[MySQL]]