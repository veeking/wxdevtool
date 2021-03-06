# wxdevtool

微信开发者工具Docker版
- 支持cli命令行和http服务调用

## 本地编译运行:
1. 编译容器
```
进入docker项目源码目录
docker build -t 账户名/容器名 .
```
2. 运行镜像并启动容器
- -d 表示后台运行容器
- -v 将本地主机(宿主机)目录挂载(映射到)到docker容器对应目录里
- -p 8080:80  将本地的 8080 端口映射到容器的 80 端口 （映射的只是临时 随时可以取消）
```
docker run -d --name 容器名 -p 本地端口(8080):容器端口(80) -v 本地项目目录:容器项目目录 账户名/容器名:tag标签名(默认latest)
```

3. 使用容器
```
- docker exec -t 容器名 wxdevtool脚本 脚本方法 微信cli命令行
(wxdevtool脚本 脚本方法) = cli = 包装了一层
```

## 自动化HTTP调用使用步骤
1. 运行并启动镜像

```bash
docker run -d --name wxdevtool -p 8083:9000 -p 8080:80 -v 本地项目地址:容器项目地址 veekingsen/wxdevtool:latest
```

2. 并在VNC中开启开发者工具的服务端口，并启动开发者工具
打开浏览器，输入http://localhost:8080地址
在自动打开的开发者工具中手动开启 工具的服务端口
```
设置 > 安全 > 开启服务端口  
```
[点击查看参考文档](https://developers.weixin.qq.com/miniprogram/dev/devtools/cli.html)


3. 初始化wine（如果想使用开发者GUI功能的话需要初始化）(可选)
```
// 初始化wine  并生成了~/.wine32/目录及其一系列文件
wine win
```
4. 重启容器

```bash
docker restart wxdevtool
```

5. 浏览器里输入```http://localhost:8083/login```检查是否正常显示调用


## docker hub运行:

1. 从docker hub中pull最新镜像

```bash
docker pull veekingsen/wxdevtool:latest
```

2. 运行并启动镜像

```bash
docker run -d --name wxdevtool -p 8080:80 -v 本地项目地址:容器项目地址 veekingsen/wxdevtool:latest
```

3. 并在VNC中开启开发者工具的服务端口，并启动开发者工具

打开浏览器，输入http://localhost:8080地址
启动开发者工具
```bash
docker exec -t wxdevtool wxdevtool start
```
在设置 > 安全 > 开启服务端口  
[参考文档](https://developers.weixin.qq.com/miniprogram/dev/devtools/cli.html)


4. 重启容器

```bash
docker restart wxdevtool
```

## 命令行调用

```bash
docker exec -t wxdevtool wxdevtool help

# 运行开发者工具命令
- docker exec -t 容器名 wxdevtool脚本 脚本方法 微信命令行
(wxdevtool脚本 脚本方法) = cli = 包装了一层
# 例子1:
docker exec -t wxdevtool wxdevtool exec [..args]
# 例子2：
docker exec -t wxdevtool wxdevtool exec --login
```

[参考文档](https://developers.weixin.qq.com/miniprogram/dev/devtools/cli.html)

## HTPP服务调用

开发者工具IDE Http服务开放在9000端口 

```bash
# 映射本地端口8083到容器端口9000
docker run -d --name wxdevtool -p 8083:9000 -p 8080:80 -v /path/to/projects:/projects veekingsen/wxdevtool:latest

# 打开项目
curl localhost:8083/open?projectpath=path_to_project

# 预览项目
curl http://localhost:8083/preview?projectpath=/root/WeChatProjects/miniprogram-1
```

[参考文档](https://developers.weixin.qq.com/miniprogram/dev/devtools/http.html)

## 标签

|  Tag   |   Version    |
| :----: | :----------: |
| latest |    1.02.2004022    |
|  1.02-2004022  | 1.02.2004022 |


## 环境变量

**WXURL**

https://servicewechat.com/wxa-dev-logic/download_redirect?type=x64&from=mpwiki

> Always fetch the latest

**NODE_URL**

https://nodejs.org/dist/v${version}/node-v${version}-linux-x64.tar.gz

Mirror:

https://npm.taobao.org/mirrors/node/v${version}/node-v${version}-linux-x64.tar.gz

**NWJS_URL**

https://dl.nwjs.io/v${version}/nwjs-sdk-v${version}-linux-x64.tar.gz

Mirror:

https://npm.taobao.org/mirrors/nwjs/v${version}/nwjs-sdk-v${version}-linux-x64.tar.gz

## 相关参考
[linux版微信开发者工具](https://github.com/dragonation/wechat-devtools)

[Linux微信web开发者工具docker版本2](https://github.com/cytle/wechat_web_devtools)


## 其它
2020-05-14: docker运行后开发者工具没有自动启动
- 原因：
 - 1、$WINEPREFIX/user.reg默认不存在 导致无法启动开发者工具 (user.reg不存在的原因是新版wine(>=5.0)没能正确安装,而且需要手动初始化，才会在~/.wine32生成user.reg等文件)
 - 2、$IDE_PATH端口文件默认不存在，导致也无法启动nginx (手动开启端口后，重启docker正常恢复)