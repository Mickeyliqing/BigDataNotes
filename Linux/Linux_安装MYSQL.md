# Linux_安装MYSQL

## 准备安装包
**（Linux下MySQL5.6与MySQL5.7安装方法略有不同）**

mysql-5.6.23-linux-glibc2.5-x86_64.tar.gz

## 开始安装

### 第一步

- 查找并删除mysql 有关的文件。

```xshell
find / -name mysql
rm -rf 上边查找到的路径，多个路径用空格隔开#或者下边一条命令即可
```

（实际安装中并未执行这一步）

### 第二步

- 上传包到指定位置，解压。

### 第三步

- 检查是否有mysql 用户组和mysql 用户。

```xshell
groups mysql
```

### 第四步

- 添加mysql 用户组和mysql 用户。

```xshell
groupadd mysql
useradd -r -g mysql mysql
```

### 第五步

- 进入mysql 目录，更改权限。

```xshell
cd mysql/
chown -R mysql:mysql ./
```

### 第六步

- 执行安装脚本。

```xshell
./scripts/mysql_install_db --user=mysql
```

### 第七步

- 修改当前目录的拥有者为root 用户，修改data 目录的拥有者为mysql。

```xshell
chown -R root:root ./
chown -R mysql:mysql data
```

### 第八步

- 更改mysql 密码。

先启动mysql

```xshell
./support-files/mysql.server start
```

若启动有报错，先查找是否已经有mysql 进程，若有先终止,然后在启动。

```xshell
ps aux|grep mysql
kill -9 上边的进程号#或者下边一条命令即可杀掉所有MySQL进程
ps aux|grep mysql|awk '{print $2}'|xargs kill -9
//再次启动
./support-files/mysql.server start
```

### 第九步

- mysql 启动之后，更改密码。

```xshell
./bin/mysqladmin -u root -h localhost.localdomain password 'root'
```

### 第十步

- 更改之后登录。

```xshell
./bin/mysql -h127.0.0.1 -uroot -proot
```



