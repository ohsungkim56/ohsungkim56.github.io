---
title: "Python을 사용하여 Windows에서 화면 캡쳐"
author: ohsungkim56

categories:
  [Python]
tags:
  [Python]

toc: true

date: 2025-04-20
# last_modified_at: 2025-03-03
---

Windows환경에서 이미지 처리에 사용하기 위해서 특정 프로그램의 화면을 캡쳐하는 기능이 필요하였다.
관련 내용을 정리하여 아래와 같이 기록해둔다.

### 1. Windows API를 사용하여 캡쳐

ChatGPT에 물어보면 다음과 같은 방법을 알려준다.
``` Python
wDC = win32gui.GetWindowDC(self.hwnd)
dcObj = win32ui.CreateDCFromHandle(wDC)
cDC = dcObj.CreateCompatibleDC()
dataBitMap = win32ui.CreateBitmap()
dataBitMap.CreateCompatibleBitmap(dcObj, self.window_w, self.window_h)
cDC.SelectObject(dataBitMap)
cDC.BitBlt((0, 0), (self.window_w, self.window_h), dcObj, (self.window_x, self.window_y), win32con.SRCCOPY)

signedIntsArray = dataBitMap.GetBitmapBits(True)
img = np.frombuffer(signedIntsArray, dtype='uint8')
img.shape = (self.window_h, self.window_w, 4)

dcObj.DeleteDC()
cDC.DeleteDC()
win32gui.ReleaseDC(self.hwnd, wDC)
win32gui.DeleteObject(dataBitMap.GetHandle())
```

그런데 일부 프로그램의 경우, 캡쳐에 문제가 있었다...
예를 들어, `Windows 11 계산기`, `Discord`를 캡쳐한 후 이미지를 확인해보면 검은색 이미지만 나왔다.
캡쳐되지 않는 이유는 계산기는 `UWP`, Discord는 `Electron`인데, 이런 경우는 GPU 가속으로 화면을 그리기 때문에,
디바이스 컨텍스트(DC)에서 화면의 비트맵 데이터를 얻을 수 없기 때문이라고 한다.. (위의 코드에서 BitBlt 부분)

이를 위해 아래 방법을 추가하였다.


### 2. 모니터 전체 화면을 캡쳐 후, 필요한 부분만 잘라내기

1. Windows API를 사용하여 전체 화면을 캡쳐한다.
2. Windows API를 사용하여 특정 프로그램의 width와 height를 가져온다.
3. 전체화면에서 필요한 부분만 잘라낸다.

여기서 고려해야할 점은,
1. 내가 캡쳐하고 싶은 프로그램이 최소화가 되어 있을수 있다.
2. 다른 프로그램에 의해 일부가 가려져 있을 수 있다.

1번의 경우, Window가 캡쳐할 수 없는 상태라면 예외를 던지도록 만들었다.

2번의 경우, `SetForegroundWindow` windows API를 사용해서 foregorund로 가져올 수 있을줄 알았는데...

A 프로그램이 B프로그램에 대해서 `SetForegroundWindow`를 호출하려면,
B 프로그램에서 `AllowSetForegroundWindow` API 으로 `SetForegroundWindow`을 사용할 수 있는 프로그램을 A로 설정해줘야 한다.

이걸 우회할 수 있는 방법은 아래와 같은 방법이다.
`alt`키를 전달하여 현재 프로그램에대한 focus를 빼고, `SetForegroundWindow`를 호출하여 해결할 수 있었다.
(이유는 모르겠지만 ineterpreter에서 사용할 때만 문제가 있었다)

아래와 같은 코드로 해결하였다.


``` python
 def bring_window_foreground(self):
  # If in an interpreter environment, bring the window to the foreground.
  # https://stackoverflow.com/questions/14295337/win32gui-setactivewindow-error-the-specified-procedure-could-not-be-found
  # https://stackoverflow.com/questions/63648053/pywintypes-error-0-setforegroundwindow-no-error-message-is-available
  if WindowCapture.is_interpreter():
      import win32com.client
      shell = win32com.client.Dispatch("WScript.Shell")
      shell.SendKeys('%')
  win32gui.SetForegroundWindow(self.hwnd)
  time.sleep(0.1)
```

### 3. 코드 정리
아래 위치에 import 해서 사용할 수 있도록 정리해두었다.

[https://github.com/ohsungkim56/WindowCaptureUtil](https://github.com/ohsungkim56/WindowCaptureUtil)

## 4. 사용 방법
{% include embed/notebook.html src="/assets/html/2025-04-20/WindowCapture.html" full_content="true" %}
