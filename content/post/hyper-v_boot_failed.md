---
title: "Hyper V_boot_failed"
date: 2024-02-29T14:21:44Z
draft: true
---

某些时候虚拟机会对某些ISO引导失败，并最终`Start Pxe over IPv4`并死锁。
确认ISO本身和虚拟机的引导顺序都没问题后，考虑在虚拟机启动时疯狂按F12。