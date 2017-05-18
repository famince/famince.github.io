---
layout: post
title:  "hello from docker!"
date:   2017-05-16 19:46:52 +0800
output:
  html_document:
      highlight: pygments
---

14年就有了解过docker，后面工作没用到，自己也很懒，没有去接触

公司虚拟机linux内核才2.6,为此专门安装了ubuntu16.4 amd64

然后按照 https://docs.docker.com/engine/installation/linux/ubuntu/


中途换 sohu mirrors 才安装成功

```shell
alex@alex-xiao:~$ sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
78445dd45222: Pull complete 
Digest: sha256:c5515758d4c5e1e838e9cd307f6c6a0d620b5e07e6f927b07d05f6d12a1ac8d7
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.
```
