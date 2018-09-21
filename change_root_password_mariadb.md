  
# MySQL修改root密码的4种方法
 
## 方法1： 用SET PASSWORD命令 

首先登录MySQL。 
格式：mysql> set password for 用户名@localhost = password(‘新密码’); 
例子：mysql> set password for root@localhost = password(‘123’);

## 方法2：用mysqladmin 

格式：mysqladmin -u用户名 -p旧密码 password 新密码 
例子：mysqladmin -uroot -p123456 password 123

## 方法3：用UPDATE直接编辑user表 
首先登录MySQL。 
mysql> use mysql; 
mysql> update user set password=password(‘123’) where user=’root’ and host=’localhost’; 
mysql> flush privileges; 
  
## Comparsion

使用update SQL语句是最好的， 因为sql语句可以批量操作，比如：

```bash
MariaDB [mysql]> select user,host,password from user where user='root';
+------+----------------------------------------------+-------------------------------------------+
| user | host                                         | password                                  |
+------+----------------------------------------------+-------------------------------------------+
| root | localhost                                    | *C7126A20C71E6475963DF54B2C8CDA6AA4A2F521 |
| root | cloud-sz-control-b13-01.sz.cloud.genomics.cn | *C7126A20C71E6475963DF54B2C8CDA6AA4A2F521 |
| root | 127.0.0.1                                    | *C7126A20C71E6475963DF54B2C8CDA6AA4A2F521 |
| root | ::1                                          | *C7126A20C71E6475963DF54B2C8CDA6AA4A2F521 |
| root | %                                            | *C7126A20C71E6475963DF54B2C8CDA6AA4A2F521 |
+------+----------------------------------------------+-------------------------------------------+
```

这种情况，update 可以一下子就搞定， 而方法一，方法二都需要多次操作。


# 在丢失root密码的时候
```bash
　　mysqld_safe --skip-grant-tables&

　　mysql -u root mysql

　　mysql> UPDATE user SET password=PASSWORD("new password") WHERE user='root';

　　mysql> FLUSH PRIVILEGES;
```

