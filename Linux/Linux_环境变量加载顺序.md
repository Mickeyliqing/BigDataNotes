# Linux_环境变量加载顺序

- 用户环境加载变量
当前用户使用 `~/.bashrc`，`~/.bash_profile`。

- 系统环境变量
给系统当前所有用户使用 `/etc/profile`。

任何普通用户在进行登录的时候，都会同时加载以下几个环境变量，按照顺序如下：
1. /etc/profile
2. ~/.bash_profile
3. ~/.bashrc