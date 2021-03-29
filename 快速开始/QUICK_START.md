## 快速开始

> 本篇主要介绍如何搭建 cockpit 环境，并使用 cockpit 进行开发

### 参考文档

cockpit 官方文档如下所示，[传送门](https://cockpit-project.org/)

### cockpit 介绍

cockpit 是一款 liunx 系统监控 web 平台，其内部通过 cockpit.js 实现了
许多与 linux 底层交互的方法，不过 cockpit.js 只能在 cockpit web 环境中使用，所以我们的第一步是要安装 cockpit

### cockpit 安装

在 Ubuntu 18.04 上安装 Cockpit，首先需要执行如下两个命令

```
sudo apt update
sudo apt -y install cockpit
```

安装后，使用以下命令确认服务是否在运行

```
sudo systemctl status cockpit
```

查看后如果 Active 状态为 inactive，请使用以下命令启动它：

```
sudo systemctl start cockpit
```

之后通过 localhost:9090 就可以访问 cockpit web 页面，其中用户名和密码为当前账号的用户和密码。

### cockpit 源码

以下为 cockpit 源码的 github 下载地址

```
https://github.com/cockpit-project/cockpit.git
```

### cockpit 开发

在 cockpit 的基础上也可以增加自己的页面，首先我们没必要自己从头开始开发一个 cockpit 的页面，在 cockpit 的源码中已经给我们准备了例子，我们可以直接下载源码，例子目录为：cockpit/examples/，这里我们以 cockpit/examples/docker-info 为例，按照如下命令可以增加页面

```
git clone https://github.com/cockpit-project/cockpit.git
cd ./cockpit/examples/
mkdir -p ~/.local/share/cockpit/docker-info
cp -r ./docker-info/* ~/.local/share/cockpit/docker-info
```

之后再打开 localhost:9090，就可以看到侧边栏上多出了 Docker Info 的栏目，之后就可以在~/.local/share/cockpit/docker-info/plugin.html 中引入新的 js 文件测试 cockpit 的各种 api
