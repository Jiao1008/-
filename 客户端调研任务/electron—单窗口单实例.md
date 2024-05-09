## 单实例

const gotTheLock = app.requestSingleInstanceLock(additionalData)  // 请求锁

返回当前应用程序是否成功取得 锁（是否是当前进程的主要实例）；

如果当前进程是app的主要实例，则gotInstanceLock返回true，同时应用会继续运行；若返回 false，则进程没有取得锁，应该立刻退出，并将参数发送给已经取到锁的进程。

**示例：**

```
if (!gotTheLock) {
  // 如果已经有同样的该程序正在运行，则不启动
  app.quit()
} else {
  // 如果检测到有同样的该程序正在试图启动...
  // Windows 和 Linux 会触发 second-instance 事件
  app.on('second-instance', (event, commandLine, workingDirectory, additionalData) => {
    // 输出从第二个实例中接收到的数据
    console.log(additionalData)
    if (currentWin) {
      const flag =  currentWin.isMinimized() ? currentWin.restore() : currentWin.show()
      flag()
      // 将该程序窗口置为当前聚焦态
      currentWin.focus()
    }
  })
  // MacOS 会触发 open-file 和 open-url 事件
  app.on('open-url', (event, url) => {
    if (currentWin) {
      // 如果该程序窗口处于最小化状态，则恢复窗口
      if (currentWin.isMinimized()) {
        currentWin.restore()
      }
      // 将该程序窗口置为当前聚焦态
      currentWin.focus()
    }
  })
  
  app.whenReady().then(() => {})
}
```