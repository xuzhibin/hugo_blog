---
title: Linux下重置MySQL root密码
date: 2013-08-17
categories:
- server
tags:
- mysql
---
1、停止MySQL服务
执行：


``` bash
/etc/init.d/mysql stop
```

你的机器上也不一定是/etc/init.d/mysql，也可能是/etc/init.d/mysqld

2、跳过验证启动MySQL


``` bash
/usr/local/mysql/bin/mysqld_safe --skip-grant-tables >/dev/null 2>&1 &
```

注：如果mysqld_safe的位置如果和上面不一样需要修改成你的，如果不清楚可以用find命令查找。

3、重置密码
等一会儿，然后执行：


``` bash
/usr/local/mysql/bin/mysql -u root mysql
```

出现mysql提示符后输入：


``` bash
update user set password = Password('要设置的密码') where User = 'root';
```

回车后执行：


``` bash
flush privileges;
```

刷新MySQL系统权限相关的表。再执行：


``` bash
exit;
```

退出。

4、重启MySQL
杀死MySQL进程：


``` bash
killall mysqld
```

重启MySQL：


``` bash
/etc/init.d/mysql start
```

