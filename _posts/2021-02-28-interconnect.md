---
title: TCP HTTP Socket Socket连接池
categories: [学习笔记]
comments: true
---

## 七层网络模型
首先从网络通信的分层模型讲起：七层模型，亦称OSI(Open System Interconnection)模型。自下而上分为：物理层、数据链路层、网络层、传输层、会话层、表示层和应用层。所有有关通信的都离不开它，介绍下各层所对应的一些协议和硬件

- 7.应用层：例如HTTP SMTP SNMP FTP Telnet SIP SSH NFS RISP XMPP Whois ENRP

- 6.表示层：例如XDR ASN.1 SMB AFP NCP

- 5.会话层：例如ASAP TLS SSH ISO 8327/CCITT X.225 RPC NetBIOS ASP Winsock BSD

- 4.传输层：例如TCP UDP RI SCTP SPX ATP IL

- 3.网络层：例如IP ICMP IGMP IPX BGP OSPF RIP IGRP EIGRP ARP RARP X25

- 2.数据链路层：例如以太网、令牌环、HDLC、帧中继、ISDN、 ATM、 IEEE 802.11、FDDI PPP

- 1.物理层：例如线路、无线电、光纤、信鸽

## TCP和UDP
关于传输层TCP、UDP协议可能我们平时遇见的会比较多，有人说TCP安全的，UDP不安全的，UDP传输比TCP快，为什么呢？先从TCP连接建立的过程开始分析，然后解释UDP和TCP区别。

### TCP的三次握手和四次挥手
略

### TCP和UDP的区别
