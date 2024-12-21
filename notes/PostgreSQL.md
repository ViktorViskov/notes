### Connect example
```shell
psql -h $host -p $port -U $user -W -d $db
```

### Dump usage

##### Create
```shell
pg_dump -h $host -p $port -U $user -W -d $db > dump.sql
```

##### Restore
```shell
psql -h $host -p $port -U $user -W -d $db < dump.sql
```

#### Docker compose example
```yaml
services:

  db:
    image: postgres:16-alpine
    restart: always
    environment:
      POSTGRES_USER: cars_user
      POSTGRES_PASSWORD: password
    ports:
      - "5432:5432"
    volumes:
      - "./data:/var/lib/postgresql/data/"

```

#### Tune config file
https://pgtune.leopard.in.ua/