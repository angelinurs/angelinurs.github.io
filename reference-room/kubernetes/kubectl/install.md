---
layout: page
title: Install and Set Up kubectl on Linux
description: >
  How you install and set up kubectl on Linux.
hide_description: false
sitemap: true
categories: [linux, kubernetes, kubectl]
tags:       [linux, kubernetes, kubectl]
---

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Recommended Specifications

- Ubuntu 22.04.2 LTS

## Reference site

- [Install and Set Up kubectl on Linux]{:.heading.flip-title}

## Deploy to wsl

Run to powershell as administration
{:.note}

- Download
  
    ```sh
    curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
    ```

- Install

    ```sh
    sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
    ```

- Check version

    ```sh
    kubectl version --client --output=yaml
    ```

{:.related-posts.faded}


[Install and Set Up kubectl on Linux]: https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/
