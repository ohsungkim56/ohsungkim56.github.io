---
title: "Selenium + Chrome 삽질 정리"
author: ohsungkim56

categories:
  [Web]
tags:
  [Web, Chrome, Selenium]

toc: true

date: 2025-07-21
last_modified_at: 2025-07-23
---

## 1. Selenium Manager로 driver 자동 관리

Selenium 4.6 이상에서 Selenium Manager가 자동으로 driver를 관리해준다.[^1]

selenium 과거 버전을 사용중이라면 일부 코드 수정이 필요할 수 있다.

```python
from selenium import webdriver
driver = webdriver.Chrome() # driver 설정이 없어도 사용 할 수 있다.
driver.get("https://naver.com")
```

## 2. `--load-extension` 옵션 (확장 프로그램 로드)
137 이상(25.06~)에서 `--load-extension` CLI 옵션이 삭제되었다.[^2]

아래와 같은 옵션을 덧붙이면, `--load-extension`를 사용할 수 있다고 한다. [^3]

```bash
google-chrome-stable --disable-features=DisableLoadExtensionCommandLineSwitch --load-extension=MyExt.crx
```

## 3. `--remote-debug-address` 옵션
Chromium에서 `--remote-debuggin-address` 옵션이 삭제되었다.[^4]

언제 삭제된건지는 정확히 알 수 없지만, 23년도 (110 버전) 무렵에 삭제된 것으로 보인다.

chrome 135.0.7049.84 버전 기준, `--remote-debuggin-address` 옵션를 넣고 chrome을 실행시키면 무시된다.

![img](/assets/img/2025-07-21/chrome_remote_debugging_address_option.png)

함께 사용하던 `--remote-debugging-port` 옵션은 아직 유효하다.

즉, **localhost에서 devtools에 접속하는건 가능**하고, 외부 호스트에서 접속하는 것만 막혔다.

위와 같이 chrome을 실행 시키고, 같은 host에서 아래와 같이 python 코드를 실행 하면 접속이 가능하다.
```bash
google-chrome-stable --headless --disable-dev-shm-usage --no-sandbox --remote-debugging-port=9999 1>/dev/null 2>&1 &
```

```python
from selenium import webdriver

options = webdriver.ChromeOptions()
options.add_experimental_option('debuggerAddress', '127.0.0.1:9999')
driver = webdriver.Chrome(options=options)
```

## 4. Old version chrome, 
Google에서는 공식적으로 chrome 구버전을 제공하지 않는다.

아래 위치에서 구버전 linux deb 파일을 다운로드 받을 수 있다.
```
https://mirror.cs.uchicago.edu/google-chrome/pool/main/g/google-chrome-stable/
```

## 5. Old Chrome Driver 
chrome driver는 아래 URL에서 버전만 변경해서 사용하면 된다.
```
https://storage.googleapis.com/chrome-for-testing-public/135.0.7049.84/win64/chromedriver-win64.zip
https://storage.googleapis.com/chrome-for-testing-public/135.0.7049.84/win32/chromedriver-win32.zip
https://storage.googleapis.com/chrome-for-testing-public/135.0.7049.84/linux64/chromedriver-linux64.zip
```

114 버전까지는 아래 위치에서도 다운로드 받을 수 있다.
```
https://chromedriver.storage.googleapis.com/index.html
```

## 5. Linux 환경에서 Chromium 설치
Docker + Ubuntu 환경에서 Chromium 설치가 필요했는데, 결과적으로 실패했다.

1. Chromium 배포 방식이 apt에서 snap으로 변경되었다.[^5]
2. snap을 사용하려면 시스템이 systemd으로 init 되어야 한다.
3. Docker의 Ubuntu 베이스 이미지는 별도의 init 프로세스 없이 동작 (PID 1이 사용자 프로세스)

실패한 이유는 위와 같이, 내 환경에서는 snap을 사용할 수 없었기 때문이다.

snap 없이 설치 할 수 있는 다른 방법이 있긴하다.[^6]

WSL2 ubuntu 환경(내부적으로 docker에 올라가는듯)도 마찬가지로 systemd가 아닌데, MS에서 systemd 으로 변경하는 가이드가 제공된다[^7]

내 경우는, systemd로 변경 및 snap 설치하였으나, chromium 설치시 에러가 발생하여 진행 할 수 없었다.

---

[^1]: https://www.selenium.dev/documentation/selenium_manager

[^2]: https://winaero.com/google-releases-chrome-137-with-new-features-and-security-enhancements/#Removal_of_--load-extension_CLI_Flag

[^3]: https://stackoverflow.com/questions/79657438/how-to-load-extensions-programmatically-in-chrome-version-137/79660303#79660303

[^4]: https://issues.chromium.org/issues/40242234

[^5]: https://snapcraft.io/blog/chromium-in-ubuntu-deb-to-snap-transition

[^6]: https://askubuntu.com/questions/1204571/how-to-install-chromium-without-snap

[^7]: https://learn.microsoft.com/en-us/windows/wsl/systemd
