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
