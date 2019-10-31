---
title: "Mysql创建用户"
date: 2019-10-31T17:53:45+08:00
draft: false
author: "WenpiCai"
tags: ["笔记", "mysql"]
showFullContent: false
---

https://www.cnblogs.com/chanshuyi/p/mysql_user_mng.html

```mysql
创建用户
create user zhangsan identified by 'zhangsan';
开权限
grant all privileges on zhangsanDb.* to zhangsan@'%' identified by 'zhangsan';
flush privileges;
查看权限
show grants for 'zhangsan';
更新密码
update mysql.user set password = password('zhangsannew') where user = 'zhangsan' and host = '%';
flush privileges;
删除用户
drop user zhangsan@'%';
```

