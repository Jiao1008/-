## 1、多窗口

```
const { BrowserWindow } = require('electron')

// 直接使用new BrowserWindow()新建多个窗口，但不能设置parent和modal属性
const parent = new BrowserWindow()
const child = new BrowserWindow({ parent })
```

## 2、打开新窗口并关闭当前窗口

```
const newWin= new BrowserWindow()
newWin.addListenerEvent("show", () => {
	// 获取所有打开窗口的实例数组
	const winList = BrowserWindow.getAllWindows()
	winList.forEach(v => v.destroy())
})
```

## 3、打开新窗口并置顶（父子窗口）

使用 `parent` 属性创建子窗口：

```
const parent = new BrowserWindow()
const child = new BrowserWindow({ parent })
```

`child` 窗口将总是显示在 `parent` 窗口的顶部

注意：

- macOS 上，子窗口会保持与父窗口的相对位置；Windows 和 Linux 中，当父窗口移动时子窗口不会移动。

## 4、模态窗口（禁用父窗口的子窗口）

创建模态窗口必须设置 `parent` 和 `modal` 属性：

```
const parent = new BrowserWindow()
const child = new BrowserWindow({ parent, modal: true, show: false })
```

注意：

- macOS 上，modal 窗口将显示为附加到父窗口的工作表
- Linux上，许多桌面环境不支持隐藏模态窗口
- Linux上，模态窗口的类型将更改为 `dialog`

## 5、页面可见性

- 在所有平台上, 可见性状态与窗口是否隐藏/最小化相关；
- 在 macOS 上, 可见性状态还会跟随窗口被遮挡状态。 如果窗口A被窗口B完全遮挡了，则A的可见性状态为`hidden`；在其他平台上，可见性状态只有在使用 `win.hide()`使窗口最小化或者隐藏时才为 `hidden`；
- 若创建`BrowserWindow` 时带有 `show: false`参数, 则窗口初始可见性状态将为`visible`， 但窗口实际上是隐藏的；
- 若窗口`backgroundThrottling`被禁用，可见性状态将保持为`visible`， 即使窗口被最小化、遮挡或隐藏。