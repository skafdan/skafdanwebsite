---
title: "Hosted on My Pi"
date: 2021-06-24T16:10:26+12:00
draft: false
toc: false
images:
tags:
  - Raspberry Pi
---

![ServerPi](/serverPi.jpg)

This site is hosted using ```Apache web server``` on ```Raspberry Pi OS```.

As a brief guide:
1. Ensure all repos and packages are up to date.
2. Install the `Apache` server and enable the `ssl` and `rewrite` modules for 
https support.
3. Enable the service to start at boot.
4. Copy the html files to the webserver directory.


