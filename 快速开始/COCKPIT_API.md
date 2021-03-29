> 本篇主要以更容易理解的方式介绍 cockpit 中使用到的方法

### 参考文档

本篇文档主要参考自 cockpit 官方文档，关于 cockpit 的功能大家以官方文档为主，这里主要是作为一个记录，[文档传送门](https://cockpit-project.org/guide/latest/api-base1.html)

### 文件相关

- 读取文件

```
  // path: 文件所在路径，content: 读取文件内容
  cockpit
  .file(path)
  .read()
  .then((content) => { ... })
  .catch(error => { ... })
```

- 替换文件内容

```
// path: 文件所在路径，content: 文件要替换的内容，如果为null，将删除这个文件
cockpit
.file(path)
.replace(content)
```

### 脚本相关

- 执行 linux 命令

```
// shell: 执行的linux命令，options: 可选参数，其中常用选项如下所示
{
    superuser: 是否以超级管理员身份运行该命令，可选值有require:需要用超级管理员身份运行，try: 尝试用超级管理员身份运行，如果失败，则以普通用户运行,
    environ: 如果linux命令需要设置环境变量，可以使用该参数，参数形式为"NAME=VALUE",
    err: 控制错误的输出形式，常用的可选参数为message: 输出错误信息，ignore: 忽略错误信息
}
cockpit
.script(shell, [options])
.catch(err => { ... })
```

### DBUS 相关

- bus 建立链接

```
//name为dbus的链接名，client为链接后用于操作dbus的对象
client = cockpit.dbus(name)
```

- bus 关闭链接

```
// 关闭bus链接
client.close()
```

- bus 调用方法

```
// bus调用总线方法，path为object path，interface为接口名称，method为方法名，args为方法的参数
client.call(path, interface, method, args)
```

- bus 设置代理

```
// bus设置代理，interface为接口名称，path为object path
proxy = client.proxy([interface, path], [options])
```

这里再解释一下，设置代理的目的是当某个接口的 single 发生变化时可以监听到数据的传送

- proxy 监听信号

```
// proxy监听信号的方法，name为信号的名称，args为信号触发的参数
proxy.addEventListener("signal", (event, name, args) => { ... })
```
