---
layout: post
title: Scan with trivy using docker
description: >
  Scan with trivy using docker
sitemap: false
hide_last_modified: true
categories: [linux, ubuntu, trivy, docker, scan]
tags:       [linux, ubuntu, trivy, docker, scan]
---

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Recommended Specifications

- jdk : java 17
- os : ubuntu 22.04 LTS or wsl2 ( ubuntu )

## Reference site

- [docker hub trivy] &rarr; docker hub trivy
- [trivy jar] &rarr; Boosting DevOps Security: A Beginner’s Guide to Integrating Trivy with Jenkins”
{:.related-posts.faded}

## scan

### scan vulnerability to local build docker image

```sh
# scan vulnerability to local build docker image
mkdir -p $REPORT_DIRECTORY &&
docker build -t $NAME:$VERSION . &&
docker run \
        -v /var/run/docker.sock:/var/run/docker.sock \
        aquasec/trivy \
        image $NAME:$VERSION \
        --no-progress > $REPORT_DIRECTORY/$NAME:$VERSION-$DATE.txt
```

{:.related-posts.faded}

### scan vulnerability to specific registry docker image

```sh
# scan vulnerability to specific registry docker image
mkdir -p $REPORT_DIRECTORY &&

# Deploy to local as docker
docker login $CONTAINER_REGISTRY -u $ACCESS_KEY --password $SECRET_KEY &&
docker run \
        -v /var/run/docker.sock:/var/run/docker.sock \
        aquasec/trivy \
        image $CONTAINER_REGISTRY/$NAME:$VERSION\
        --no-progress > $REPORT_DIRECTORY/$NAME:$VERSION-$DATE.txt
```

{:.related-posts.faded}

### scan vulnerability to local filesystem jar file

```sh
# scan vulnerability to local filesystem jar file
## c drive mounted on wsl2 in window
WORKSPACE=$2
LOCAL_DATA_PATH=$HOME/trivy/$DATE

mkdir -p $LOCAL_DATA_PATH

cp -r $WORKSPACE $LOCAL_DATA_PATH

docker run aquasec/trivy \
        --rm \
        -v $LOCAL_DATA_PATH/:/root/.cache/ \
        # -v /var/run/docker.sock:/var/run/docker.sock \
        -q image \
        --exit-code 1 \
        --severity CRITICAL \
        --light eclipse-temurin:17.0.9_9-jre-alpine \
        --no-progress > $LOCAL_DATA_PATH/report-$DATE.txt

cp -r $LOCAL_DATA_PATH/report-$DATE.txt $WORKSPACE/
rm -rf $LOCAL_DATA_PATH

echo "Report File name is report-$DATE.txt"
echo "- Done."
```

{:.related-posts.faded}


[docker hub trivy]:https://hub.docker.com/r/aquasec/trivy
[trivy jar]:https://achraf-nhaila.medium.com/boosting-devops-security-a-beginners-guide-to-integrating-trivy-with-jenkins-3c25e44bad06