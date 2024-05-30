```python
from seleniumwire.undetected_chromedriver import Chrome
from seleniumwire.undetected_chromedriver import ChromeOptions
from selenium.webdriver import Firefox, FirefoxOptions


def create_browser() -> Chrome:
    options = ChromeOptions()
    options.add_argument("--disable-notifications")
    options.add_argument("--disable-blink-features=AutomationControlled") 
    options.add_argument('--headless')
    options.add_argument('--ignore-certificate-errors')
    options.add_argument('--no-sandbox')
    options.add_argument('--disable-dev-shm-usage')
    options.add_argument('--window-size=1366,760')
    options.add_argument('--disable-gpu')
    options.add_argument("--blink-settings=imagesEnabled=false") 
    options.add_experimental_option("prefs", {
        "download.default_directory": "/dev/null"
    })
    driver = Chrome(options=options)
    driver.set_page_load_timeout(60)
    
    return driver

def create_firefox_browser() -> Firefox:
    options = FirefoxOptions()
    options.headless = True
    options.add_argument("--disable-notifications")
    options.add_argument('--ignore-certificate-errors')
    options.add_argument('--disable-dev-shm-usage')
    options.add_argument('--disable-gpu')
    options.add_argument("--blink-settings=imagesEnabled=false") 
    options.set_preference("browser.download.folderList", 2) #2 custom location
    options.set_preference("browser.download.dir", "/dev/null")
    options.set_preference("browser.download.manager.showWhenStarting", False)
    options.set_preference("browser.helperApps.neverAsk.saveToDisk", "application/octet-stream")
    options.set_preference("browser.helperApps.neverAsk.openFile", "application/octet-stream")
    driver = Firefox(options=options)
    driver.set_page_load_timeout(60)

    return driver
```

#### Using auth proxy
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

#### Assign to seleniumwire
```python
driver = Chrome(options=options, seleniumwire_options=seleniumwire_options)
```

#### Base usage
```python
driver.find_element(By.CSS_SELECTOR, 'button[type="submit"]')
driver.find_element(By.ID, 'button[type="submit"]')
driver.switch_to.frame(self.driver.find_element(By.ID, "main-iframe"))
driver.execute_script("console.log('Hello world')")
```

[[python]]
[[selenium]]
[[parsing]]