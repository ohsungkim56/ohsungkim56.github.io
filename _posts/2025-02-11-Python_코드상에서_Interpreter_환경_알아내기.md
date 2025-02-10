---
title: "Python 코드상에서 Interpreter 환경 알아내기"
author: ohsungkim56

categories:
  [Python]
tags:
  [Python]

toc: true

date: 2025-02-11
# last_modified_at: 2025-01-18
---

## 1. 필요한 이유

Python interpreter 환경에서 특정 코드를 실행해야 하는 경우가 발생하였다.

검색해본 결과 몇가지 방법이 있어, 그중 쓸 수 있는 방법을 정리해둔다.

## 2. 확인해본 방법들

#### 2-1. `sys.flags.interactive`

```python
import sys

def is_interpreter():
    return bool(sys.flags.interactive)

print("Is running in interpreter:", is_interpreter())
```
> Python IDLE : False  
> 
> Notebook : False
> 
> Python 파일 실행 (intelliJ) : False

#### 2-2. `sys.ps1`

```python
import sys
print(sys.ps1)
```
> Python IDLE : >>>   / 
>
> Notebook : In :
>
> Python 파일 실행 (intelliJ) : AttributeError: module 'sys' has no attribute 'ps1'

#### 2-3. `hasattr(__main__, "__file__")`

```python
import __main__
def is_interpreter():
    return not hasattr(__main__, "__file__")

print(is_interpreter())
```

> Python IDLE : True
> 
> Notebook : True
> 
> Python 파일 실행 (intelliJ) : False
