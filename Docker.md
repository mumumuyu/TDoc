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

鸡肋,心急吃不了热豆腐 (hhh，跑起来咯，CI/CD需要一定的配置，个人成本有限，将就docker咯)

😅，本来想跳过Docker直接CI/CD的，没想到jenkins直接要三个节点，一个服务器16G内存(GitLab直接6G)其，另一个也不小，寄，继续看Docker了

```sh
docker run -d --hostname my-rabbit --name myrabbit -e RABBITMQ_DEFAULT_USER=root -e RABBITMQ_DEFAULT_PASS=admin -p 15672:15672 -p 5672:5672 rabbitmq:management
```

- Dockerfile

```bash
FROM java:8
EXPOSE 8081

VOLUME /home/docker

ENV TZ=Asia/Shanghai
RUN ln -sf /usr/share/zoneinfo/{TZ} /etc/localtime && echo "{TZ}" > /etc/timezone

ADD Myboke.jar  /app.jar
RUN bash -c 'touch /app.jar'
ENTRYPOINT ["java","-jar","/app.jar"]
```

- FROM java:8 表示基于jdk8环境
- EXPOSE 8080  表示对外暴露的端口是8081
- - VOLUME /tmp 表示挂载到/tmp目录
- ADD eblog-0.0.1-SNAPSHOT.jar /app.jar 表示把jar包复制到镜像服务里面的根目录，并改名称app.jar
- RUN bash -c 'touch /app.jar' 表示执行创建app.jar
- ENTRYPOINT ["java","-jar","/app.jar"] 表示执行启动命令java -jar

```bash
docker build -t mybokev1.0 .
docker run -p 8081:8081 --name mybokev1.0 -d mybokev1.0
```

成功~，mysql redis也可以用镜像，要用的话application.yml配置里记得把所有软件ip更改为镜像内网ip，经济实力雄厚的可以用云服务器👍云mysql 30/年

含mysql,redis,es等等中间件的springboot一键部署https://juejin.cn/post/6844904142620622862

```bash
docker images 
docker rmi xxxid
```

