# selenium-python

## Table of Contents

1. [WebDriver Setup](#webdriver-setup)
2. [BrowserOptions & DesiredCapabilities](browseroptions--desiredcapabilities)

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
## BrowserOptions & DesiredCapabilities
BrowserOptions or DesiredCapabilities, uesed to customize and configure the default behavior of a browser. Since DesiredCapabilities used for all browsers (common conf.), so it has some limitation because different browser manage their configuration in a different way. To overcome this situation, we have BrowserOptions for each and every browser like ChromeOptions, FirefoxOptions & EdgeOptions etc.
```python
from selenium import webdriver
from selenium.webdriver.chrome.service import Service as ChromeService
from webdriver_manager.chrome import ChromeDriverManager

options = webdriver.ChromeOptions()
options.add_argument('--headless')
options.add_argument("--window-size=1920,1080") # avoid the uncertain window size during headless mode
options.add_argument("--start-maximized") # avoid the uncertain window size during headless mode
options.add_argument("--incognito")
options.add_argument('--ignore-ssl-errors=yes') # avoid the http website open issue
options.add_argument('--ignore-certificate-errors') # avoid the http website open issue
# you can arg by following this
options.headless = True
options.binary_location = 'webdriver-path'

options.add_experimental_option("detach", True) # to stop automatically browser close
desired_device = {"deviceName": "Pixel 5"}
options.add_experimental_option("mobileEmulation", desired_device) # run script in mobile device

caps = webdriver.DesiredCapabilities.CHROME.copy()
caps['acceptInsecureCerts'] = True # solve the unsecured website open problem

driver = webdriver.Chrome(service=ChromeService(ChromeDriverManager().install()), options=options,
                          desired_capabilities=caps)
driver.get('http://www.google.com/')
```






