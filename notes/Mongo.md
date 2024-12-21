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
```python
from bson.objectid import ObjectId
from dataclasses import dataclass
from dataclasses import asdict
from dataclasses import field


@dataclass
class Base:
    def to_dict(self):
        data = asdict(self)
        id = data.pop("id", ObjectId())
        data["_id"] = id
        return data

    @classmethod
    def from_dict(cls, data: dict):
        data = data.copy()
        data["id"] = str(data.pop("_id", ObjectId()))
        return cls(**data)
    
@dataclass
class Car(Base):
    make: str
    model: str
    year: int
    indexed: bool
    id: str = field(default_factory=lambda: str(ObjectId()))
```
## Repo example
```python
from db.context import DB
from model import CarDb


COLLECTION = DB["cars"]

def add(car: CarDb):
    COLLECTION.insert_one(car.to_dict())

def get_all() -> list[CarDb]:
    return [CarDb.from_dict(car) for car in COLLECTION.find()]

def get_by_id(id) -> CarDb | None:
    record = COLLECTION.find_one({"_id": id})
    if record is None:
        return None
    
    return CarDb.from_dict(COLLECTION.find_one({"_id": id}))

def get_by_ids(ids: list[str]) -> list[CarDb]:
    return [CarDb.from_dict(car) for car in COLLECTION.find({"_id": {"$in": ids}})]

def get_by_make_and_model(make: str, model: str) -> list[CarDb]:
    return [CarDb.from_dict(car) for car in COLLECTION.find({"make": make, "model": model})]

def get_by_make_or_model(make: str, model: str) -> list[CarDb]:
    return [CarDb.from_dict(car) for car in COLLECTION.find({"$or": [{"make": make}, {"model": model}]})]

def update(car: CarDb):
    COLLECTION.update_one(
        {"_id": car.id},
        {"$set": car.to_dict()}
    )
```