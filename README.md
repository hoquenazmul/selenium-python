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
              <a href="#" class="card-link" aria-label="another-link">Another link</a>
          </div>
      </div>
    </div>
</body>
</html>
```
**Relative:** Create Xpath from anywhere on a web page or we can directly grab the desire element if we've unique web element using double forward slash (//). It's more safe since in here we don't need to maintain relationship from very first node 'html' to desire element.
```python
# Xpath Formula => //* or tagName[@attribute='attributeValue']
//a[@aria-label='card-link']
```
**Absolute:** It maintains relationships from very first 'html' tag to desire web element using single forward slash(/). If developer modify or remove any web element of absolute xpath in future, it will not work. 
```html
/html/body/div/div/div/h5
```
```
//a[@aria-label='card-link' and @class='card-link']
//a[@aria-label='card-link' or @class='card-link']
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
              <a href="#" class="card-link" aria-label="another-link">Another link</a>
          </div>
      </div>
    </div>
</body>
</html>
```
```python

```
**[⬆ back to top](#table-of-contents)**

## Pytest
```python

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




