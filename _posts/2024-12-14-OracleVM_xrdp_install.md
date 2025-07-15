---
title: "[Oracle VM] ubuntu에 xrdp 설치"
author: ohsungkim56

categories:
  [Linux, VM]
tags:
  [Oracle, VM, xrdp, ubuntu]

toc: true

date: 2024-12-14
# last_modified_at: 2025-01-18
---

## 1. xfce 설치

```bash
sudo DEBIAN_FRONTEND=noninteractive apt-get -y install xfce4
sudo apt install xfce4-session
```

## 2. xrdp 설치 및 xrdp 서비스 실행

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

## 3. xrdp 사용자에게 인증서 권한 부여

```bash
sudo adduser xrdp ssl-cert
#info: Adding user `xrdp' to group `ssl-cert' ...
echo xfce4-session > ~/.xsession
# sudo systemctl restart xrdp # 서비스 재시작
```

## 4. 포트 변경

```bash
sudo ls -l /etc/xrdp/xrdp.ini # 설정 파일 권한 확인
# -rw-r--r-- 1 root root 9472 Dec 14 15:45 /etc/xrdp/xrdp.ini
```

```bash
sudo chmod a+w /etc/xrdp/xrdp.ini # 설정 파일 권한 변경
```

```bash
sudo ls -l /etc/xrdp/xrdp.ini
# -rw-rw-rw- 1 root root 9472 Dec 14 15:45 /etc/xrdp/xrdp.ini
```

```bash
sudo vim /etc/xrdp/xrdp.ini
# ;port=3389
# port={변경 할 포트}
# :wq
```

## 5. 서비스 재시작

```
sudo systemctl restart xrdp
```

## 6. iptable 설정

```bash
sudo iptables -I INPUT 5 -m state --state NEW,ESTABLISHED -p tcp -m tcp --dport {변경한 포트} -j ACCEPT
sudo netfilter-persistent save # 저장
```

## 7. 포트 오픈 확인

```bash
sudo netstat -plnt | grep rdp
# tcp6       0      0 ::1:3350  :::*  LISTEN      46043/xrdp-sesman
# tcp6       0      0 :::{변경한 포트 번호}   :::*  LISTEN      46055/xrdp
```

## 8. Oracle 방화벽 포트 오픈

## 9. 원격 데스크톱으로 접속

실행(win+r) > mstsc > 옵션 표시 > 컴퓨터 입력칸에 'IP주소:포트' 입력

## 10. Reference

[Azure VM에 원격 데스크톱 설치 가이드](https://learn.microsoft.com/ko-kr/azure/virtual-machines/linux/use-remote-desktop)

[[Ubuntu Desktop] xrdp 원격 데스크톱 - 반야자비님 블로그](https://blog.banyazavi.com/2022-06-11/xrdp-%EC%9B%90%EA%B2%A9-%EB%8D%B0%EC%8A%A4%ED%81%AC%ED%86%B1)

[2-4. Ubuntu 20.04 xRDP 설정 및 원격 데스크탑 연결](https://sonhc.tistory.com/918)
