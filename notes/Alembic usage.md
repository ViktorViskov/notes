#### Init alembic
```bash
alembic init migration
```

#### Autogenerate migration file
```bash
alembic revision --autogenerate -m "name"
```

#### Accept migration to DB
```bash
alembic upgrade head
```

#### Revert last migration
```bash
alembic downgrade -1
```

[[orm]]
[[python]]
[[sqlalchemy]]