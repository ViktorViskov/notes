### Packages
```bash
sqlalchemy
alembic
psycopg2-binary #postgres
pymysql #mysql/mariadb
```
### Connection strings

#### **MariaDB**
```
mssql+pymssql://username:password@localhost/dbname
```

#### **Postgres**
```
postgresql://username:pass@localhost:5432/db_name
```

#### **Sqlite**
```
sqlite:///path/to/database.db
```

#### **MSSQL**
```
mssql+pymssql://username:password@localhost/dbname
```

### Example with object binding and DTO
#### `db_context.py`
```python
from typing import Generator
from typing import Any
from os import getenv

from sqlalchemy import create_engine
from sqlalchemy.orm import Session
from sqlalchemy.orm import sessionmaker

from sqlalchemy.ext.declarative import declarative_base


Base = declarative_base()

DB_CONNECTION_STRING = getenv("DB_CONNECTION_STRING")
# DB_CONNECTION_STRING = "sqlite:///./test.db"

if DB_CONNECTION_STRING is None:
    raise Exception("DB connection string not providet")

engine = create_engine(DB_CONNECTION_STRING, echo=False, pool_pre_ping=True, pool_recycle=3600) #reconect after 1 hour
session_maker = sessionmaker(bind=engine, expire_on_commit=False)

def create_db() -> None:
    """
    Creates the database tables by calling `Base.metadata.create_all(engine)`.
    """
    Base.metadata.create_all(engine)


def get_db() -> Generator[Session, Any, None]:
    """
    Returns a generator that yields a SQLAlchemy session. This session should be used for all database interactions within the current request context.
    """
    with session_maker() as session:
        yield session
        

def auto_create_db():
    """
    Automatically creates the database if it doesn't already exist.

    This function attempts to connect to the database engine. If an exception is raised, it means the database doesn't exist yet, so it creates the database using the connection string and database name extracted from the `CONNECTION_STRING` variable.

    After creating the database, it calls the `create_db()` function to perform any additional setup or initialization for the database.
    """
    try:
        con = engine.connect()
        create_db()
        con.close()

    except Exception as _:
        connection_string, db_name = DB_CONNECTION_STRING.rsplit("/", 1)

        tmp_engine = create_engine(connection_string)
        with tmp_engine.begin() as session:
            session.exec_driver_sql(f"CREATE DATABASE `{db_name}`")

        create_db()
```
#### `models/db.py`
```python
from sqlalchemy import Column
from sqlalchemy import Integer
from sqlalchemy import String
from sqlalchemy import DateTime
from sqlalchemy import ForeignKey
from sqlalchemy.orm import relationship
from sqlalchemy.sql.functions import current_timestamp

from context import Base

from models.dto import UserDto
from models.dto import GroupDto


class Group(Base):
    __tablename__ = 'groups'
    id = Column("id", Integer(), primary_key=True, autoincrement=True)
    name = Column("name", String(32))
    
    owner_id = Column("owner_id", Integer(), ForeignKey("users.id"))
    owner = relationship("User", foreign_keys=[owner_id], back_populates="group_owners")
    
    leader_id = Column("leader_id", Integer(), ForeignKey("users.id"))
    leader = relationship("User", foreign_keys=[leader_id], back_populates="group_leaders")
    
    members = relationship("User", foreign_keys="[User.group_id]", back_populates="group")
    
    updated_at = Column("updated_at", DateTime(), default=current_timestamp())
    created_at = Column("created_at", DateTime(), default=current_timestamp())
    
    @property
    def dto(self) -> GroupDto:
        return GroupDto(
            id=self.id,
            name=self.name,
            owner=self.owner.dto,
            leader=self.leader.dto,
            members=[member.dto for member in self.members],
            updated_at=self.updated_at,
            created_at=self.created_at
        )
    
class User(Base):
    __tablename__ = 'users'
    id = Column("id", Integer(), primary_key=True, autoincrement=True)
    name = Column("name", String(32))
    surname = Column("surname", String(32))
    
    group_id = Column("group_id", Integer(), ForeignKey("groups.id"))
    group = relationship("Group", foreign_keys=[group_id], back_populates="members")
    
    group_owners = relationship("Group", foreign_keys="[Group.owner_id]" , back_populates="owner")
    group_leaders = relationship("Group", foreign_keys="[Group.leader_id]" ,back_populates="leader")
    
    updated_at = Column("updated_at", DateTime(), default=current_timestamp())
    created_at = Column("created_at", DateTime(), default=current_timestamp())
    
    @property
    def dto(self) -> UserDto:
        return UserDto(
            id=self.id,
            name=self.name,
            surname=self.surname,
            group_id=self.group.id,
            updated_at=self.updated_at,
            created_at=self.created_at
        )
```
#### `models/dto.py`
```python
from __future__ import annotations
from datetime import datetime


class UserDto:
    id: int
    name: str
    surname: str
    group_id: int
    updated_at: datetime
    created_at: datetime
    
    def __init__(self, id: int, name: str, surname: str, group_id: int, updated_at: datetime, created_at: datetime):
        self.id = id
        self.name = name
        self.surname = surname
        self.group_id = group_id
        self.updated_at = updated_at
        self.created_at = created_at
    
    
class GroupDto:
    def __init__(self, id: int, name: str, owner: UserDto, leader: UserDto, members: list[UserDto], updated_at: datetime, created_at: datetime):
        self.id = id
        self.name = name
        self.owner = owner
        self.leader = leader
        self.members = members
        self.updated_at = updated_at
        self.created_at = created_at
    
    id: int
    name: str
    owner: UserDto
    leader: UserDto
    members: list[UserDto]
    updated_at: datetime
    created_at: datetime
```
#### `repository.py`
```python
from models.db import Group
from models.db import User
from context import session_maker
from sqlalchemy import update
from sqlalchemy.orm import joinedload


def create_user(name: str, surname: str, group_id: int) -> User:
    with session_maker.begin() as session:
        user = User(name=name, surname=surname, group_id=group_id)
        session.add(user)
        return user


def get_user_by_id(user_id: int) -> User | None:
    with session_maker() as session:
        user = session.query(User).filter(User.id == user_id).first()
        return user

# Get with all child objects
def get_users() -> list[User]:
    with session_maker() as session:
        users = (
            session.query(User)
            .options(
                joinedload(User.group)
                .joinedload(Group.members)
            ).options(
                joinedload(User.group_leaders)
                .joinedload(Group.leader)
            ).options(
                joinedload(User.group_owners)
                .joinedload(Group.owner)
            )
            .all()
        )
        return users

def create_group(name: str) -> Group:
    with session_maker.begin() as session:
        group = Group()
        group.name = name
        session.add(group)
        return group


def get_group_by_id(group_id: int) -> Group | None:
    with session_maker() as session:
        group = session.query(Group).filter(Group.id == group_id).first()
        return group


def get_groups() -> list[Group]:
    with session_maker() as session:
        groups = session.query(Group).options(
                joinedload(Group.members)
                .joinedload(User.group)
            ).all()
        return groups


def set_group_owner(group_id: int, user_id: int) -> None:
    with session_maker.begin() as session:
        query = update(Group).where(Group.id == group_id).values(owner_id=user_id)
        session.execute(query)


def set_group_leader(group_id: int, user_id: int) -> None:
    with session_maker.begin() as session:
        query = update(Group).where(Group.id == group_id).values(leader_id=user_id)
        session.execute(query)
```

### Example with out objects binding
#### `models/db.py`
```python
from sqlalchemy import Column
from sqlalchemy import Integer
from sqlalchemy import String
from sqlalchemy import DateTime
from sqlalchemy import ForeignKey
from sqlalchemy import Text
from sqlalchemy.orm import Session
from sqlalchemy.orm import relationship
from sqlalchemy.orm import declarative_base
from sqlalchemy.sql.functions import current_timestamp


base = declarative_base()

class UserDb(base):
    __tablename__ = 'users'
    id = Column("id", Integer, primary_key=True, autoincrement=True, index=True)
    username = Column("username", String(128))
    email = Column("email", String(128))
    password = Column("password", String(128))
    posts = relationship("PostDb", back_populates="owner")
    comments = relationship("CommentDb", back_populates="owner")
    updated_at = Column("updated_at", DateTime(), default=current_timestamp())
    created_at = Column("created_at", DateTime(), default=current_timestamp())

class PostDb(base):
    __tablename__ = 'posts'
    id = Column("id", Integer, primary_key=True, autoincrement=True, index=True)
    title = Column("title", String(128))
    content = Column("content", String(1024))
    image_url = Column("image_url", String(128))
    owner_id = Column("owner_id", Integer, ForeignKey("users.id"))
    owner = relationship("UserDb", back_populates="posts")
    comments = relationship("CommentDb", back_populates="post")
    updated_at = Column("updated_at", DateTime(), default=current_timestamp())
    created_at = Column("created_at", DateTime(), default=current_timestamp())

class CommentDb(base):
    __tablename__ = 'comments'
    id = Column("id", Integer, primary_key=True, autoincrement=True, index=True)
    text = Column("title", String(512))
    owner_id = Column("owner_id", Integer, ForeignKey("users.id"))
    owner = relationship("UserDb", back_populates="comments")
    post_id = Column("post_id", Integer, ForeignKey("posts.id"), ondelete='SET NULL')
    post = relationship("PostDb", back_populates="comments")
    created_at = Column("created_at", DateTime(), default=current_timestamp())
```

#### `db_context.py`
```python
from os import getenv
from typing import Generator
from typing import Any

from sqlalchemy import create_engine
from sqlalchemy.orm import Session
from sqlalchemy.orm import sessionmaker

from db.schemes import base

# string example
# sqlite:///./todo.db
# mariadb+pymysql://user:password@localhost:3306/db_name
# postgresql://user:pass@localhost:5432/db_name
CONNECTION_STRING = getenv("DB_CONNECTION_STRING")
if not CONNECTION_STRING:
    raise Exception("DB connection string not providet")

engine = create_engine(CONNECTION_STRING, echo=False, pool_pre_ping=True, pool_recycle=3600) #reconect after 1 hour
session_maker = sessionmaker(bind=engine, expire_on_commit=False)

def create_db() -> None:
    base.metadata.create_all(engine)


def get_db() -> Generator[Session, Any, None]:
    with session_maker() as session:
        yield session

#Dont work with postgres
def auto_create_db():
    try:
        con = engine.connect()
        con.close()

    except Exception as _:
        connection_string, db_name = CONNECTION_STRING.rsplit("/", 1)

        tmp_engine = create_engine(connection_string)
        with tmp_engine.begin() as session:
            session.exec_driver_sql(f"CREATE DATABASE `{db_name}`")

        create_db()
```

#### `repository.py`
```python
from sqlalchemy import Delete
from sqlalchemy import Update
from sqlalchemy.sql.functions import current_timestamp

from models.db import User
from db.context import session_maker


def add(name: str, surname: str, role: User.Role, email: str, password: str)-> User:
    with session_maker.begin() as session:
        user = User()
        user.name = name
        user.surname = surname
        user.role = role
        user.email = email
        user.password = password

        session.add(user)
        session.flush()

        return user

def update(id: int, name: str, surname: str, role: User.Role, email: str, password: str) -> None:
    with session_maker.begin() as session:
        session.execute(Update(User).where(User.id == id).values({
            User.name: name,
            User.surname: surname,
            User.role: role,
            User.email: email,
            User.password: password,
            User.updated_at: current_timestamp()
        }))

def delete(id: int) -> None:
    with session_maker.begin() as session:
        session.execute(Delete(User).where(User.id == id))
    
def get(limit:int = 1000, offset: int = 0) -> list[User]:
    with session_maker() as session:
        return session.query(User).limit(limit).offset(offset).all()
```

[[orm]]
[[Alembic]]
[[python]]
