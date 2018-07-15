# 2018暑期任务说明文档

注：各阶段具体学习情况详见 LS.md(Learning Stage)

## 任务一：在树莓派上搭建一个Web服务器（Nginx）并能够通过浏览器访问。 

**安装Nginx**：

```
sudo apt-get install nginx
```

**开启Web服务**：

```
sudo service nginx start 
```

**安装Flash支持（可选）：**

```
sudo apt-get install browser-plugin-gnash 
```

打开浏览器，输入树莓派IP，如果看到 **Welcome to nginx!** 就表明Nginx开启成功了！

之后可自行添加php支持和Web应用框架等。

## 任务二：Socket和串口通信 





## 任务三：创建自己的账户并赋予其超级权限

**切换至root用户：**

```
su -
```

**创建账户：**（需要在管理员用户下执行）

```
adduser <用户名>
```

**特别注意：**

在**CentOs**下useradd与adduser是没有区别的都是在创建用户，在home下自动创建目录，没有设置密码，需要使用passwd命令修改密码。
而在**Ubuntu**下useradd与adduser有所不同

1、useradd在使用该命令创建用户是不会在/home下自动创建与用户名同名的用户目录，而且不会自动选择shell版本，也没有设置密码，那么这个用户是不能登录的，需要使用passwd命令修改密码。

2、adduser在使用该命令创建用户是会在/home下自动创建与用户名同名的用户目录，系统shell版本，会在创建时会提示输入密码，更加友好。

**赋予其超级权限：**（需要在管理员用户下执行）

```
usermod -g root <用户名>
```

**检验是否成功赋予：**

切换至该用户：

```
su <用户名>
```

尝试打开只有管理员权限才能打开的目录：

```
nano /etc/passwd
```

如果能打开，则表明已成功将该用户移至管理员组。