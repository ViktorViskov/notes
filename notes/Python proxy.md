## Using by requests
#### Private
```python
from requests import Session

session = Session()
session.headers = {
    'User-Agent': "user_agent"
}

session.proxies = {
    'http': f'http://{proxy_user}:{proxy_pass}@{proxy_addr}:{proxy_port}',
    'https': f'http://{proxy_user}:{proxy_pass}@{proxy_addr}:{proxy_port}'
}

session.get("https://google.com")
```
#### Public
```python
from requests import Session

session = Session()
session.headers = {
    'User-Agent': "user_agent"
}

session.proxies = {
    'http': f'http://{proxy_addr}:{proxy_port}',
    'https': f'http://{proxy_addr}:{proxy_port}'
}

session.get("https://google.com")
```

## Selenium
#### Private
```python
seleniumwire_options = {
    'proxy': {
        'http': f'http://{p_user}:{p_pass}@{p_addr}:{p_port}', 
        'https': f'http://{p_user}:{p_pass}@{p_addr}:{p_port}',
        'no_proxy': 'localhost,127.0.0.1' # excludes
    }
}
```
#### Using public proxy
```python
seleniumwire_options = {
    'proxy': {
        'http': f'http://{p_addr}:{p_port}', 
        'https': f'http://{p_addr}:{p_port}',
        'no_proxy': 'localhost,127.0.0.1' # excludes
    }
}
```