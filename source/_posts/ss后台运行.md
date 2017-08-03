---
title: ss后台运行
date: 2017-04-23 15:38:56
tags: 
- linux
catalogies:
- Tips
---

如果只是` sslocal -c `的话，关掉bash就会退出，及时加上`&`也无效。谷歌后发现以下命令：

```
sudo sslocal -c [config] -d [start|stop|restart]
```
这样就能在后台运行ss