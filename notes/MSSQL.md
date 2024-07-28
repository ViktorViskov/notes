### Docker compose example
```yml
services:

  db:
    image: mcr.microsoft.com/mssql/server:2022-latest
    restart: always
    environment:
      ACCEPT_EULA: Y
      MSSQL_SA_PASSWORD: Password1!
    ports: 
      - "1433:1433"
```