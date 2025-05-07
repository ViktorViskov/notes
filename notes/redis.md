### docker-compose.yml
```yml
services:
  redis:
    image: redis:8-alpine
    container_name: redis
    restart: unless-stopped
    command: redis-server --save 60 1 --loglevel warning --requirepass Password1! # Save every 60 seconds if at least 1 key changed. Save to /data
    volumes:
      - ./data:/data
    ports:
      - "6379:6379"
```

### redis ORM
```bash
pip install redis-om
```

### context.py
```python
from redis_om import get_redis_connection

CONTEXT = get_redis_connection(
    host="localhost",
    port=6379,
    password="Password1!",
    decode_responses=True,
)

def except_handler(func):
    """
    Decorator to handle exceptions.
    """
    def wrapper(*args, **kwargs):
        try:
            return func(*args, **kwargs)
        except Exception as e:
            print(f"An error occurred: {e}")
            return None
    return wrapper

```

### models.py
```python
from redis_om import Field
from redis_om import HashModel

from db.context import CONTEXT


class UserDb(HashModel):
    username: str = Field(index=True, default="")
    password: str
    email: str
    class Meta:
        database = CONTEXT
```

### user_repo.py
```python

```