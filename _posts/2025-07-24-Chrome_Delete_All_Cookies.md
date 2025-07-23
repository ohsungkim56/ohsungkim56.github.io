---
title: "Selenium + Chrome 모든 cookie 삭제"
author: ohsungkim56

categories:
  [Web]
tags:
  [Web, Chrome, Selenium]

toc: true

date: 2025-07-24
# last_modified_at: 2025-07-23
---

## 1. 현재 tab에서 접속중인 cookie 삭제

```python
from selenium import webdriver
driver = webdriver.Chrome()
driver.get("https://naver.com")
driver.driver.delete_all_cookies() # 현재 열린 탭에서 접근한 도메인의 cookie 삭제
```

## 2. 모든 cookie 삭제
위 방법으로 했을때 cloudflare의 cf_clearence 쿠키가 남아있었다. 

(cf_clearence cookie에 partion key site 설정이 있어서 인듯?)

CDP(Chrome Devtools Protocol)를 이용하여 모든 cookie를 삭제 할 수 있다.[^1][^2]
```bash
driver.execute_cdp_cmd("Network.clearBrowserCookies", {}) # (A) working in 135, 138
driver.execute_cdp_cmd("Storage.clearCookies", {}) # (B) working in 138
```
A, B 둘 다 cookie 삭제하는 기능인데, 버전별로 동작에 약간 차이가 있다.
가능하면 A를 사용하도록 하자.

다른 CDP cmd은 아래 사이트에서 볼 수 있다.

[https://chromedevtools.github.io/devtools-protocol/](https://chromedevtools.github.io/devtools-protocol/)

---

[^1]: https://stackoverflow.com/questions/35403614/clear-browser-cookies-with-selenium-webdriver-java-bindings#answer-60487685

[^2]: https://chromedevtools.github.io/devtools-protocol/tot/Network/#method-clearBrowserCookies

