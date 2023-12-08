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

- Windows 11, power shell

## Depoy to wsl
- [How to install Linux on Windows using WSL]{:.heading.flip-title}
- Run to powershell as administration
- wsl version set 1 -> 2 : `wsl --set-default-version 2`
- List available Linux distributions : `wsl -l -o`
- Deploying a specific Linux to wsl : `wsl --install -d <distribution name>`
- Recommended version : Ubuntu 22.04.2 LTS
- Status of wsl : `wsl --status`
{:.related-posts.faded}

[How to install Linux on Windows using WSL]: https://learn.microsoft.com/ko-kr/windows/wsl/install
