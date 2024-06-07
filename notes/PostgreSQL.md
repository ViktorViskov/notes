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
