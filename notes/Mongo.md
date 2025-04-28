# `docker-compose.yml`
```yaml
services:
  mongo:
    image: mongo
    restart: always
    ports:
      - 27017:27017
    environment:
      MONGO_INITDB_ROOT_USERNAME: mongouser
      MONGO_INITDB_ROOT_PASSWORD: mongopassword
    volumes:
      - ./data:/data/db

  mongo-express:
    image: mongo-express
    restart: always
    ports:
      - 8081:8081
    environment:
      ME_CONFIG_MONGODB_URL: mongodb://mongouser:mongopassword@mongo:27017/
      ME_CONFIG_BASICAUTH_USERNAME: mongouser
      ME_CONFIG_BASICAUTH_PASSWORD: mongopassword
      # ME_CONFIG_BASICAUTH: false # disable login to express
```

# Python example
## Pip packages
```
pymongo
```
## Context
```python
import pymongo


_CONNECTION_STRING = "mongodb://mongouser:mongopassword@localhost:27017/some_db"
if not _CONNECTION_STRING:
    raise Exception("DB connection string not providet")

_DB_NAME = _CONNECTION_STRING.split("/").pop()
_PREPARED_CONNECTION_STRING = _CONNECTION_STRING.replace(f"/{_DB_NAME}", "")

_CLIENT = pymongo.MongoClient(_PREPARED_CONNECTION_STRING)

DB = _CLIENT[_DB_NAME]
```
## DB models example

## base.py
```python
from dataclasses import dataclass, fields, asdict
from bson.objectid import ObjectId
from datetime import datetime
from json import dumps


class BaseMeta(type):
    _cache_fields = {}

    def __getattr__(cls, name: str) -> str:
        class_name = cls.__name__

        if class_name not in cls._cache_fields:
            class_fields = {f.name for f in fields(cls)}  # type: ignore
            if class_fields:
                cls._cache_fields[class_name] = class_fields

        field_names = cls._cache_fields.get(class_name, set())
        if name in field_names:
            return name
        raise AttributeError(f"Field '{name}' not found in {cls.__name__}")


@dataclass
class Base(metaclass=BaseMeta):
    def to_dict(self):
        data = asdict(self)
        id = data.pop("id", ObjectId())
        data["_id"] = id
        return data

    def to_json(self) -> str:
        data = self.to_dict()
        data["_id"] = str(data["_id"])

        # Convert datetime fields to ISO format
        for key, value in data.items():
            if isinstance(value, datetime):
                data[key] = value.isoformat()

        return dumps(data)

    @classmethod
    def from_dict(cls, data: dict):
        data = data.copy()
        data["id"] = data.pop("_id", ObjectId())
        return cls(**data)
```

## models.py
```python
from bson.objectid import ObjectId
from dataclasses import dataclass
from dataclasses import field
from datetime import datetime
from datetime import timezone

from base import Base


@dataclass
class ProxyDb(Base):
    ip: str
    port: int
    username: str
    password: str
    group: str
    is_active: bool
    created_at: datetime = field(default_factory=lambda: datetime.now(timezone.utc))
    updated_at: datetime = field(default_factory=lambda: datetime.now(timezone.utc))
    id: ObjectId = field(default_factory=lambda: ObjectId())
```
## Repo example
```python
from datetime import datetime
from datetime import timezone

from db import ProxyDb
from context import DB
from bson.objectid import ObjectId


COLLECTION = DB["proxyes"]


def add(proxy: ProxyDb) -> ProxyDb:
    COLLECTION.insert_one(proxy.to_dict())
    return proxy


def get_by_id(id: ObjectId) -> ProxyDb | None:
    record = COLLECTION.find_one({ProxyDb.id: id})
    if record is None:
        return None

    return ProxyDb.from_dict(record)


def get_all() -> list[ProxyDb]:
    return [ProxyDb.from_dict(proxy) for proxy in COLLECTION.find()]


def get_active() -> list[ProxyDb]:
    return [
        ProxyDb.from_dict(proxy) for proxy in COLLECTION.find({ProxyDb.is_active: True})
    ]


def get_random() -> ProxyDb | None:
    record = COLLECTION.aggregate([{"$sample": {"size": 1}}]).try_next()
    if record is None:
        return None

    return ProxyDb.from_dict(record)


def get_random_by_group(group: str) -> ProxyDb | None:
    record = COLLECTION.aggregate(
        [{"$match": {ProxyDb.group: group}}, {"$sample": {"size": 1}}]
    ).try_next()
    if record is None:
        return None

    return ProxyDb.from_dict(record)


def delete(id: ObjectId) -> None:
    COLLECTION.delete_one({str(ProxyDb.id): id})


def update(proxy: ProxyDb) -> None:
    COLLECTION.update_one(
        {str(ProxyDb.id): proxy.id},
        {"$set": {**proxy.to_dict(), ProxyDb.updated_at: datetime.now(timezone.utc)}},
    )

```