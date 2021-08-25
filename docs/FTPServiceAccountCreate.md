# FTP服务器搭建

## 启动IIS服务

1. 打开控制面板

​	win + R 打开命令栏 输入 control 打开控制面板

![](./FTPServiceAccountCreate.assets/3.png)

2. 打开程序和机能

![](./FTPServiceAccountCreate.assets/4.png)

3. 打开右上角的windows的机能有效化和无效化，然后打开IIS服务

   ![](./FTPServiceAccountCreate.assets/6.png)

   ![](./FTPServiceAccountCreate.assets/7.png)

   加载完成后可以在命令栏中输入services.msc查看IIS服务是否启动成功

   ![](./FTPServiceAccountCreate.assets/8.png)

## 创建FTP服务器

1.  在命令栏输入compmgmt.msc ，打开电脑管理

   ![](./FTPServiceAccountCreate.assets/10.png)

2.  打开服务器管理

   ![](./FTPServiceAccountCreate.assets/12.png)

3. 在连接处右击，选择创建FTP服务器

   ![](./FTPServiceAccountCreate.assets/13.png)

4. 输入服务器名字，选择服务器路径文件

   ![](./FTPServiceAccountCreate.assets/14.png)

![](D:\05_git\linqstudy\docs\FTPServiceAccountCreate.assets\image-20210825142707259.png)

![](.\FTPServiceAccountCreate.assets\17.png)

FTP服务器创建完成

## 为服务器设置指定用户

1. 创建测试用户

   ![](./FTPServiceAccountCreate.assets/18.png)

   在用户列表中右击

   ![](./FTPServiceAccountCreate.assets/19.png)

   ![](./FTPServiceAccountCreate.assets/20.png)

   测试账户创建完成

2. 指定账户

   打开FTP认证

   ![](./FTPServiceAccountCreate.assets/26.png)

   将匿名认证状态设置为无效

   ![](./FTPServiceAccountCreate.assets/27.png)

   打开基本设定

   ![](./FTPServiceAccountCreate.assets/21.png)

   ![](./FTPServiceAccountCreate.assets/22.png)

   ![](./FTPServiceAccountCreate.assets/24.png)

   ![](./FTPServiceAccountCreate.assets/25.png)

   点击OK完成账户指定

