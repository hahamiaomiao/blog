---
title: TCP HTTP Socket Socket连接池
categories: [学习笔记]
comments: true
---

## 七层网络模型

首先从网络通信的分层模型讲起：七层模型，亦称 OSI(Open System Interconnection)模型。自下而上分为：物理层、数据链路层、网络层、传输层、会话层、表示层和应用层。所有有关通信的都离不开它，介绍下各层所对应的一些协议和硬件

- 7.应用层：例如 HTTP SMTP SNMP FTP Telnet SIP SSH NFS RISP XMPP Whois ENRP

- 6.表示层：例如 XDR ASN.1 SMB AFP NCP

- 5.会话层：例如 ASAP TLS SSH ISO 8327/CCITT X.225 RPC NetBIOS ASP Winsock BSD

- 4.传输层：例如 TCP UDP RI SCTP SPX ATP IL

- 3.网络层：例如 IP ICMP IGMP IPX BGP OSPF RIP IGRP EIGRP ARP RARP X25

- 2.数据链路层：例如以太网、令牌环、HDLC、帧中继、ISDN、 ATM、 IEEE 802.11、FDDI PPP

- 1.物理层：例如线路、无线电、光纤、信鸽

## TCP 和 UDP

关于传输层 TCP、UDP 协议可能我们平时遇见的会比较多，有人说 TCP 安全的，UDP 不安全的，UDP 传输比 TCP 快，为什么呢？先从 TCP 连接建立的过程开始分析，然后解释 UDP 和 TCP 区别。

### TCP 的三次握手和四次挥手

略

### TCP 和 UDP 的区别

- TCP 是面向连接的，而 UDP 是面向无连接的。
- TCP 仅支持单播传输，UDP 提供了单播，多播，广播的功能
- TCP 的三次握手保证了连接的可靠性；UDP 是无连接的、不可靠的一种数据传输协议，首先不可靠体现在无连接上，通信都不需要建立连接，对接收到的数据也不发送确认信号，发送端不知道数据是否会正确接收。
- UDP 的头部开销比 TCP 的更小，数据传输速率更高，实时性更好。
