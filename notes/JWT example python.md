```python
from datetime import datetime

import jwt

  
SECRET_KEY = "SomeRandomSalt"
ALGORITHM = "HS256"

def create(some_id: int, exp: datetime) -> str:
    token_data = {
        "id": some_id,
        "exp": exp
    }

    return jwt.encode(token_data, SECRET_KEY, algorithm=ALGORITHM)
    
def parse(token: str) -> dict | None:
    try:
        token_data = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        return token_data
    except jwt.PyJWTError as e:
        print(e)
        return None
```
[[jwt]]
[[python]]