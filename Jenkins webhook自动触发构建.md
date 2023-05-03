# Jenkins+Gitlab webhook触发自动构建项目

* 效果：只要Gitlab仓库代码更新，Jenkins自动拉取代码，完成构建任务。无需手动点击“立即构建”

* 需求场景：

  1、项目代码的更新迭代较多，每次点击比较麻烦

  2、更新的可能不是代码，可能是一些资源（比如：静态文件等）

### 安装配置Gitlab

Yum安装即可 ， 过程 略

Gitlab平台root用户密码配置为12345678

##### 创建组

![image-20210404222805242](assets/Jenkins webhook自动触发构建/image-20210404222805242.png)

![image-20210404222827306](assets/Jenkins webhook自动触发构建/image-20210404222827306.png)

![image-20210404222903725](assets/Jenkins webhook自动触发构建/image-20210404222903725.png)

##### 创建一个项目

![image-20210404222934618](assets/Jenkins webhook自动触发构建/image-20210404222934618.png)

![image-20210404223032550](assets/Jenkins webhook自动触发构建/image-20210404223032550.png)

在项目中随便新建个文件，当作代码文件

![image-20210404223239413](assets/Jenkins webhook自动触发构建/image-20210404223239413.png)

### 安装配置Jenkins

-----------------------------------------------------------------------------------------------

安装过程略

1.安装jdk

2.安装Tomcat

3.安装Maven（可选，不确定是否编译）

4.配置环境变量

5.启动

--------------------------------------------------------------------------------------------------------

##### 记得配置jdk和maven

![image-20210404223509638](assets/Jenkins webhook自动触发构建/image-20210404223509638.png)

![image-20210404223559996](assets/Jenkins webhook自动触发构建/image-20210404223559996.png)

##### 安装Gitlab hooks plugins插件

因为要用gitlab hook自动拉取代码的功能，需要安装GItlab hooks插件，才具有自动构建的功能

去“插件管理”页面，“可选插件”，搜索“Gitlab Hook Plugin和Gitlab Plugin”，点击“直接安装即可”

**注意：安装过程和网络有关。网络必须顺畅。且能正常连同国外Jenkins网站，才能下载成功**

![image-20210404224242800](assets/Jenkins webhook自动触发构建/image-20210404224242800.png)

##### 新建Gitlab webhook相关项目

![image-20210404223639981](assets/Jenkins webhook自动触发构建/image-20210404223639981.png)

![image-20210404223821668](assets/Jenkins webhook自动触发构建/image-20210404223821668.png)

Jenkins具体配置

![image-20210404224328316](assets/Jenkins webhook自动触发构建/image-20210404224328316.png)

来到Gitlab的test1项目中，复制拉取地址

![image-20210404224421039](assets/Jenkins webhook自动触发构建/image-20210404224421039.png)

粘贴到

![image-20210404224522677](assets/Jenkins webhook自动触发构建/image-20210404224522677.png)

出现一堆报红，正常！因为需要配置私钥和公钥

需要把Jenkins服务器的私钥，配置到test1项目中。把Jenkins服务器的公钥，配置到GItlab的服务里面。

这样拉取就可以免密了！

![image-20210404224803046](assets/Jenkins webhook自动触发构建/image-20210404224803046.png)

```shell
[root@jenkins-server ~]# useradd jenkins 
[root@jenkins-server ~]# su - jenkins 
[jenkins@jenkins-server ~]$ ssh-keygen 
[jenkins@jenkins-server ~]$ cat .ssh/id_rsa #查看jenkins用户的私钥
```

![image-20210404224921005](assets/Jenkins webhook自动触发构建/image-20210404224921005.png)

![image-20210404225000791](assets/Jenkins webhook自动触发构建/image-20210404225000791.png)

看到仍然报红，将jenkins服务器上面的jenkins用户的公钥添加到gitlab中

![image-20210404225109521](assets/Jenkins webhook自动触发构建/image-20210404225109521.png)

![image-20210404225126475](assets/Jenkins webhook自动触发构建/image-20210404225126475.png)

![image-20210404225147143](assets/Jenkins webhook自动触发构建/image-20210404225147143.png)

```shell
登录到jenkins服务器中
[jenkins@jenkins-server ~]$ cat .ssh/id_rsa.pub #查看jenkins用户的公钥
```

![image-20210404225222508](assets/Jenkins webhook自动触发构建/image-20210404225222508.png)

![image-20210404225259649](assets/Jenkins webhook自动触发构建/image-20210404225259649.png)

##### 构建触发器

![image-20210404225803947](assets/Jenkins webhook自动触发构建/image-20210404225803947.png)

![image-20210404225539959](assets/Jenkins webhook自动触发构建/image-20210404225539959.png)

**要记录下上边的URL和认证密钥，切换到gitlab，找到对应的git库点击setting --> webhook ,填写以下内容**

地址：http://192.168.153.149:8080/jenkins/project/webhook

Secret token：68ed645e3f6f51103fb32189d804498b

##### 配置GItlab

![image-20210404225934931](assets/Jenkins webhook自动触发构建/image-20210404225934931.png)

![image-20210404230006031](assets/Jenkins webhook自动触发构建/image-20210404230006031.png)

粘贴到此2处

![image-20210404230054315](assets/Jenkins webhook自动触发构建/image-20210404230054315.png)

![image-20210404230129836](assets/Jenkins webhook自动触发构建/image-20210404230129836.png)

添加完成之后报错

![image-20210404230156342](assets/Jenkins webhook自动触发构建/image-20210404230156342.png)

这是因为gitlab 10.6 版本以后为了安全，不允许向本地网络发送webhook请求，设置如下：

登录管理员账号

![image-20210404230225325](assets/Jenkins webhook自动触发构建/image-20210404230225325.png)

![image-20210404230246406](assets/Jenkins webhook自动触发构建/image-20210404230246406.png)

![image-20210404230320223](assets/Jenkins webhook自动触发构建/image-20210404230320223.png)

然后需要再次添加webhook，就会成功了。

![image-20210404230409785](assets/Jenkins webhook自动触发构建/image-20210404230409785.png)

成功了，才会显示出来

![image-20210404230418132](assets/Jenkins webhook自动触发构建/image-20210404230418132.png)

**回到jenkins页面**

![image-20210404230448745](assets/Jenkins webhook自动触发构建/image-20210404230448745.png)

##### 开始测试

```shell
在任何一台测试都可以。我这里在gitlab机器上面测试： 

[root@gitlab-server ~]# ssh-keygen #生成秘钥 
[root@gitlab-server ~]# cat .ssh/id_rsa.pub #查看生成的公钥添加到gitlab里面去
```

![image-20210404230641003](assets/Jenkins webhook自动触发构建/image-20210404230641003.png)

```shell
先克隆一下仓库 
[root@gitlab-server ~]# git clone git@192.168.153.157:root/test1.git Cloning into 'test1'... remote: Enumerating objects: 6, done. remote: Counting objects: 100% (6/6), done. remote: Compressing objects: 100% (4/4), done. remote: Total 6 (delta 0), reused 0 (delta 0), pack-reused 0 Receiving objects: 100% (6/6), done. 
[root@gitlab-server ~]# ls anaconda-ks.cfg test1 upload 
[root@gitlab-server ~]# cd test1/ 
[root@gitlab-server test1]# ls README.md 创建文件模拟代码提交到仓库 
[root@gitlab-server test1]# touch a.txt 
[root@gitlab-server test1]# git add . #把文件添加到暂存区 
[root@gitlab-server test1]# git commit -m 'test' [master 2451295] test 1 file changed, 0 insertions(+), 0 deletions(-) create mode 100644 a.txt 

将新代码，推动至Gitlab仓库，立刻去Jenkins查看。是否有新项目构建
[root@gitlab-server test1]# git push origin master #上传到中心仓库master分支 Counting objects: 4, done. Delta compression using up to 2 threads. Compressing objects: 100% (2/2), done. Writing objects: 100% (3/3), 296 bytes | 0 bytes/s, done. Total 3 (delta 0), reused 0 (delta 0) To git@192.168.153.156:root/test1.git f3848d7..2451295 master -> master
```

**返回到jenkins页面查看是否自动发布**

![image-20210404230742616](assets/Jenkins webhook自动触发构建/image-20210404230742616.png)

![image-20210404230807833](assets/Jenkins webhook自动触发构建/image-20210404230807833.png)

![image-20230503155210416](assets/Jenkins webhook自动触发构建/image-20230503155210416.png)