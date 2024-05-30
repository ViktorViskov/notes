#### Postgresql
```yaml
services:

  db:
    image: postgres:15-alpine
    restart: always
    environment:
      POSTGRES_USER: cars_user
      POSTGRES_PASSWORD: password
    ports:
      - "5432:5432"

```

#### MariaDB
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

#### Httpd
```yaml
services:

  httpd:
    image: httpd:alpine
    restart: always
    ports:
      - 80:80
    volumes:
      - ./share:/usr/local/apache2/htdocs/
```


[[docker]]
[[templates]]