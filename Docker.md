# Docker

开发 -- 运维（部署上线） 环境不同，麻烦，问题多

开发丢个 jar包给运维，运维配置mysql,redis,jdk之类

现在开发把(jar mysql redis jdk)放入Docker容器，直接丢给运维上线。

之前vm十分笨重几个G起步，而docker把最核心环境变成镜像，十分小巧，几秒就能出

文档地址：https://docs.docker.com/

**镜像（image）：**

镜像就是一个模板，通过模板来创建服务，由镜像new很多对象放到各自容器，项目运行就在容器中

**容器(container):**

利用日期技术可以独立运行或者一个组的应用，通过镜像来创建。

容器可以启动，停止，删除等基本命令

可以把容器理解为一个十分轻量简易的Linux。

**仓库(repository):**

仓库存放镜像，而仓库分为public与private

Docker Hub（服务器在国外），阿里云都有容器服务，类似于Maven（国内最好用阿里的镜像mirror）

安装

```
yum remove docker \

yum install -y yum-utils

yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
  
yum makecache fast

yum install docker-ce docker-ce-cli containerd.io

systemctl start docker

docker version

docker run hello-world


```

卸载docker

```
yum remove docker-ce docker-ce-cli containerd.io

rm -rf /var/lib/docker
```

镜像加速器

```
sudo mkdir -p /etc/docker

sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://owf8ydca.mirror.aliyuncs.com"]
}
EOF

sudo systemctl daemon-reload

sudo systemctl restart docker

```



### SpringBoot微服务打包成Docker镜像

1. 构建
2. 打包
3. 编写dockerfile
4. 构建镜像
5. 发布运行

鸡肋,心急吃不了热豆腐
