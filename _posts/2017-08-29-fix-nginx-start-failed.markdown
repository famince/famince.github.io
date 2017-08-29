---
layout: post
title:  "记一次 nginx 启动失败"
date:   2017-08-29 18:13:29 +0800
output:
  html_document:
      highlight: pygments
---


1.启动nginx
```{r, engine='sh', count_lines}
$ /opt/third-party/nginx/sbin/nginx -c /opt/deploy/env/eastsea/app_integration/umms_rws/current/config/nginx.conf 启动报错
nginx: [emerg] bind() to 0.0.0.0:8082 failed (98: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:8082 failed (98: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:8082 failed (98: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:8082 failed (98: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:8082 failed (98: Address already in use)
nginx: [emerg] still could not bind()
```


2.查看所有端口使用情况, 可以发现8082的端口已经在监听;
```{r, engine='sh', count_lines}
$ netstat -ntpl
Proto Recv-Q Send-Q Local Address               Foreign Address             State       PID/Program name   
tcp        0      0 0.0.0.0:8112                0.0.0.0:*                   LISTEN      17676/nginx         
tcp        0      0 0.0.0.0:8082                0.0.0.0:*                   LISTEN      8644/nginx          
tcp        0      0 0.0.0.0:8242                0.0.0.0:*                   LISTEN      16760/nginx         
tcp        0      0 0.0.0.0:8212                0.0.0.0:*                   LISTEN      1589/nginx          
tcp        0      0 0.0.0.0:22                  0.0.0.0:*                   LISTEN      -                   
tcp        0      0 0.0.0.0:8122                0.0.0.0:*                   LISTEN      20437/nginx         
tcp        0      0 0.0.0.0:2812                0.0.0.0:*                   LISTEN      -                   
tcp        0      0 0.0.0.0:8132                0.0.0.0:*                   LISTEN      20803/nginx         
tcp        0      0 0.0.0.0:8232                0.0.0.0:*                   LISTEN      1367/nginx          
tcp        0      0 0.0.0.0:8202                0.0.0.0:*                   LISTEN      21186/nginx
```

3.查看端口8082使用进程
```{r, engine='sh', count_lines}
$ lsof -i:8082
COMMAND  PID     USER   FD   TYPE  DEVICE SIZE/OFF NODE NAME
nginx   8644 optilink    7u  IPv4 6711860      0t0  TCP *:us-cli (LISTEN)
nginx   8645 optilink    7u  IPv4 6711860      0t0  TCP *:us-cli (LISTEN)
```

4.查看进程启动时间：
```{r, engine='sh', count_lines}
$ ps -p 8645 -o lstart
                 STARTED
Tue Aug 29 17:39:42 2017
```

5.查看子进程的父进程，参数为子进程号
```{r, engine='sh', count_lines}
$ ps -f 8644
UID        PID  PPID  C STIME TTY      STAT   TIME CMD
optilink  8644 19146  0 17:39 ?        S      0:00 nginx: worker process
```
或者
```{r, engine='sh', count_lines}
$ ps -o ppid= 8645
19146
```

6.查看进程树，参数为父进程号
```{r, engine='sh', count_lines}
$ pstree -p 19146
nginx(19146)─┬─nginx(8644)
             └─nginx(8645
```
可以试下 pstree -p 1

7.kill nginx 进程，然后启动nginx进程;
