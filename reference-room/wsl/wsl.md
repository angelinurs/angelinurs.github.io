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

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Recommended Specifications

- Windows 11, power shell, Ubuntu 22.04.2 LTS
{:.related-posts.faded}

## Reference site

- [How to install Linux on Windows using WSL]{:.heading.flip-title} - Microsoft &rarr; WSL
{:.related-posts.faded}

## Deploy to wsl

Run to powershell as administration
{:.note}

- wsl version set 1 -> 2

```sh
$ wsl --set-default-version 2
```

- List available Linux distributions

```sh
$ wsl -l -o
```

- Deploying a specific Linux to wsl

```sh
$ wsl --install -d <distribution name>
```

- Status of wsl
  
```sh
$ wsl --status
```

{:.related-posts.faded}


[How to install Linux on Windows using WSL]: https://learn.microsoft.com/ko-kr/windows/wsl/install
