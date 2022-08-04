**1. 一切问题先检查客户端、`swoole_tracker`扩展、服务端版本是否一致，是否为最新发布版本；
2. 客户端Agent服务是否启动；
3. 安装扩展完成以后是否重启客户端 Server 服务或 php-fpm 服务；**
>[success] swoole_tracker扩展的发布版本可能会出现比客户端、服务端高，这个不影响。

* 查看客户端版本 `ps -ef | grep agent`

![](images/screenshot_1574762030104.png)

* 查看 `swoole_tracker` 扩展版本

```ini
;php.ini不要忘了添加
extension=swoole_tracker.so
apm.enable=1
apm.sampling_rate=100
```

>[info] cli模式 php --ri swoole_tracker
![](images/screenshot_1566981931425.png)

>[success] fpm模式 phpinfo()
![](images/screenshot_1566981935839.png)

* 查看服务端版本

![](images/screenshot_1565061881319.png)

[TOC]

## 1. 找不到对应应用

* 查看项目是否正确，自动创建的应用会放到默认项目中
* 检查该应用是否存在合并应用或应用黑名单中
* 检查客户端`swoole_tracker` 配置是否正确，参考安装客户端->安装扩展
* 检查客户端是否给该服务端上报

## 2. 调用统计/链路追踪无信息

* 检查对应后台地址(IP或域名)是否正确，防火墙、安全组中端口是否开放
>[danger] 端口在配置文件`/opt/swoole/config/config_port.conf`中
* 检查客户端进程是否存在
* 检查客户端`swoole_tracker`配置是否正确，参考安装客户端->安装扩展
* 存在脏数据缓存，等待5-10分钟（之前逻辑为客户端上报的时候会创建文件缓存，每五分钟删除一次，重新安装服务端后客户端文件缓存未删除，出现脏数据缓存，导致短时间内无法上报，现已将缓存写入内存中，重装服务端后**重启服务端fpm和重启swoole-admin服务就能正常接收数据**，无需等待）
* 检查采样率`apm.sampling_rate`

## 3. Service应用无调用统计、链路追踪信息

* 检查代码使用是否正确，参考上报数据
* 检查客户端服务名和服务端创建是否一致
* 同问题2

------
>[danger] 如果以上2，3两个问题，所有步骤尝试过后都无效，请点击swoole-admin后台的**首页**、**应用监控**和**应用追踪**，查看是否有弹窗提醒，没有请[咨询客服](contact-us.md)

## 4. 管理NodeAgent守护进程

目前NodeAgent采用系统的守护进程管理，如果要管理NodeAgent的状态：

* 对于采用openrc和类似sysvinit的系统（如Debian 7 "Wheezy", Ubuntu 14.04 "Trusty"（默认不安装systemd）, CentOS(RHEL) 6, Fedora 14, Gentoo（可选）, Archlinux（可选）, Alpine Linux）使用命令

```bash
/etc/init.d/node-agent start
/etc/init.d/node-agent stop
/etc/init.d/node-agent restart
```

来启动/停止/重启NodeAgent（非root用户需要sudo）

* 对于采用systemd的系统（Debian 8 "Jessie"及以后, Ubuntu 15.04 "Vivid"及以后, CentOS(RHEL) 7及以后, Fedora 15及以后, Gentoo（可选）, Archlinux（可选））使用命令

```bash
systemctl start node-agent
systemctl stop node-agent
systemctl restart node-agent
```

来启动/停止/重启NodeAgent（非root用户需要sudo）

## 5. 增加调试器中的相关代码后报错：Fatal error: Uncaught Exception: plz set apm.enable_memcheck=1 which in php.ini

解决方法：在 `php.ini` 中添加配置： `apm.enable_memcheck=1`

>[info] 从2.3.3版本以后默认关闭调试功能，需要手动进行配置。或者使用远程调试

## 6. 重装服务端并清空数据库后，没有上报信息等情况

从2.3.3版本开始有部分信息缓存在php内存中，服务端重装后，数据对应不上导致部分数据获取失败，所以客户端需要重启fpm进程

## 7. 机器信息无上报信息

1. 检查网络是否通畅
2. 查看客户端日志 `/opt/swoole/logs/NodeAgent-sysinfoerr.log` 是否存在，存在查看内容，是否有上报失败字样
3. 服务端地址是否正确

## 8. 进程列表无信息

1. 查看客户端node-agent进程是否存在
2. 查看客户端本地是否有pid文件，路径：`/var/run/swoole_tracker/`下的cli和fpm文件夹中
3. pid文件对应的进程是否正常

## 9. 加载扩展后报错
[扩展安装问题](扩展安装问题.md)

## 11. 客户端运行报错 sw_get_entrypoint()：ERROR：mkdir error, make sure that start the agent first (Premission denied).

请使用 root 用户启动 Agent服务，没有启动 Agent 服务并且不是使用 root 用户启动时会报这个错误

## 12. 报错：PHP Startup: apm.enable and apm.enable_malloc_hook can't be turned on together, reset apm.enable=0

开启了`apm.enable_malloc_hook =1`之后其他的功能均不可用，只能进行内存泄漏检测。