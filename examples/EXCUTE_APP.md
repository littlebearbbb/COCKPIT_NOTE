> 本案例主要介绍打开应用的案例，并介绍在该案例中如何用到了 cockpit 的 api

### 案例背景

该案例的实现目标为将所有/usr/share/applications 路径下的.desktop 文件中的 name，exec 提取出来，将 name 作为应用的名称，当用户点应用图标时将执行 exec 命令

### 读取文件

实现该功能的第一步要把/usr/share/applications 所有.desktop 文件列出来，之后再将路径和文件名拼接进行读取，关键代码如下所示

```
const baseUrl = "/usr/share/applications";
// `cd ${baseUrl} && ls | grep .desktop` 这个命令的含义为跳转到/usr/share/applications目录下，并筛选出其中包含.desktop的文件
return new Promise((resolve, reject) => {
  cockpit
    .script(`cd ${baseUrl} && ls | grep .desktop`, {
      superuser: "try",
      err: "message"
    })
    .done(files => {
      // 因为读取出来的文件结尾都包含换行符，所以这里通过换行符将文件拆分为数组
      const fileArr = files.split("\n");
      const promisearrs = [];
      fileArr.forEach(file => {
        if (file) {
          // 将读取文件的promise push到数组中
          promisearrs.push(cockpit.file(`${baseUrl}/${file}`).read());
        }
      });
      // 将所有读取文件的promise执行
      Promise.all(promisearrs).then(contents => {
        // extractContent方法用于抽取文件内容
        resolve(this.packageApp(this.extractContent(contents)));
      });
    })
    .catch(err => {
      console.log(err);
      reject(err);
    });
}

```

接下来我们看一下抽取文件内容的方法

```
extractContent(contents) {
  const newApps = [];
  contents.forEach(content => {
    const app = {};
    // 由于该文件可能有多套入口文件，格式如下所示
    // [ENTRY-NAME1]
    // Name=XXX
    // Exec=XXX
    // [ENTRY-NAME2]
    // Name=XXX
    // Exec=XXX
    // 下面的正则匹配就是匹配到所有入口的内容
    // \w\W 这种写法代表包括\n在内的任意字符，+?这种写法为非贪婪匹配
    const reg = /\[.+\]\n([\w\W]+?)((\n\s*\n)|$)/g;
    const entries = content.match(reg);
    entries.forEach(entry => {
      const entryNameMatch =
        entry.match(/\[(.+)\]\n/) && entry.match(/\[(.+)\]\n/)[1];
      if (entryNameMatch) {
        const type =
          entry.match(/Type=(.*)/) && entry.match(/Type=(.*)/)[1];
        const name =
          entry.match(/Name=(.*)/) && entry.match(/Name=(.*)/)[1];
        const exec =
          entry.match(/Exec=(.*)/) && entry.match(/Exec=(.*)/)[1];
        const icon =
          entry.match(/Icon=(.*)/) && entry.match(/Icon=(.*)/)[1];
        // 将文件内容结构为object
        // 图片这里先用默认的图片
        app[entryNameMatch] = {
          type,
          name,
          exec,
          img: require("@/assets/images/chrome.png"),
          icon,
          content: entry
        };
      }
    });
    newApps.push(app);
  });
  return newApps;
},
```

### 打开应用

打开应用实现方式是当触发应用的点击事件时，执行 exec 命令，关键代码如下所示

```
// 需要注意的是，这里的代码是早期的版本，之后还有跳转，这里主要以讲解为主
excuteApp() {
  if (cockpit) {
    // 如果要想使用命令打开应用，需要设置DISPLAY=:0.0，并要设置权限
    cockpit
      .script(
        `DISPLAY=:0.0; XAUTHORITY=/run/user/1000/gdm/Xauthority; xhost + && ${this.exec}`,
        {
          superuser: "try",
          environ: ["DISPLAY=:0.0"],
          err: "message"
        }
      )
      .catch(err => {
        console.log(err);
      });
  }
},
```
