# 1. Linux环境变量加载的顺序
* 用户环境变量 ：仅仅只是当前用户使用 ~/.bashrc　　 ~/.bash_profile

* 系统环境变量 ：给当前系统中的所有用户使用 /etc/profile

* 任何普通用户在进行登录的时候：会同时加载几个环境变量的配置文件：

* 按顺序：

1、/etc/profile

2、~/.bash_profile

3、~/.bashrc

# 2. 查看linux 下现有用户和权限

-  查看所有用户：cat /etc/passwd

- 彻底删除某一个用户：userdel -r hadoop (hadoop是用户)

-  查看用户组：cat /etc/group

- 删除用户组：groupdel hadoop (hadoop是用户组)

# 3. 新增用户，赋值root 权限

* 新增用户 ：adduser hadoop

* 赋值root 权限：修改/etc/sudoers文件，找到下面一行，在root下面添加一行“hadoop ALL=(ALL) ALL”.

**各个用户切换命令**

* 普通用户切换到root 用户：su 和su -

* **su 和su - 的区别**

```markdown
su切换到 root 用户之后仍然保持旧的（或者说原始用户的）环境.
su - 是创建一个新的环境（由 root 用户 `~/.bashrc` 文件所设置的环境），相当于使用 root 用户正常登录（从登录屏幕登录）。
```

* root 用户切换到普通用户：su - hadoop

* 普通用户切换到普通用户：su - lq (当前登录用户是hadoop)

# 4. 关闭防火墙

- 查看：systemctl status firewalld

- 关闭：systemctl stop firewalld

- 开启：systemctl start firewalld

- 禁止开机启动：systemctl disable firewalld

- 开机启动：systemctl enable firewalld

# 5. 本机免密登录

1. cd ~./.ssh/

2. ssh-keygen -t rsa    (一直回车)

3. cat id_rsa.pub >>  authorized_keys

4. chmod 600 authorized_keys

# 6. 三台机器配置免密登录


- 三台机器的hosts 分别配置如下：

1. 192.168.48.130 master

2. 192.168.48.131 worker_01

3. 192.168.48.132 worker_02

- 48.131机器下：（/home/hadoop/.ssh）目录

scp id_rsa.pub root@192.168.48.130:/home/hadoop/.ssh/id_rsa_01
  
- 48.132机器下：（ /home/hadoop/.ssh）目录

scp id_rsa.pub root@192.168.48.130:/home/hadoop/.ssh/id_rsa_02
  
- 48.130机器下：（/home/hadoop/.ssh）目录

1. cat id_rsa_01 >> authorized_keys 

2. cat id_rsa_02 >> authorized_keys 

3. scp authorized_keys hadoop@worker_01:/home/hadoop/.ssh/

4. scp authorized_keys hadoop@worker_02:/home/hadoop/.ssh/

- 48.130,48.131,48.132下

确保都执行了：

chmod 600 authorized_keys

- 然后每台机器都执行：(三台机器都要执行)

1. ssh master

2. ssh worker_01

3. ssh worker_02



