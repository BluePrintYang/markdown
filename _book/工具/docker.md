## win10下安装docker

需要win10专业版，如果是家庭版可以通过更改产品密钥升级到专业版

看[官方文档](https://docs.docker.com/docker-for-windows/install/)安装



```bash
git clone https://github.com/docker/doodle.git
```



```bash
cd doodle\cheers2019 
docker build -t bluepyang/cheers2019 .
```



```bash
docker run -it --rm bluepyang/cheers2019
```



```bash
docker login 
docker push bluepyang/cheers2019
```





一些命令

```bash
docker image ls  # 列出本机的所有 image 文件
docker image rm [imageName] # 删除 image 文件
docker container ls # 列出本机正在运行的容器
docker container ls --all # 列出本机所有容器，包括终止运行的容器
docker image prune #清除none
docker ps  #查看正在运行的容器
```



```bash
docker run --name nginx-test -p 8080:80 -d nginx
```

- **--name nginx-test**：容器名称。
- **-p 8080:80**： 端口进行映射，将本地 8080 端口映射到容器内部的 80 端口。
- **-d nginx**： 设置容器在在后台一直运行。