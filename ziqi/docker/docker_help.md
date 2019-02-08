#                       Ubuntu16.04中使用docker
# 安装
记得在安装完docker后

```
sudo usermod -aG docker isaac
sudo service docker start
```
# 本地镜像
文件都存储在/var/lib/docker下面的文件夹中
查看镜像：
```
docker images
docker image ls []
```
# 本地容器
查看运行中的容器：
```
docker ps
```
查看终止的容器：
```
docker container ls -a 
```
如果要删除镜像，必须删除对应的终止的容器：
```
docker container rm $containerid
docker image rm $imageid
```

重新运行终止的容器：
```
docker restart &containerid
或者
docker container start $ containerid
```
运行并以root进入到容器中配置：
```
nvidia-docker run -itd -v ~/FCIS-babycam:/data --restart=always --link nvidia/cuda:latest python2.7.14

docker exec -ti python3 /bin/bash
或者
sudo docker attach $container
```
提交容器到本地作为镜像
```
sudo docker commit $container $image_name
```
# 定制镜像
一个简易方式：

使用file文件构建镜像，命名为python：numpy。如有同名，原来的image的tag会变成<none\>：
```
cat file | docker build -t python:numpy  -
```
注意run中的命令通过&&连接，这样只会建立一层镜像。但是管道不能够让服务器读取上下文。
***
因此，使用文件名为Dockerfile的文件，通过docker build来使用上下文——context。

[如何使用Dockerfile](https://www.jianshu.com/p/93a678d1bde6)

# Volume
## 使用数据卷对共享代码，隔离数据与容器很重要。

“代码不放入镜像，使用volume挂载放入容器。这样我的镜像只需要维护程序运行的环境，不同的项目运行不同的容器挂载不同的代码。我认为这种粒度比较合适。”

“由于开发环境代码一直在变动，而且多人通过git协作，于是代码都是放在外面，构建一个运行环境的image，然后代码部分用volume映射进去，方便随时调整。

我们的生产环境比较独特，一般都是给客户安装成品而不是自己的服务器，一方面为了保护代码，另一方面是减少不必要的麻烦，于是就把代码都构建在image里面，这样直接在客户那边把镜像运行起来就行，只是个下载的时间，也不需要其他操作。”

“If you have some persistent data that you want to share between containers, or want to use from non-persistent containers, it’s best to create a named Data Volume Container, and then to mount the data from it.”

***
可以```docker run -v```在运行容器时动态挂载数据卷

```docker run -v local/path:container/path```:在容器上挂载本地非默认目录

也可以在```dockerfile```中用volumes参数指定。不过dockerfile用来定制镜像的。

[详见此贴](http://dockone.io/article/128)

# Docker Compose
Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration. 
用来定义和运行多个容器应用的工具。用.yml配置。


# 配置私有仓库
[详见此贴](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-private-docker-registry-on-ubuntu-14-04)

## repository和registry的不同
Repository：本身是一个仓库，这个仓库里面可以放具体的镜像，是指具体的某个镜像的仓库，比如Tomcat下面有很多个版本的镜像，它们共同组成了Tomcat的Repository。

Registry：镜像的仓库，比如官方的是Docker Hub，它是开源的，也可以自己部署一个，Registry上有很多的Repository，Redis、Tomcat、MySQL等等Repository组成了Registry。

按照Docker的logo来看，Repository是集装箱，Registry是鲸鱼。

# harbor：企业级的docker仓库管理工具

## 需要

    docker-compose 1.8.0以上
    用wget下载harbor

进入harbor文件夹内，修改harbor.conf配置 最后```sudo ./install.sh```完成配置

harbor有必选参数和可选参数，必选参数比如http是不可修改的，要修改的话得备份重装。必选和可选：[参考](https://github.com/vmware/harbor/blob/master/docs/installation_guide.md)

## 安装后
### 服务器端
拥有管理员的账号：admin
密码：88888888

### 客户端
由于没有使用https传输，所以ubuntu用户在/etc/docker/daemon.json中加入：

```
{
  "insecure-registries" : ["$服务器ip地址"]
}
```
再 ```sudo service docker restart```

mac用户在软件的```preference->daemon->advanced```中添加如上语句。再点击restart即可。

## 权限
项目管理员（可读写，改人员），开发者（读写），访客（读）
公有项目不登录也可下载

## 上传和下载
在读写镜像前修改名称：

```
docker tag $原名称（加tag） $host_ip/$project_name/$source_name(比如：python) : $tag
```
镜像名称是有要求的，如果project_name在harbor中找不到，是无法上传的
上传（下载）文件：

```
docker push(pull) $image
```



# 容器迁移

export和save的区别：
export对容器操作，save对镜像操作
save会保存历史操作，可以回滚到以前的层。在run的时候可以不加/bin/bash

```
sudo docker export $container_name > $compress_file

scp给其他hosts

cat $compress_file | sudo docker import - $image_name(rep:tag)

docker run ... /bin/bash
必须要加/bin/bash 否则docker: Error response from daemon: No command specified.

```
# 容器连接
通过端口公开连接 --expose $port

将宿主机端口绑定bind至容器端口

通过链接link选项去连接两个容器，限于单宿主机
# 配置docker子网ip
## create一个子网
```
 docker network create -o "com.docker.network.bridge.name"="docker1" --subnet 172.20.0.0/16 docker1
    创建名为docker1的bridge
    --subnet 设置子网段
    -o "com.docker.network.bridge.name"="docker1" 给这个bridge起个名字，否则宿主机中看到的网桥名是一坨乱码。
```

## 查看子网信息
```
docker network inspect $docker_bridge_name
```

## 连接容器到子网
```
docker network connect [OPTIONS] NETWORK CONTAINER
    --alias		Add network-scoped alias for the container
    --ip		IPv4 address (e.g., 172.30.100.104)
    --ip6		IPv6 address (e.g., 2001:db8::33)
    --link		Add link to another container
    --link-local-ip     Add a link-local address for the container
```

## 取消连接
```
docker network disconnect [OPTIONS] NETWORK CONTAINER
```

## 修改默认网关
sudo service docker stop
在/etc/docker/daemon.json 中添加"bip" : "192....."
再 sudo service docker restart

这样会改变默认的网桥docker0的地址

# pycharm访问remote docker
在server端修改/etc/default/docker,添加：```DOCKER_OPTS="-H unix:///var/run/docker.sock -H 0.0.0.0:2375"```
但这个操作不安全。

在pycharm中的edit configurations里，添加docker。这实际上相当于配置一个```docker run```有关的命令。

在这里我发现一个问题，就是挂载数据时，host的路径指的是remote docker daemon所在的主机的路径，而不是客户端主机所在的路径。所以你需要把文件传到server上再挂载到容器里运行才可以（这也太麻烦了吧！！）。

这个host不是指你自己的主机而是docker daemon它自己的宿主机！！！。。。

```pycharm里面连接容器有两种方法，一个是添加Docker，一个是通过Deployment的ssh。后者通过路径映射，可以将local volume内的数据传到容器内，因为ssh时，我们连接的是容器的ip。但是对与前者，添加Docker一直是一个静态的过程。添加后我们在使用容器前，必须先用静态文件比如镜像。我们只能通过docker的宿主机ip:port（见docker端口映射）访问到docker里的内容，容器的ip是不知的。```
***
后来我使用tcp连接了一下远程docker，确实不能够挂载local volume。那么怎么跨主机共享volume呢？

# 跨主机共享数据卷
1. [btsync](https://www.centos.bz/2016/11/distributed-docker-volumes-with-bittorrent-sync/)
以在公网上为例，会有一个tracker server。客户端获得包含tracker server的ip地址和端口，以找到其他peer。

    服务器端会开启一个btsync镜像，暂且将这个容器命名为btsync，获得秘钥。
    
    不同的客户端获得秘钥，并在运行自己的容器时，挂载btsync内的数据卷。这样就能实现共享。

2. [NFS server](https://jaxenter.com/how-to-share-docker-volumes-across-hosts-119602.html)
    
    适合企业大型项目。在这个过程中用到了[RPC](https://zh.wikipedia.org/wiki/%E9%81%A0%E7%A8%8B%E9%81%8E%E7%A8%8B%E8%AA%BF%E7%94%A8)

    引入概念[infrastructure containers](https://docs.joyent.com/public-cloud/instances/infrastructure)

# docker集群



## （why？）下载代理服务：
```
docker pull docker.io/shipyard/docker-proxy

docker run -ti -d -p 2375:2375 \
 --restart=always \
  --hostname=$HOSTNAME \
   --name shipyard-proxy  \
   -v /var/run/docker.sock:/var/run/docker.sock  -e PORT=2375  docker.io/shipyard/docker-proxy:latest
```
 
## 下载swarm
```
docker pull docker.io/swarm
```

## 生成manager
```
   docker swarm init --advertise-addr 192.168.6.151
```
   
### 得到一个
```
token:SWMTKN-1-5lm8fwpabncsu7vbzs9r0r3blsrzzbuqadpe799n75e8ql3pod-4vvmkf28toxy07ef7gh0h8cjs
```
### 在manager端：
```
docker run -ti -d -p 2376:2375  \
 --name pswarmanager docker.io/swarm:latest manage \
  --host tcp://192.168.6.151:2375 \ token:SWMTKN-1-0vwbgqjb59yitnex3ipnuum9wng0nkvx2oeq4v0vagahgiozxu-2elz17mtj7qudhhykzb92kdl7
```
### 在另外的客户端即工作节点：
```
docker swarm join \
    --token SWMTKN-1-0vwbgqjb59yitnex3ipnuum9wng0nkvx2oeq4v0vagahgiozxu-2elz17mtj7qudhhykzb92kdl7 \
    192.168.6.151:2377
```

## 集群运行：
```
docker pull docker.io/portainer/portainer
docker run -d -p 9001:9000 --restart=always --name prtainer-test docker.io/portainer/portainer
```

## 打开portainer
即192.168.6.151:/9001
在endpoint URL中输入192.168.6.151:2375



# 坑

## net/http: TLS handshake timeout.

```不要```按照百度上的去换国内源：
```
echo "DOCKER_OPTS=\"\$DOCKER_OPTS --registry-mirror=http://f2d6cb40.m.daocloud.io\"" | sudo tee -a /etc/default/docker
```
否则没有办法启动docker了。如果换了的话，不要按照网上说的那些随便删除文件，会崩的。通过命令可以看出来在/etc/default/docker追加了"DOCKER_OPTS...".把这个文件移出这个文件夹试试看能否重新```service docker start```

那这个timeout的解决方式，我是通过```docker logout```，再```docker login```成功的。注意login时不要输入用户名，一直按回车直到```Error: Non-null Username Required```。

## 默认网关
在docker里，不同子网的ip网段不要相同。我之前配了一个docker1，是192.168.64.1/24.后来又在/etc/docker/daemon.json里面加"bip" : "192.168.64.1/24"来更改默认网关（docker0）的IP网段，这样会冲突没有办法启动docker
