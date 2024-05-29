```python
import base64

from cryptography.fernet import Fernet
from cryptography.hazmat.primitives import hashes
from cryptography.hazmat.primitives.kdf.pbkdf2 import PBKDF2HMAC


class Crypter:
    _password: str
    _salt: str
    _key: bytes
    _fernet: Fernet

    def __init__(self, password: str, salt:str = "") -> None:
        self._password = password
        self._salt = salt

        self._init_crypter()
        self._fernet = Fernet(self._key)

    def _init_crypter(self) -> None:        
        kdf = PBKDF2HMAC(
            algorithm=hashes.SHA256(),
            length=32,
            salt=self._salt.encode(),
            iterations=480000,
        )

        self._key = base64.urlsafe_b64encode(kdf.derive(self._password.encode()))

    def encrypt(self, message: str) -> bytes:
        return self._fernet.encrypt(message.encode())

    def decrypt(self, message: bytes) -> str:
        try:
            return self._fernet.decrypt(message).decode()
        
        # if some exeption, return empty string
        except Exception as e:
            # print("Message decoding error")
            return ""
```
[[python]]
[[cryptography]]