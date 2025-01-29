---
title: "[Oracle VM] ssh port 변경"
excerpt: ""
author: ohsungkim56

categories:
  [Linux, VM]
tags:
  [Oracle, VM, xrdp, ubuntu]

toc: true

date: 2024-12-15
# last_modified_at: 2025-01-18
---

## 0. 작업 내용

- VM에서 SSH 포트 추가
- VM에서 22 포트는 VM에서 유지, Oracle FW에서 22포트 막음.

## 1. ssh 서비스에 포트 추가

```bash
sudo systemctl edit ssh.socket
# [Socket]
# ListenStream=
# ListenStream={추가할 포트}
# ListenStream=22
```

## 2. iptables 에 포트 추가

```bash
sudo iptables -I INPUT 5 -m state --state NEW,ESTABLISHED -p tcp -m tcp --dport {추가할 포트} -j ACCEPT
```

## 2. 서비스 재시작

```bash
sudo systemctl restart ssh.socket
```

## 3. 포트 오픈 확인

```bash
sudo systemctl status ssh.socket
# ● ssh.socket - OpenBSD Secure Shell server socket
# ...
#      Listen: [::]:{추가한 포트} (Stream)
#              [::]:22 (Stream)
# ...

```

```bash
sudo netstat -plnt | grep 추가한 포트
# tcp6       0      0 :::{추가한 포트}    :::*    LISTEN      1/init
```

## 3. oracle 방화벽 해제

- 22 포트 삭제

- {추가한 포트} 추가

## 4. Reference

[stackoverflow how-to-change-the-ssh-server-port-on-ubuntu ](https://serverfault.com/questions/1159599/how-to-change-the-ssh-server-port-on-ubuntu)

[sonhc님 블로그](https://sonhc.tistory.com/915)
