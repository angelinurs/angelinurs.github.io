---
layout: post
title: Install webmin, sever dashboard.
description: >
  install webmin, sever dashboard
sitemap: false
hide_last_modified: true
categories: [linux, server, dashboard]
tags:       [linux, server, dashboard]
---

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Recommended Specifications

- ubuntu 22.04 live server amd64

## Reference site

- [fabric8 io githup]{:.heading.flip-title}  &rarr; fabric8 io githup tree
- [webmin official]  &rarr; webmin official
- [webmin download]  &rarr; webmin download and install
- [webmin manual]  &rarr; webmin manual install
- [download deb]  &rarr; webmin manual download
{:.related-posts.faded}

### setup

- [webmin download] - install

```sh
curl -o setup-repos.sh https://raw.githubusercontent.com/webmin/webmin/master/setup-repos.sh

sudo /bin/sh -c setup-repos.sh

apt-get install webmin --install-recommends

https://<Your-Server-IP>:10000
```

{:.related-posts.faded}

### setup - manaul

- [webmin manual] - manual download page
- [download deb] - Debian derivatives (Ubuntu, Kali, Parrot, Pop!, Lite, Devuan)
  
```sh
apt-get install --install-recommends ./webmin-current.deb
```

{:.related-posts.faded}

### Access

- `https://<Your-Server-IP>:10000`

{:.related-posts.faded}

### login

- administartor system id/passwd

{:.related-posts.faded}


[webmin official]:https://webmin.com/
[webmin download]:https://webmin.com/download/
[webmin manual]:https://webmin.com/download/#manual
[download deb]:https://www.webmin.com/download/deb/webmin-current.deb