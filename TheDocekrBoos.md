# docker 

## 容器命名
--name 命名容器

    sudo docker run --name demo_container -i -t ubuntu /bin/bash
命名正则[a-zA-Z0-9_.-]

## 重启已经停止的容器

    sudo docker start demo_container(名称和ID都可以)

通过ID启动已经停止的容器

    sudo docker start 8cf696fe974a

### 附着到容器上
docker重启的时候会沿用docker run 命令时指定的参数来运行，因此容器启动之后会运行一个shell 可以使用的docker attach命令重新附着到该容器会话上

附着到正在运行的容器上

    sudo docker attach daemon_demo 

    sudo docker attach 8cf696fe974a

## 创建守护式容器
可以创建长期运行的容器。守护式容器没有交互式会话，非常适合运行应用程序与服务。  
启动守护式容器：

    sudo docker run --name daemon_demo -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"

    因为添加了一个while循环，容器内会一直打印hello world,直到容器或其进程停止运行

### 获取容器日志

    sudo docker logs daemon_demo 

跟踪守护式容器日志

    sudo docker logs -f daemon_demo

获取某个片段的日志  
只需在tail命令后加入-f --tail 标志即可

    sudo docker logs --tail 10 daemon_demo(获取日志最后10行内容)

    sudo docker logs --tail 0 -f daemon_demo(跟踪某个容器的最新日志而不必读取整个日志文件)

    还可以使用-t 标志为每条日志添加时间戳

    sudo docker logs -ft daemon_demo

## docker日志驱动
可以控制docker守护进程和容器所用的日志驱动 通过--log-driver选项来实现 选项有json-file,syslog(该选项禁用docker log并将所有日志都输出都重定向到Syslog或者通过docker run对个别容器即兴日志重定向输出),none(禁用日志)

    sudo docker run --log-driver="syslog" --name daemon_test -d ubuntu /bin/sh -c "while true;do echo hello world;sleep 1; done"

## 查看容器内的进程
docker top

    sudo docker top daemon_demo

## 在容器内运行进程
通过使用docker exec命令在容器内部额外启动新进程(后台任务，交互式任务)

后台任务

    sudo docker exec -d daemon_demo touch /etc/new_config_file
这里-d表明需要运行一个后台进程，-d后指定要在内部执行这个命令的容器名称以及要执行的命令

交互式任务

    sudo docker exec -t -i daemon_demo /bin/bash

## 停止守护式容器
docker stop 

    sudo docker stop daemon_demo

    sudo dkcoer stop 8cf696fe974a

    docker ps -n x (显示最后x个容器，无论是否停止)

## 自动重启容器
--restart标志，让docker 重启该容器。会检查容器退出代码，并据此来决定是否要重启容器

    sudo docker run --restart=always --name daemon_test -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"

配置：  
always: 始终会重启  
on-failure: 只有退出代码为非0的时候才会重启，还可以接收一个重启次数的参数

    --restart=failure:5(最多重启5次)

## 深入容器
docker inspect来获取更多容器信息

    sudo dokcer inspect daemon_demo
可以通过用-f或者--format来选定查看结果

    sudo docker inspect --formate='{{ .State.Running }}' daemon_demo false

    sudo docker inspect --formate '{{ .NetworkSetting.IPAddress }}' daemon_demo

查看多个容器

    sudo docker inspect --formate '{{ .State.Running }}' daemon_demo demo_container

## 删除容器
docker rm

    sudo docker rm 8cf696fe974a

删除全部容器

    sudo docker rm 'sudo docker ps -a -q'
    -q表示只需要返回容器的ID而不会返回容器的其他信息
