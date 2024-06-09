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
[[PostgreSQL]]
apac[[MariaDB]]
