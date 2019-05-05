# docker 安装centos
https://www.cnblogs.com/yu-hailong/p/7629120.html

1. 下载centos镜像

下载镜像

    [root@docker ~]# docker pull centos

2. 启动容器并进行配置

启动容器

    [root@docker ~]# docker run -it -d --name test-centos1 centos

命令注释:  
-it :　进行交互式操作  
-d :　等同于 -d=true,容器将会在后台运行，不然执行一次命令后，退出后，便是exit状态了。  
--name : 容器启动后的名字，默认不指定，将会随机产生一个名字。或者使用 -name="containers_name"  
centos：使用的镜像名称  

进入容器，安装ssh server,以及配置开机启动

    [root@docker ~]# docker exec -it test-centos1 /bin/bash

注: 命令最后参数 /bin/bash： 指进入容器时执行的命令（command）

安装net-tools，openssh-server

    [root@d72250ecaa5e /]# yum install openssh-server net-tools -y

创建ssh 所需的目录，并在根目录创建sshd 启动脚本

    [root@d72250ecaa5e /]# mkdir -pv /var/run/sshd
    mkdir: created directory '/var/run/sshd'

    [root@d72250ecaa5e /]# cat /auto_sshd.sh 
    #!/bin/bash
    /usr/sbin/sshd -D
    [root@d72250ecaa5e /]# chmod +x /auto_sshd.sh 

## docker容器安装nvm
进入docker 执行

    curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.29.0/install.sh | bash

检查nvm 可安装的node版本　
    
    nvm ls-remote

安装指定版本的nodejs

    nvm install v10.15.1

## docker 安装Nginx

1. 添加nginx的源  
执行：

        rpm -ivh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm


2. 执行安装

        yum install -y nginx

3. 查看nginx默认路径

        whereis nginx

以下是Nginx的默认路径：

(1) Nginx配置路径：/etc/nginx/

(2) PID目录：/var/run/nginx.pid

(3) 错误日志：/var/log/nginx/error.log

(4) 访问日志：/var/log/nginx/access.log

(5) 默认站点目录：/usr/share/nginx/html

事实上，只需知道Nginx配置路径，其他路径均可在/etc/nginx/nginx.conf 以及/etc/nginx/conf.d/default.conf 中查询到


4. 常用执行命令

nginx   启动

nginx -t  测试命令

nginx -s relaod 修改nginx.conf之后，可以重载


## docker 打包镜像 上传dockerhub
执行：

    docker commit yujuenianbei-centos yujuenianbeicentos:0.1

yujuenianbei-centos:容器名称  
yujuenianbeicentos:生成后的镜像名称
此写法上传不上去

    docker commmit　CONTAINER ID username/Repositories

登录dockerhub

    docker login

push镜像

     docker push username/Repositories:latest 

修改tag

    docker tag 3bd2787b9fa3 a1030907690/ubuntu:latest





## docker挂载本地文件

　　Docker容器启动的时候，如果要挂载宿主机的一个目录，可以用-v参数指定。 
　　比如启动一个centos容器，宿主机的/test目录挂载到容器的/soft目录，可通过以下方式指定：

    docker run -it -v /test:/soft centos /bin/bash 

　　这样在容器启动后，容器内会自动创建/soft的目录。

注意： 
　　容器目录不可以为相对路径，必须以下斜线“/”开头。宿主机的目录最好也是绝对路径。 
　　挂载宿主机已存在目录后，在容器内对其进行操作，报“Permission denied”。可通过指定–privileged参数来解决：

    docker run -it --privileged=true -v /test:/soft centos /bin/bash

    docker run -it -v /home/ubuntu/demo/nodeServer:/home/node centos /bin/bash

容器地址：/home/node  
本机地址：/home/ubuntu/demo/nodeServer

    
    docker run -it -p20180:80   -p20181:8080  -p20182:8976 --name containerName image:tag
    docker run -it -p 127.0.0.1:3001:3000 -v /home/ubuntu/demo/nodeServer:/home/node yujuenianbeicentos:0.1 /bin/bash
    
    docker run -it -p3000:3000 -p3001:3001 --link mymysql:db -v /home/ubuntu/demo/ManageFIleGuide:/home/node --name mycentos yujuenianbei/yujuenianbeicentos /bin/bash
    
    docker run -it -p3000:3000 -p3001:3001 -p3002:3002 -p3003:3003 -p3004:3004 -p3005:3005 --link mymysql:db -v /home/ubuntu/demo/ManageFIleGuide:/home/node --name mycentos yujuenianbei/yujuenianbeicentos /bin/bash
    
    docker run -d --name mymysql --rm -p 3400:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
    
    https://www.jianshu.com/p/f72154e54321
    
    https://www.decathloncustom.cn/en_us/?___from_store=zh_cn
