# selenium-python

## Table of Contents

1. [WebDriver Setup](#webdriver-setup)
2. [Common WebDriver Properties](#common-webdriver-properties)
3. [BrowserOptions & DesiredCapabilities](#browseroptions--desiredcapabilities)
4. [Window Size, Handles & Screenshot](#window-size-handles--screenshot)
5. [Alert, iFrame & Cookie](#alert-iframe--cookie)
6. [ActionChains](#actionchains)
7. [Wait & Select](#wait--select)
8. [Xpath](#xpath)
9. [CSS Selector](#css-selector)
10. [Pytest](#pytest)
11. [Logging](#logging)

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
**[⬆ back to top](#table-of-contents)**

## Common WebDriver Properties
```python
from selenium import webdriver
from selenium.webdriver.chrome.service import Service as ChromeService
from selenium.webdriver.common.by import By
from webdriver_manager.chrome import ChromeDriverManager


driver = webdriver.Chrome(service=ChromeService(ChromeDriverManager().install()))

driver.get('https://google.com')
driver.delete_all_cookies()
driver.implicitly_wait(5)
driver.set_page_load_timeout(30)

driver.title
driver.current_url
```
```python
driver.switch_to.default_content() # window(window_name), alert, iframe(iframe)
element = driver.find_element(By.XPATH, 'xpath')
driver.find_elements()
driver.execute_script('return arguments[0].innerText', element)
```
```python
driver.back()
driver.forward()
driver.refresh()

driver.close()
driver.quit()
```
**[⬆ back to top](#table-of-contents)**

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
driver.get('https://www.google.com/')
```
**[⬆ back to top](#table-of-contents)**

## Window Size, Handles & Screenshot
```python
from selenium import webdriver
from selenium.webdriver.chrome.service import Service as ChromeService
from selenium.webdriver.common.by import By
from webdriver_manager.chrome import ChromeDriverManager

driver = webdriver.Chrome(service=ChromeService(ChromeDriverManager().install()))
driver.get('https://the-internet.herokuapp.com/windows')
driver.find_element(By.LINK_TEXT, 'Click Here').click()
```
```python
driver.set_window_size(width=1920, height=1080)
driver.get_window_size() # {'width': 1920, 'height': 1080}
driver.maximize_window()
driver.minimize_window()
driver.fullscreen_window()
```
```python
driver.current_window_handle # parent window
driver.window_handles # list of all open windows
```
```python
driver.get_screenshot_as_file('test.png')
# or
driver.save_screenshot('test.png')
```
**[⬆ back to top](#table-of-contents)**

## Alert, iFrame & Cookie
```python
# Alert
from selenium import webdriver
from selenium.webdriver.chrome.service import Service as ChromeService
from selenium.webdriver.common.by import By
from webdriver_manager.chrome import ChromeDriverManager

driver = webdriver.Chrome(service=ChromeService(ChromeDriverManager().install()))
driver.get('https://the-internet.herokuapp.com/javascript_alerts')
js_prompt = driver.find_element(By.XPATH, "//button[text() = 'Click for JS Prompt']")

js_prompt.click()
alert = driver.switch_to.alert
print(alert.text)
alert.send_keys("Hello Selenium!")
alert.accept()
alert.dismiss()
```
```python
# iFrame
from selenium import webdriver
from selenium.webdriver.chrome.service import Service as ChromeService
from selenium.webdriver.common.by import By
from webdriver_manager.chrome import ChromeDriverManager

driver = webdriver.Chrome(service=ChromeService(ChromeDriverManager().install()))
driver.get('https://the-internet.herokuapp.com/iframe')
iframe = driver.find_element(By.ID, 'mce_0_ifr')

driver.switch_to.frame(iframe) # frame_name, index also available

para = driver.find_element(By.XPATH, "//p[text() = 'Your content goes here.']")
print(para.text) # Your content goes here.
para.clear()
```
```python
# Cookie
from selenium import webdriver
from selenium.webdriver.chrome.service import Service as ChromeService
from webdriver_manager.chrome import ChromeDriverManager

driver = webdriver.Chrome(service=ChromeService(ChromeDriverManager().install()))
driver.get('https://the-internet.herokuapp.com')

driver.add_cookie({"name": "foo", "value": "bar"})
driver.get_cookie("foo") # bar
driver.get_cookies()
driver.delete_cookie("foo")
driver.delete_all_cookies()
```
**[⬆ back to top](#table-of-contents)**

## ActionChains
```python
clickable = driver.find_element(By.ID, "clickable")

ActionChains(driver)\
    .move_to_element(clickable)\
    .pause(1)\
    .click_and_hold()\
    .pause(1)\
    .send_keys("abc")\
    .perform()
```
```python
# to clear the existing driver state of all the input items
ActionBuilder(driver).clear_actions()
```
>Keyboard Actions
```python
ActionChains(driver)\
    .key_down(Keys.SHIFT)\
    .send_keys("abc")\
    .perform() # ABC
```
```python
ActionChains(driver)\
    .key_down(Keys.SHIFT)\
    .send_keys("a")\
    .key_up(Keys.SHIFT)\
    .send_keys("b")\
    .perform() # Ab
```
```python
ActionChains(driver)\
    .send_keys("ab")\
    .perform() # ab
```
```python
text_input = driver.find_element(By.ID, "textInput")
ActionChains(driver)\
    .send_keys_to_element(text_input, "abc")\
    .perform()
```
> Mouse Actions
```python
clickable = driver.find_element(By.ID, "clickable")
ActionChains(driver)\
    .click(clickable)\
    .perform()
```
```python
clickable = driver.find_element(By.ID, "clickable")
ActionChains(driver)\
    .context_click(clickable)\
    .perform()

ActionChains(driver)\
    .double_click(clickable)\
    .perform()
```
```python
hoverable = driver.find_element(By.ID, "hover")
ActionChains(driver)\
    .move_to_element(hoverable)\
    .perform()
```
```pytnon
draggable = driver.find_element(By.ID, "draggable")
droppable = driver.find_element(By.ID, "droppable")
ActionChains(driver)\
    .drag_and_drop(draggable, droppable)\
    .perform()
```
**[⬆ back to top](#table-of-contents)**

## Wait & Select
> Implicit Wait
```python
driver.implicitly_wait(5)
```
> Explicit Wait
```python
driver = Chrome()
element = WebDriverWait(driver, timeout=3).until(lambda dv: dv.find_element(By.TAG_NAME, "p"))
assert element.text == "Hello World"
```
```python
clickable = driver.find_element(By.ID, "test")
wait = WebDriverWait(driver, timeout=10)
wait.until(expected_conditions.element_to_be_clickable(clickable)) 
# available => alert is present, element is visible
```
> Fluent Wait
```python
driver = Firefox()
driver.get("http://somedomain/url_that_delays_loading")
wait = WebDriverWait(driver, timeout=10, poll_frequency=1, ignored_exceptions=[ElementNotVisibleException])
element = wait.until(EC.element_to_be_clickable((By.XPATH, "//div")))
```
#### Select 
```html
<select name="lang" id="lang">
    <option selected="selected" value="python">Python</option>
    <option value="java">Java</option>
    <option value="javascript">JavaScript</option>
</select>
```
```python
element = driver.find_element(By.ID, "lang")
select = Select(element)
option_list = select.options # get all the option list
```
```python
select.select_by_visible_text('Java') # Java
select.select_by_value('javascript') # JavaScript
select.select_by_index(0) # Python
```
**[⬆ back to top](#table-of-contents)**

## Xpath
```html
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Xpath & CSS Selector</title>
</head>
<body>
    <div role="group" aria-label="card-wrapper">
      <div class="card" style="width: 18rem;">
          <div class="card-body">
              <h5 class="card-title">Card title</h5>
              <h6 class="card-subtitle mb-2 text-muted">Card subtitle</h6>
              <p class="card-text">Some quick example text to build on the card title</p>
              <a href="#" class="card-link" aria-label="card-link">Card link</a>
              <a href="#" id="another-76980" class="card-link" aria-label="another-link">Another link</a>
          </div>
      </div>
    </div>
</body>
</html>
```
**Relative:** Create Xpath from anywhere on a web page or we can directly grab the desire element if we've unique attribute using double forward slash (//). It's more safe since in here we don't need to maintain relationship from very first node 'html' to desire element.
```python
# Xpath Formula => //* or tagName[@attribute='attributeValue']
//a[@aria-label = 'card-link']
//div[@class='card-body']//h5
//div[@class='card-body']//h5[@class='card-title']
//div[@role='group']//h5
```
**Absolute:** It maintains relationships from very first 'html' tag to desire web element using single forward slash(/). If developer modify or remove any web element of absolute xpath in future, it will not work. 
```
/html/body/div/div/div/h5
```
**AND/OR:**
```
//a[@aria-label = 'card-link' and @class='card-link']
//a[@aria-label = 'card-link' or @class='card-link']
``` 
**text():** only used for text and it should be matched properly whatever mentioned on DOM. 
```
//p[text() = 'Some quick example text to build on the card title']
```
**contains():** used for text & html attribute both and match fully or partially with text or attribute value. No need to match exactly whatever mentioned on DOM, partial match should be okay
```
//p[contains(text(), 'on the card title')]
//p[contains(@class, 'card-text')]
```
**starts-with():** used to match from the starting characters of a text or attribute value 
```
//p[starts-with(text(), 'Some quick example')]
//a[starts-with(@id, 'another')]
```
**Ancestor/পূর্বপুরুষ:** It refers all previous elements if they have child-parent relationships
```html
//p[@class='card-text']//ancestor::html <!-- 1 of 1 -->
//p[@class='card-text']//ancestor::head <!-- 0 of 0 -->
```
**Descendant/বংশধর:** It refers all next elements if they have child-parent relationships
```html
//div[@role = 'group']//descendant::a <!-- 1 of 2 -->
//div[@role = 'group']//descendant::a[contains(@id, 'another')] <!-- 1 of 1 -->
```
**Preceding/পূর্ববর্তী:** It refers all previous elements if the don't have child-parent relationships.
```html
//p[@class='card-text']//preceding::html <!-- 0 of 0 -->
//p[@class='card-text']//preceding::head <!-- 1 of 1 -->
```
**Following Sibling:** It refers only the same level html elements. not upper or lower level.
```html
//h5[@class = 'card-title']//following-sibling::h6 <!-- 1 of 1 -->
//h5[@class = 'card-title']//following-sibling::a <!-- 1 of 2 -->
//h5[@class = 'card-title']//following-sibling::a[1] <!-- 1 of 1 -->
```
**Child:** represents all the child elements
```html
//div[@role= 'group']//child::a[1]
//div[@class= 'card-body']//child::a
//div[@class= 'card-body']//child::p
```
**Parent:** represents only the immediate parent element
```html
//a[contains(@id, 'another')]//parent::div <!-- refer immediate one (.card-) -->
```
**Table**
```html
<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Mark</td>
      <td>1102</td>
    </tr>
    <tr>
      <td>Jacob</td>
      <td>1101</td>
    </tr>
  </tbody>
</table>
```
```html
//table//tbody//tr//td[1] <!-- Mark -->
//table//tbody//tr[2]//td[1] <!-- Jacob -->
//table//tbody//tr[2]//td[position()= 2] <!-- 1101 -->
<!-- or -->
//table//tbody//tr[2]//td[2]
```
**[⬆ back to top](#table-of-contents)**

## CSS Selector
```html
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Xpath & CSS Selector</title>
</head>
<body>
    <div role="group" aria-label="card-wrapper">
      <div class="card" style="width: 18rem;">
          <div class="card-body">
              <h5 class="card-title">Card title</h5>
              <h6 class="card-subtitle mb-2 text-muted">Card subtitle</h6>
              <p class="card-text">Some quick example text to build on the card title</p>
              <a href="#" class="card-link" aria-label="card-link">Card link</a>
              <a href="#" id="another-76980" class="card-link" aria-label="another-link">Another link</a>
          </div>
      </div>
    </div>
</body>
</html>
```
> CSS Selector Formula: tagName[attribute=value]
```html
a[aria-label = 'card-link']
a[aria-label='card-link'][class = 'card-link'] <!-- indicates multiple attr -->
```
**Child Element**
```html
div[role='group']>div>div>p <!-- '>' indicates immediate child only -->
div[role='group'] div p <!-- ' ' indicates immediate or not immediate child -->
```
**Class**
```html
<!-- '.' indicates class -->
.card-title
h5.card-title
a.card-link[aria-label='another-link']
h5[class='card-title']
a[id='another-76980'][class='card-link']
.card-subtitle.mb-2.text-muted <!-- indicates who has multiple classes -->
.card-link#another-76980 <!-- indicates who has class & id both -->
```
**ID**
```html
<!-- '#' indicates ID -->
#another-76980
a#another-76980
a#another-76980[aria-label='another-link']
a[id='another-76980']
a[id='another-76980'][class='card-link']
a#another-76980.card-link <!-- indicates who has class & id both -->
```
**[⬆ back to top](#table-of-contents)**

## Pytest
To execute code using pytest, need to follow the convention below
|         |Example            |
|---------|-------------------|
|file     |`test_login`       |
|method   |`"test_login()"`   |
|class    |`TestLogin`        |
```console
<!-- pytest commands -->

$ pytest -v -s
$ pytest -k "mobile"
$ pytest -k "not mobile"

$ pytest -m "regression"
$ pytest -m "not regression"

$ pytest module_name.py
```
Register the new mark in pytest.ini file
```bat
[pytest]

markers =
    smoke: representing smoke test cases
    regression: representing regression test cases
```
**Fixture**
```python
# file -> conftest.py

import pytest

@pytest.fixture()
def setup():
    print("Executing First")
    yield
    print("Executing Last")
```
```python
# file -> test_login.py 

def test_login(setup):
    print("login success")

# Executing First
# login success
# Executing Last
```
```python
# A good example
import pytest

@pytest.fixture()
def driver():
    driver = webdriver.Chrome()
    driver.implicitly_wait(5)
    driver.maximize_window()
    yield driver 
    driver.close()


def test_app_url(driver):
    driver.get("https://google.com")
```

Fixture as Data Provider
```python
# file -> conftest.py

import pytest

@pytest.fixture(params=["firefox", "chorme"])
def browser(request):
    return request.param

@pytest.fixture(scope='class')
def setup():
    print("Executing First")
    yield
    print("Executing Last")

@pytest.fixture(scope='class')
def clear_cache():
    print("cache cleared")
```
```python
# file -> test_login.py

import pytest

@pytest.mark.usefixtures('clear_cache', 'setup')
class TestLogin:
    def test_initiate_browser(self, browser):
        print("Initiated", browser)

    def test_login(self):
        print("Login Test")

# cache cleared
# Executing First
# Initiated firefox
# Initiated chorme
# Login Test
# Executing Last
```
**Fixture Scope**
|scope|definition
|-----|---------
|@pytest.fixture()|default => Function Level (each function)
|@pytest.fixture(scope='class')|Class Level
|@pytest.fixture(scope='module')| Module Level
|@pytest.fixture(scope='session')|for Each pytest execution or `pytest command`

For Reporting
```console
$ pip install pytest-html

<!-- command to generate report -->
$ pytest --html=report.html --css=highcontrast.css --css=accessible.css
```
**[⬆ back to top](#table-of-contents)**

## Logging
```python
import logging

def test_log():
    log = logging.getLogger(__name__) # to get the current module
    file_handler = logging.FileHandler("app.log", mode='w') # w for new log after removing old log
    file_format = logging.Formatter('%(asctime)s | %(levelname)s | %(name)s | %(message)s')
    file_handler.setFormatter(file_format)

    log.addHandler(file_handler)
    log.setLevel(logging.DEBUG)

    log.info("Info Log")
    log.debug("Debug Log")
    log.warning("Warning Log")
    log.critical("Critical Log")
    log.error("Error Log")
```
**[⬆ back to top](#table-of-contents)**




