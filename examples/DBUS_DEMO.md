> 本案例介绍如何构建一个 DBUS 服务端，并使用 cockpit 发起调用以及监听信号

### 参考文档

本案例主要参考网上的一篇文章，也有对于 DBUS 的介绍，[文档传送门](https://blog.csdn.net/weixin_39759247/article/details/107055188)

### DBUS 服务端构建

#### 运行环境准备

部署服务之前需要先安装一些包，安装内容如下所示

```
sudo apt install dbus
sudo apt install d-feet
sudo apt install libgtk2.0-dev
sudo apt install libdbus-glib-1-dev
sudo apt install automake
```

需要注意的是，d-feet 是一个很好用的 dbus 可视化工具，可以查看 session，system 的 dbus，也可以执行其中的方法，启动命令如下所示

```
d-feet
```

之后可以下载文章尾部的 example-signal-emitter 和 example-signal-recipient 演示信号发射文件，解包到工程目录,并执行下面的命令

```
./autogen.sh
./configure
make
```

需要注意的是，如果在上述命令的发生错误，可以查看发生错误的代码，将其注释掉就好

接下来要起两个终端，分别执行下面的命令

```
cd hello-dbus-0.1/src
./example-signal-emitter
```

```
cd hello-dbus-0.1/src
./example-signal-recipient
```

cockpit 中的测试代码如下所示

```
// 通过dbus名称和dbus服务进行链接，dbus名可以在d-feet中查看
const client = cockpit.dbus("org.designfu.TestService", {
    bus: 'session',
});
// 传入interface和object path，这些也可以在d-feet中查看
const proxy = client.proxy("org.designfu.TestService", "/org/designfu/TestService/object");
// 监听接口上的信号，并接收信号发送的数据
proxy.addEventListener("signal", (event, name, args) => {
    console.log("-----signal----");
    console.log('--------event-------');
    console.log(event);
    console.log('-------name------');
    console.log(name);
    console.log('-------args-------');
    console.log(args);
});

// 通过client.call也可以触发信号的变化，被监听。另外也可以在d-feet中手动触发方法，改变信号变化
client
  .call("/org/designfu/TestService/object", "org.designfu.TestService", "emitHelloSignal")
  .then(
    (data) => {
        console.log('----data-----');
        console.log(data);
    })
  .catch((err) => {
      console.log('-----err----');
      console.log(err);
  });
```
