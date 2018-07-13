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