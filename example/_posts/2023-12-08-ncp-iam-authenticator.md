---
layout: post
title: ncp-iam-authenticator
description: >
  How you install and set up How you install and set up kubectl on Linux.
  How to get kube-config.yml using ncp-iam-authenticator
sitemap: false
hide_last_modified: true
categories: [linux, kubernetes, kubectl]
tags:       [linux, kubernetes, kubectl]
---

## Recommended Specifications

- Ubuntu 22.04.2 LTS

## Reference site

- [ncp-iam-authenticator setup]{:.heading.flip-title} - gov-ncp-docs &rarr; kubernetes
- [setup to access key and secret key]{:.heading.flip-title} - gov-ncp-docs &rarr; kubernetes
{:.related-posts.faded}

## Setup to ncp-iam-authenticator

- Download

```sh
$ curl -o ncp-iam-authenticator \
       -L https://github.com/NaverCloudPlatform/ncp-iam-authenticator/releases/latest/download/ncp-iam-authenticator_linux_amd64
```

- Add Permission

```sh
$ chmod +x ./ncp-iam-authenticator
```

- Add path

  ```sh
  $ mkdir -p $HOME/bin && cp ./ncp-iam-authenticator $HOME/bin/ncp-iam-authenticator && export PATH=$PATH:$HOME/bin
  ```

- Add bashrc

```sh
$ echo 'export PATH=$PATH:$HOME/bin' >> ~/.bash_profile
```

- Check binary

```sh
$ cp-iam-authenticator help
```

{:.related-posts.faded}

## Configure access key and secret key to ncp-iam-authenticator

- Create configure file
  
```sh
# file: `~/.ncloud/configure`
[DEFAULT]
ncloud_access_key_id = ACCESSKEYACCESSKEYAC
ncloud_secret_access_key = SECRETKEYSECRETKEYSECRETKEYSECRETKEYSECR
ncloud_api_url = https://ncloud.apigw.gov-ntruss.com

[project]
ncloud_access_key_id = ACCESSKEYACCESSKEYAC
ncloud_secret_access_key = SECRETKEYSECRETKEYSECRETKEYSECRETKEYSECR
ncloud_api_url = https://ncloud.apigw.gov-ntruss.com
```

- Where is key path

```text
gov-ncp > 마이페이지 > 계정 관리 > 인증키 관리
```

{:.related-posts.faded}

## Create kubeconfig.yaml

- ClusterUuid
  
```text
console > Service > VPC > Kubernetes Service > Clusters > cluster-name
```

- Region : KR,KRS
- Download

```sh
$ ncp-iam-authenticator create-kubeconfig --region KR --clusterUuid <cluster-uuid> --output kubeconfig.yaml
```

{:.related-posts.faded}

## Install kubectl

- [Install and Set Up kubectl on Linux]{:.heading.flip-title} &rarr; kubernetes

{:.related-posts.faded}

## Alias

```sh
# file: `~/.bashrc`
alias kube='kubectl --kubeconfig /path/to/kubeconfig.yaml'
```

{:.related-posts.faded}

## append ncp Container Registry

```sh
$ kube create secret docker-registry regcred \
            --docker-server=<docker-server> \
            --docker-username=<api-access-key> \
            --docker-password=<api-secret-key> \
            --docker-email=<docker-email>
```

{:.related-posts.faded}


[ncp-iam-authenticator setup]: https://guide-gov.ncloud-docs.com/docs/k8s-iam-auth-ncp-iam-authenticator
[setup to access key and secret key]: https://guide-gov.ncloud-docs.com/docs/k8s-iam-auth-kubeconfig
[Install and Set Up kubectl on Linux]: ../../reference-room/kubernetes/kubectl/install.md
