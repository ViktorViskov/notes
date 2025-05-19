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
      POSTGRES_DB: db_name
    ports:
      - "5432:5432"
    volumes:
      - "./data:/var/lib/postgresql/data/"

```

#### Tune config file
https://pgtune.leopard.in.ua/
https://www.pgconfig.org/

Config example
```conf
listen_addresses = '*' # allow all hosts
max_connections = 500
shared_buffers = 12GB
effective_cache_size = 36GB
maintenance_work_mem = 2GB
checkpoint_completion_target = 0.9
wal_buffers = 16MB
default_statistics_target = 100
random_page_cost = 1.1
effective_io_concurrency = 200
work_mem = 6291kB
huge_pages = try
min_wal_size = 2GB
max_wal_size = 8GB
max_worker_processes = 12
max_parallel_workers_per_gather = 4
max_parallel_workers = 12
max_parallel_maintenance_workers = 4
log_min_duration_statement = '10s'  # log queries with executions more than 10s
```