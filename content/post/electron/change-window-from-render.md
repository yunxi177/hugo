+++
title = "electron 改变窗体大小"
date = 2019-04-13T07:28:08+47:00
tags = ["electron"]
categories = ["electron"]
draft = false
commentId="electron-change-window-from-render"
+++
相关链接:  
[electron-vue 集成 element-ui](http://www.artacode.com/posts/electron/install/)  
在开发 `electron` 的时候遇到了需要在 `render` 中修改 `BrowserWindow` 窗口大小的方式，经过一番尝试，有两种方法实现:  
1. 通过 `ipcRenderer` 与 `ipcMain` 的通讯来实现
2. 通过 `render` 的 `remote` 模块来实现

# ipcRenderer 和 ipcMain 实现
实现原理是 `render` 进程通过 `ipcRenderer`  与 `ipcMain`  进行通讯以通知 `main` 进程操作窗体操作。  

在 `render` 引入 `ipcRenderer`
```
let {ipcRenderer} = require('electron')
```
发送同步消息给 `main` 进程  
```
ipcRenderer.sendSync('synchronous-message','logined')
```

在 `main` 中监听同步消息，并处理 `logined` 消息操作
```
ipcMain.on('synchronous-message', (event, arg) => {
  if (arg === 'logined') {
    mainWindow.resize(1000, 1000)
  }
})
```

# remote 方式是实现
引入 `remote` 模块

```
const { remote } = require('electron')
```
调用 `remote` 方法中的 `getCurrentWindow` 获取当前窗体对象，然后进行修改窗体属性
``` js
remote.getCurrentWindow().setSize(1000, 1000)
```


# 总结
上面实现方式可以看出 `remote` 方式其实是比较简单和方便的，我个人更倾向于用第二种方式实现此功能。其实在 `remote` 模块的底层实现也是通过发布同步消息的方式来实现与 `main` 进程通讯的，本质上和我们实现的方式一是一样的，既然 `eletron` 已经做了一个很好的封装，完全也没有必要舍近求远 直接用 `remote` 方式实现是一个比较优雅的方式。

# 参考链接
[electron remote](https://electronjs.org/docs/api/remote)  
[electron ipcMain](https://electronjs.org/docs/api/ipc-main)  
[electron ipcRenderer](https://electronjs.org/docs/api/ipc-renderer)
