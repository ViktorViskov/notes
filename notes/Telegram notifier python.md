```python
from requests import post
from requests import RequestException


class TelegramNotifier:
    """Telegram notifier class"""
    token: str
    chat_id: str
    base_url: str

    def __init__(self, chat_id: str, token: str) -> None:
        self.chat_id = chat_id
        self.token = token
        
        self.base_url = f"https://api.telegram.org/bot{self.token}"


    def send_message(self, message: str) -> None:
        """Method to send messange"""

        # API endpoint URL
        url = f'{self.base_url}/sendMessage'

        # Parameters for the message to be sent
        payload = {
            'chat_id': self.chat_id,
            'text': message,
            'parse_mode': 'Markdown'
        }

        try:
            post(url, data=payload, timeout=15)
        except RequestException as e:
            print(f"Failed to send message. Exception: {e}")

    def send_image(self, image: bytes, text:str = '') -> None:
        """Method to send image"""
        url = f'{self.base_url}/sendPhoto'

        files = {"photo": image}
        data = {
            "chat_id": self.chat_id,
            "caption": text,
        }

        try:
            post(url, data=data, files=files, timeout=15)
        except RequestException as e:
            print(f"Failed to send message. Exception: {e}")
```

[[telegram]]
[[python]]
[[notifier]]