# 一. user 管理参数解释
- useradd
```
useradd [option] username

[option]:
-d<登入目录> 指定用户登入时的目录。

-g<群组> 初始群组。

-G<群组> 非初始群组。

-m 自动创建用户的家目录。

-M 不要创建用户的家目录。

-N 不要创建以用户名称为名的群组。

-s 指定用户登入后所使用的shell。
```
- usermod
```
usermod [option] <params>

-c<备注>：修改用户帐号的备注文字；

-d<登入目录>：修改用户登入时的目录；

-e<有效期限>：修改帐号的有效期限；

-f<缓冲天数>：修改在密码过期后多少天即关闭该帐号；

-g<群组>：修改用户所属的群组；

-G<群组>: 修改用户所属的附加群组；

- aG<群组>: 添加用户所属的附加群组

-l<帐号名称>：修改用户帐号名称；

-L：锁定用户密码，使密码无效；

-s<shell>：修改用户登入后所使用的shell；

-u<uid>：修改用户ID；

-U:解除密码锁定。
```

# 二. groupadd参数解释
```
groupadd [-g gid ] groupname
-g：指定新建工作组的id, 缺省值 可不写
```

- groupmod -n newName oldName
    - 修改组名
