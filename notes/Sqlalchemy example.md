#### Models
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
    post_id = Column("post_id", Integer, ForeignKey("posts.id"))
    post = relationship("PostDb", back_populates="comments")
    created_at = Column("created_at", DateTime(), default=current_timestamp())
```

#### Context
```python
from os import getenv
from typing import Generator
from typing import Any

from sqlalchemy import create_engine
from sqlalchemy.orm import Session
from sqlalchemy.orm import sessionmaker

from db.schemes import Base

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
    Base.metadata.create_all(engine)


def get_db() -> Generator[Session, Any, None]:
    with session_maker() as session:
        yield session

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

#### Using (sql like)
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
[[python]]
[[sqlalchemy]]
[[Alembic usage]]