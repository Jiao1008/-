### 加载本地`vue-devtools`插件

1、下载vue-devtools

​	1）打开插件下载地址：https://microsoftedge.microsoft.com/addons/Microsoft-Edge-Extensions-Home

​	2）搜索`vue-devtools`并获取

​	3）获取插件ID

![img](https://img-blog.csdnimg.cn/771f66f51b5349c4814424f4c45c90a8.png)

4）打开目录C:\Users\Administrator\AppData\Local\Microsoft\Edge\User Data\Default\Extensions\插件id ，将下面内容拷贝到插件目录。

2、安装本地vue-devtools

```
const { session } = require("electron");
const path = require("path");
session.defaultSession.loadExtension(
        path.resolve(__dirname, "../../_devtools/Vuejs-devtools-6.5.1")
      );
```

