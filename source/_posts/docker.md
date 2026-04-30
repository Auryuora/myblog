---
title: docker
date: 2026-04-08 21:07:50
tags:
---

## 在Linux上安装Docker

### 更新apt源
打开终端，输入
需要在前面加上sudo，否则可能因没有权限无法保存退出
若没加sudo，可以先按Esc，输入“:q!”,从而不保存强制退出

``` bash
$ sudo vim /etc/apt/sources.list
```

按a键进入可写模式，将下面源粘贴进去

``` bash
#中科大
deb http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib
deb-src http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib

#阿里云
deb http://mirrors.aliyun.com/kali kali-rolling main non-free contrib
deb-src http://mirrors.aliyun.com/kali kali-rolling main non-free contrib
```

按Esc，输入“:wq”退出
查看是否成功保存

``` bash
$ cat /etc/apt/sources.list
$ apt update                  #对源进行更新
```

### 用apt安装Docker
``` bash
$ sudo apt-get install docker.io
```
安装后查询一下版本，验证是否安装成功

``` bash
$ #启动docker服务
$ service docker start
$
$ #列出docker现有镜像
$ docker images
```
若要开机自启动，则输入systemctl enable docker, 然后运行显示的代码

检查服务运行状态
``` bash
$ systemctl status docker.service
```

## windows上安装docker

首先在windows功能里勾选**适用于linux的windows子系统**和**虚拟机平台**

然后已管理员身份打开命令提示符
**安装wsl**
``` bash
wsl --install
sudo apt update && sudo apt upgrade  #更新和升级
```
详细教程：https://learn.microsoft.com/zh-cn/windows/wsl/setup/environment 

**下载docker安装包**
下载安装包，按照安装说明进行

若安装时遇到如图权限错误，则
{% asset_img install.png %}
1.在 Windows 搜索框中输入 PowerShell。
2.右键点击“Windows PowerShell”，选择 “以管理员身份运行”。
3.在弹出的命令行窗口中输入以下命令，然后按回车执行：
``` PowerShell
Remove-Item -Path "C:\ProgramData\DockerDesktop" -Force -Recurse
```
4.命令执行成功后，关闭 PowerShell 窗口，然后右键单击安装程序，选择“以管理员身份运行” 即可。
找到docker的设置，点进General
{% asset_img install2.png %}
再点进Resources->WSL集成，启动
{% asset_img install3.png %}

**确认安装**
``` bash
docker --version
docker run hello-word
```
官方详细说明：https://learn.microsoft.com/zh-cn/windows/wsl/tutorials/wsl-containers


## 下载镜像

常用命令

docker pull 下载镜像

``` bash
docker pull docker.io/library/nginx:latest
#docker.io官方仓库地址（可省略）
#library官方名（可省略）
#latest版本号（可省略）
```

docker images 下载过的镜像

docker rmi nginx 删除nginx镜像

docker pull --platform=xxxxx nginx 选择CPU架构(如linux/amd64)

### 运行容器

常用命令

``` bash
sudo docker run nginx  #使用镜像创建一个容器，如果不存在该镜像，会自动拉取
sudo docker run -d nginx  #在后台创建，不用新开窗口

docker run -p 80:80 nginx   #端口映射，宿主机端口：容器端口
docker run -v /website/html:/usr/share/nginx/html nginx  #宿主机目录：容器内目录，数据持久化保存(绑定挂载)
sudo docker rm -f id   #(-f强制)删除容器
```
sudo docker ps 查看正在运行的容器process status
内容：第一列是容器的id，最后一列是容器的名字

### 挂载卷

常用命令
``` bash
sudo docker volume creat nginx_html  #创建一个挂载卷
docker run -v nginx_html:/usr/share/nginx/html nginx  #连接目录

sudo docker volume inspect nginx_html  #查看目录 
sudo -i  #切换root目录
cd /.../nginx_html/_data  #进入目录
/.../nginx_html/_data# vi index.html  #查看index文件

sudo docker volume list  #查看卷
sudo docker volume rm  #删除卷
sudo docker volume prune -a  #删除所有没有任何容器在使用的卷
```

### 其他常用命令

``` bash
-e   #往容器中传递环境变量，环境变量可在dockerhub中寻找
sudo docker run -d --name my_nginx nginx   #给容器自定义名字
sudo docker run -it --rm alpine   #-it让控制台进入容器，--rm停止时删除容器
sudo docker run -d --restart always nginx  #容器停止就重启
sudo docker run -d --restart unless-stopped nginx   #手动停止时不重启
```  

## 调试容器

停止容器
sudo docker stop 容器id/名字

启动容器
sudo docker start 容器id/名字

查看日志
sudo docker logs -f 容器id/名字

在容器内部执行linux命令
docker exec 容器id linux命令

进入正在运行的容器内部
docker exec -it 容器id /bin/sh

docker为了压缩空间，很多工具都需要下载。首先要查一下容器内的Linux是什么版的
cat /etc/os-release

若是Debian的，其安装软件的命令就是apt，先更新apt索引。
apt update
apt install vim

## Dockerfile
用dockerfile制作一个镜像，并推送到dockerhub上

### main文件

写一个python文件使用fast API创建一个get接口，运行在8000端口上
``` python
from fastapi import FastAPI
import uvicorn
app = FastAPI()

@app.get("/")
def read_root():
    return{"Hello":"World"}

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0",port=8000)
```

再写一个**requirements**文件，列出程序的python依赖
``` txt
fastapi
uvicorn
```

接着安装依赖,
``` TERMINAL
pip install -r .\requirements.txt
python .\main.py
```
之后在浏览器访问localhost:8000,看是否运行成功

### 创建Dockerfile文件

``` Dockerfile
FROM python:3.13-slim   #选择一个基础镜像
WORKDIR /app   #切换到工作目录
COPY . .  #把代码文件拷贝到工作目录，第一个点表示我当前工作目录，第二个点表示镜像内的工作目录
RUN pip install -r .\requirements.txt   #在镜像内执行,在linux里去掉.\
EXPOSE 8000    #声明端口
CMD ["python3","main.py"]   #容器启动时的默认命令
```

### 构建镜像
``` TERMINAL
docker build -t docker_test .   #点表示在当前文件夹构建
docker run -d -p 8000:8000 docker_test     #将容器端口映射到宿主机上
```

在dockerhub官网注册，然后在终端登录docker
``` TERMINAL
docker login
```

登录成功后创建镜像，需要带上名字
``` TERMINAL
docker build -t auroryu/docker_test .
```

最后将镜像推送到DockerHub
``` TERMINAL
docker push auroryu/docker_test
```

若推送过程遇见该问题，则原因是代理配置冲突
{% asset_img push.png %}
我使用的代理是v2rayN,界面底部状态栏显示 HTTP 代理端口为10808
于是打开 Docker Desktop，进入 设置 (Settings) → Docker Engine 选项卡。
{% asset_img push2.png %}
将添加proxies部分，变成
``` json
{
  "builder": {
    "gc": {
      "defaultKeepStorage": "20GB",
      "enabled": true
    }
  },
  "experimental": false,
  "proxies": {
    "default": {
      "httpProxy": "http://host.docker.internal:10808",
      "httpsProxy": "http://host.docker.internal:10808",
      "noProxy": "localhost,127.0.0.1,.local,docker.io,registry-1.docker.io"
    }
  }
}
```
点击生效后再尝试docker push命令

## 将Docker推送到GitHub上

给现有镜像**添加一个新标签**
``` TERMINAL
docker tag docker_test auroryu/docker_test:v1.0.0
```

**登录GitHub**

在 PowerShell 窗口中运行以下命令
``` PowerShell
winget install --id GitHub.cli
```
安装好后在新窗口检验是否安装成功
``` PowerShell
gh --version
```

使用 GitHub CLI 进行身份验证
``` TERMINAL
gh auth login
```
