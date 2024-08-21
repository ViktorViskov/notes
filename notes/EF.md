## Linux / cli usage
#### Install linux cli
```shell
dotnet tool install --global dotnet-ef
```
#### Create migration
```bash
dotnet ef migrations add InitialCreate
```

#### Accept migration to db
```bash
dotnet ef database update
```

#### Remove migration
```bash
dotnet ef migrations remove
```

#### Rollback migration
```bash
dotnet ef database update Init
```

[[ef]]
[[orm]]
[[asp.net]]