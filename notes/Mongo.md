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
from datetime import timezone
from bson.codec_options import CodecOptions
from typing import Any
from typing import Callable
from typing import TypeVar
from typing import Type

import pymongo


_CONNECTION_STRING = "mongodb://mongouser:mongopassword@localhost:27017/some_db"

_DB_NAME = _CONNECTION_STRING.split("/").pop()
_PREPARED_CONNECTION_STRING = _CONNECTION_STRING.replace(f"/{_DB_NAME}", "")

_CLIENT = pymongo.MongoClient(_PREPARED_CONNECTION_STRING)

DB = _CLIENT.get_database(
    _DB_NAME, codec_options=CodecOptions(tz_aware=True, tzinfo=timezone.utc)
)


class Field:
    def __init__(self, name: str, default: Any = None, default_factory: Callable[[], Any] | None = None):
        self.name = name
        self.private_name = f"_{name}"
        self.default = default
        self.default_factory = default_factory

    def __get__(self, instance, owner):
        if instance is None:
            # Accessed via class
            return self.name

        if not hasattr(instance, self.private_name):
            # Set default if not already set
            if self.default_factory is not None:
                value = self.default_factory()
            else:
                value = self.default
            setattr(instance, self.private_name, value)

        return getattr(instance, self.private_name)

    def __set__(self, instance, value):
        setattr(instance, self.private_name, value)

class Base:
    @classmethod
    def from_dict(cls, data):
        instance = cls()
        for attr, field in cls.__dict__.items():
            if isinstance(field, Field):
                value = data.get(field.name)
                if value is not None:
                    setattr(instance, attr, value)
        return instance

    @classmethod
    def from_list_dicts(cls, data_list):
        return [cls.from_dict(data) for data in data_list]

    def to_dict(self):
        result = {}
        for attr, field in self.__class__.__dict__.items():
            if isinstance(field, Field):
                result[field.name] = getattr(self, attr)
        return result

T = TypeVar("T")
def mapped_field(name: str, data_type:Type[T], default: Any = None, default_factory: Callable[[], Any] | None = None) -> T:
    """
    Maps a field to a specific type and provides default values.
    """
    return Field(name, default, default_factory) # type: ignore
```
## DB models example

## models.py
```python
from bson.objectid import ObjectId
from datetime import datetime
from datetime import timezone

from core.context import Base
from core.context import Field
from core.context import mapped_field


class UserDb(Base):
    id = mapped_field("_id", ObjectId, default_factory=ObjectId)
    username = mapped_field("username", str)
    password = mapped_field("password", str)
    group = mapped_field("group", str)
    is_active = mapped_field("is_active", bool, default=True)
    created_at = mapped_field("created_at", datetime, default_factory=lambda: datetime.now(timezone.utc))
    updated_at = mapped_field("updated_at", datetime, default_factory=lambda: datetime.now(timezone.utc))

class NoteDb(Base):
    id = mapped_field("_id", ObjectId, default_factory=ObjectId)
    user_id = Field("user_id")
    title = Field("title")
    content = Field("content")
    created_at = mapped_field("created_at", datetime, default_factory=lambda: datetime.now(timezone.utc))
    updated_at = mapped_field("updated_at", datetime, default_factory=lambda: datetime.now(timezone.utc))
```
## Repo example
```python
from datetime import datetime
from datetime import timezone

from models.db import UserDb
from core.db_context import DB
from bson.objectid import ObjectId


COLLECTION = DB.get_collection("users")


def add(user: UserDb) -> UserDb:
    print(user.to_dict())
    id = COLLECTION.insert_one(user.to_dict()).inserted_id
    user.id = id
    return user


def get_by_id(id: ObjectId) -> UserDb | None:
    record = COLLECTION.find_one({UserDb.id: id})
    return UserDb.from_dict(record)


def get_by_username(username: str) -> UserDb | None:
    record = COLLECTION.find_one({UserDb.username: username})
    return UserDb.from_dict(record)


def get_all() -> list[UserDb]:
    items = COLLECTION.find()
    return UserDb.from_list_dicts(items)


def get_active() -> list[UserDb]:
    items = COLLECTION.find({UserDb.is_active: True})
    return UserDb.from_list_dicts(items)


def get_random() -> UserDb | None:
    record = COLLECTION.aggregate([{"$sample": {"size": 1}}]).try_next()
    return UserDb.from_dict(record)


def get_random_by_group(group: str) -> UserDb | None:
    record = COLLECTION.aggregate(
        [{"$match": {UserDb.group: group}}, {"$sample": {"size": 1}}]
    ).try_next()
    return UserDb.from_dict(record)


def delete(id: ObjectId) -> None:
    COLLECTION.delete_one({str(UserDb.id): id})


def update(proxy: UserDb) -> None:
    COLLECTION.update_one(
        {str(UserDb.id): proxy.id},
        {"$set": {**proxy.to_dict(), UserDb.updated_at: datetime.now(timezone.utc)}},
    )

```