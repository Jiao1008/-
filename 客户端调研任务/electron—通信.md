## 1、 主进程—>渲染进程

1）使用**webContents**模块（单向）

```plaintext
// main.js
const mainWindow = new BrowserWindow({
    webPreferences: {
      preload: path.join(__dirname, 'preload.js')
    }
})
mainWindow.webContents.send('update', 1),
// renderer.js
ipcRenderer.on('update', (_event, value) => {})
```

2）双向可借鉴单向流程

## 2、 渲染进程—>主进程

前言：官方不建议频繁暴露API给渲染进程使用\
1）单向

```plaintext
/****基础用法****/
// render.js
ipcRenderer.send('message', data)
// main.js
ipcMain.on('message', (event, data) => {})

/****利用预加载脚本用法****/
// preload.js
const { contextBridge, ipcRenderer } = require('electron')
// 暴露electronAPI给渲染进程
contextBridge.exposeInMainWorld('electronAPI', {
  setTitle: (title) => ipcRenderer.send('set-title', title)
})
// renderer.js
window.electronAPI.setTitle(title)
```

2）双向

1. **ipcRender.invoke** 在electron7以上版本使用，有可替代方案

```plaintext
// preload.js
const { contextBridge, ipcRenderer } = require('electron')
contextBridge.exposeInMainWorld('electronAPI', {
  openFile: () => ipcRenderer.invoke('dialog:openFile')
})
// main.js
ipcMain.handle('openFile', (event, data) => {})
// renderer.js
const filePath = await window.electronAPI.openFile()
filePathElement.innerText = filePath
```

2. **ipcRenderer.sendSync** 向主进程发送消息，并 _同步_ 等待响应。 官方不建议使用，可能会阻塞渲染进程

## 3、主进程<=>渲染进程

见上文

## 4、渲染进程—>渲染进程

1）没有直接API

2）将主进程作为渲染器之间的消息代理（转发）

![image-20240206175205782](https://github.com/Jiao1008/Learning-Notes/blob/master/git-images/1.png%20%5B%E5%8E%9F%E5%A7%8B%E5%A4%A7%E5%B0%8F%5D.jpg)

3）从主进程将一个 MessagePort 传递到两个渲染器

```plaintext
// main.js
import { MessageChannelMain } from 'electron';
const messageChannel = new MessageChannelMain();
console.log(messageChannel.port1, messageChannel.port2);

mainWin.webContents.postMessage('port', null, [port1])方法发送给BrowserWindow

在BrowserWindow窗口侧需要监听同名事件
ipcRenderer.on('port', e => {
	拿到e.ports[0]对象并保存下来。
})

主进程只需要把port分发给A和B窗口，两个窗口各自持有port1和port2之后，就可以通过他们进行通信了。
```

详见：[实例使用](https://www.electronjs.org/zh/docs/latest/tutorial/message-ports#%E5%AE%9E%E4%BE%8B%E4%BD%BF%E7%94%A8)

## 5、内嵌DOM标签

1）和宿主页面通信（postMessage方式）

2）和主进程通信

通过messageChannel的特性，可以把宿主页面作为“中介”，只进行一次端口交换，后续让主进程和iframe直接经由端口（port1、port2）来通信。

![image-20240207091406096](https://github.com/Jiao1008/Learning-Notes/blob/master/git-images/2.png%20%5B%E5%8E%9F%E5%A7%8B%E5%A4%A7%E5%B0%8F%5D.jpg)

## 6、内嵌视图容器

Electron里的tag是基于chrome app的标准开发的，由于后者已经被Chrome抛弃，所以无法保证后续版本的可用性。

![image-20240207093055754](uploads/b7878939ba6ea1c5fc7b879da360c81a/image-20240207093055754.png)
