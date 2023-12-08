---
layout: page
title: How to install Linux on Windows using WSL
description: >
  How you install wsl depends on whether you start to deploy linux on window.
hide_description: true
sitemap: false
categories: [window, linux, wsl]
tags:       [window, linux, wsl]
---

## Recommended Specifications

- Windows 11, power shell, Ubuntu 22.04.2 LTS

## Reference site

- [How to install Linux on Windows using WSL]{:.heading.flip-title}

## Deploy to wsl

- Run to powershell as administration
{:.note}

1. wsl version set 1 -> 2 :
  
```sh
wsl --set-default-version 2
```

1. List available Linux distributions

```sh
wsl -l -o
```

1. Deploying a specific Linux to wsl

```sh
wsl --install -d <distribution name>
```

1. Status of wsl
  
```sh
wsl --status
```

{:.related-posts.faded}


[How to install Linux on Windows using WSL]: https://learn.microsoft.com/ko-kr/windows/wsl/install
