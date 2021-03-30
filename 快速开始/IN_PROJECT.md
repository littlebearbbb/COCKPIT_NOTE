> 本篇主要阐述如何在 linux 环境下结合 dock-web-native 和 cockpit 进行开发

### 背景介绍

首先要说明的是，目前的开发环境还不完美，没有办法实现了热更新，这里需要注意

### 环境准备

#### 在 linux 环境上安装 node 以及 npm

在 linux 环境上安装 node 以及 npm 的方式有很多,这里只介绍其中一种，具体操作如下所示（如果在某一步的操作出现权限不足的问题，可以在命令前加 sudo 解决）

```
cd /usr/local
mkdir node
cd node
wget https://npm.taobao.org/mirrors/node/v15.8.0/node-v15.8.0-linux-x64.tar.gz
tar -zxvf node-v15.8.0-linux-x64.tar.gz
## 建立软链接
ln -s /usr/local/node/node-v15.8.0-linux-x64/bin/npm /usr/local/bin/npm
ln -s /usr/local/node/node-v15.8.0-linux-x64/bin/node /usr/local/bin/node
```

#### 在 linux 上生成 ssh key

```
ssh-keygen -o
### 生成的sshKey公钥位于~/.ssh目录下
### 打印出公钥中的内容，之后就可以将公钥中的内容复制到gitlab上
cat ~/.ssh/id_rsa.pub
```

#### 将 dock-web-native 项目克隆到本地

```
git clone git@gitlab.xpaas.lenovo.com:tangram/cloudplatform/web/dock-web-native.git
git checkout c-master
```

下载项目中首先要改变 vue.config.js 中的 outputDir

```
## 将username替换为你当前账号的name
const outputDir = '/home/username/.local/share/cockpit/dingde'
```

#### 安装依赖

```
npm install
### 如果在npm install时遇到uds-ui无权限下载的情况，可以将package.json中uds-ui改为
### "uds-ui": "git+ssh://git@gitlab.xpaas.lenovo.com:zhongwl2/uds_ui.git#master"
### 改成ssh下载有效的前提是ssh key公钥已经上传到gitlab了
```

### 开发流程

1.  首先可以在 dock-web-native 项目中增加 cockpit 代码，但是这时还不能看到效果
2.  执行 npm run build，并且复制 manifest.json 到 cockpit 目录下，操作如下所示

```
cp manifest.json ~/.local/share/cockpit/dingde
```

3. 之后在 localhost:9090 的 cockpit web 相关页面中，就可以看到 cockpit 相关代码生效

之后的开发就可以重复上面的流程
