---
title: "iptables 규칙 세부 확인"
author: ohsungkim56

categories:
  [linux]
tags:
  [linux, iptables]

toc: true

date: 2025-10-06
last_modified_at: 2025-10-06
---

## 1. 현재 iptables 규칙 확인

```bash
iptables -L INPUT --line-numbers -n # -L : List, INPUT : input 규칙, --각 규칙 번호 확인, -n : 포트 번호 확인
# Chain INPUT (policy ACCEPT)
# num  target     prot opt source               destination
# 1    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0            state RELATED,ESTABLISHED
# 2    ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0
# 3    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0
# 4    ACCEPT     udp  --  0.0.0.0/0            0.0.0.0/0            udp spt:123
# ...생략...
# 9    REJECT     all  --  0.0.0.0/0            0.0.0.0/0            reject-with icmp-host-prohibited
```

위의 규칙중 3번에 해당하는 
```
3    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0
```
에 의해서 80포트가 ACCEPT 될거라고 예상했는데, 실제로는 9번에 의해서 REJECT 되었다.

## 2. 실제 적용되는 규칙 확인
위에서 80포트가 REJECT 된 이유는 아래와 같다.

```bash
iptables -S
# -P INPUT ACCEPT
# -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
# -A INPUT -p icmp -j ACCEPT
# -A INPUT -i lo -j ACCEPT
# -A INPUT -p udp -m udp --sport 123 -j ACCEPT
# ... 생략 ...
# -A INPUT -p tcp -m tcp --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT
# -A INPUT -j REJECT --reject-with icmp-host-prohibited
```

위의 규칙 중 아래 내용을 확인해보면,
모든 src, dest, port에 대해서 ACCEPT하는 규칙이 lo(=loopback) interface에만 적용되고 있다.
```
-A INPUT -i lo -j ACCEPT
```

## 3. 해결
80 포트에 해당하는 규칙을 추가한다.
단, 맨 마지막에 규칙을 추가하면 REJECT 규칙에 의해 접속이 막히므로 중간에 insert(-I) 한다.
```
iptables -I INPUT 9 -p tcp --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT
```

---

[^1]: [https://stackoverflow.com/questions/35403614/clear-browser-cookies-with-selenium-webdriver-java-bindings#answer-60487685](https://stackoverflow.com/questions/35403614/clear-browser-cookies-with-selenium-webdriver-java-bindings#answer-60487685)
[^2]: [https://chromedevtools.github.io/devtools-protocol/tot/Network/#method-clearBrowserCookies](https://chromedevtools.github.io/devtools-protocol/tot/Network/#method-clearBrowserCookies)
