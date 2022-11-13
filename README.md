# selenium-python

## Table of Contents

1. [WebDriver Setup](#webdriver-setup)

## WebDriver Setup
```python
# using webdriver-manager
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager
from webdriver_manager.firefox import GeckoDriverManager
from webdriver_manager.microsoft import IEDriverManager, EdgeChromiumDriverManager

driver = webdriver.Chrome(service=Service(ChromeDriverManager().install())) # Chrome
driver = webdriver.Firefox(service=Service(GeckoDriverManager().install())) # Firefox
driver = webdriver.Ie(service=Service(IEDriverManager().install())) # IE
driver = webdriver.Edge(service=Service(EdgeChromiumDriverManager().install())) # Edge
```
```python
# without webdriver-manager
from selenium import webdriver
from selenium.webdriver.chrome.service import Service

service = Service("webdriver path")
driver = webdriver.Chrome(service=service)
driver = webdriver.Firefox(service=service)
driver = webdriver.Ie(service=service)
driver = webdriver.Edge(service=service)
```






