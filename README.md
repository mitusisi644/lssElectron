# 用electron打包VUE前端页面为exe示例
	最近要参个赛，为了牛*一下，要把前端整个桌面应用，看起来高级些，推electron打包，研究了一下，看了好多网上的教程 ，真是入坑分分钟，按着教程走通是没问题，就是坑太多。  安装依赖就说法太多，太跳跃。
	经历了千山万水的终于装好了，发现目录少文件，都没有太详细的说明。 
	等目录也好了，打包命令也能执行了，最终告诉我  Error: Unable to find a valid app   
	什么鬼。  很决然的下班，
	、、、最终还是自己写一个吧，看了其实很简单，就做了三个件事：
		装依赖、拷静态文件改打包配置、开始打包
	

# 项目布局
	建一个空目录,目录结构：

```
.
├── app                                       	// 打包时存放前端静态文件
│   ├── electron.js                    			// 复制 ../main.js 文件直接改名
│   │── package.json                     		// 打包时对包文件的一些名称、版本...配置
│   │── ...                 					// 前端静态文件（html、css、js...）
├── main.js                                     // 项目打包路径

```
	
开始安装 electron 相关依赖
	注：我使用的cnpm，npm在第二步时太考验网络了
	
##	1、  开始拉取 electron 依赖
		cnpm install electron --save-dev 

##	2、  拉取 electron-packager 打包成exe文件的插件（预下载好最后一步打包时用）
		cnpm install electron-packager --save-dev
		
		这一步可能报相关cnpm的版本错误 ，重新再拉取cnpm就可以；
		拉取 cnpm taobao镜像  命令：
		npm install cnpm -g --registry=http://registry.npm.taobao.org

##	3、	把打好包的前端静态项目放到 ./app 下，静态文件是vue里直接用build打包出来的。此时目录为：
```
.
├── app                                       	// 打包时存放前端静态文件
│   ├── electron.js                    			// 复制 ../main.js 文件直接改名
│   │── package.json                     		// 打包时对包文件的一些名称、版本...配置
│   │── static                 					// 前端静态文件（css、js、images...）
│   │   │── ...                     			// ...
│   │── index.html                 				// 前端静态首页
├── node_modules                                // 相关依赖
├── main.js                                     // 项目打包路径
├── package.json
```

##	4、	开始打包
		npm run packageWin

#	结
	按步走下来一般能走通打包是没问题。其中注意前端的静态文件就可以，一般出错是因为静态文件的规范问题导致的electron的警告问题

##	附代码
```
./app/package.json
	{
		"name": "lss",
		"version": "1.0.0",
		"description": "A minimal Electron application",
		"main": "electron.js"
	}
```

```
./package.json
	{
		"scripts": {
		    打包命令====>packageWin": "electron-packager ./app/ --platform=win32 --arch=x64 --overwrite"
		},
		"devDependencies": {
			"electron": "^2.0.2",
			"electron-packager": "^12.1.0"
		}
	}
```

```
./app/electron.json
./main.js

	const electron = require('electron')
	// Module to control application life.
	const app = electron.app
	// Module to create native browser window.
	const BrowserWindow = electron.BrowserWindow

	const path = require('path')
	const url = require('url')

	// Keep a global reference of the window object, if you don't, the window will
	// be closed automatically when the JavaScript object is garbage collected.
	let mainWindow

	function createWindow () {
	  // Create the browser window.
	  mainWindow = new BrowserWindow({width: 800, height: 600})

	  // and load the index.html of the app.
	  mainWindow.loadURL(url.format({
	    pathname: path.join(__dirname, 'index.html'),
	    protocol: 'file:',
	    slashes: true
	  }))

	  // Open the DevTools.
	  // mainWindow.webContents.openDevTools()

	  // Emitted when the window is closed.
	  mainWindow.on('closed', function () {
	    // Dereference the window object, usually you would store windows
	    // in an array if your app supports multi windows, this is the time
	    // when you should delete the corresponding element.
	    mainWindow = null
	  })
	}

	// This method will be called when Electron has finished
	// initialization and is ready to create browser windows.
	// Some APIs can only be used after this event occurs.
	app.on('ready', createWindow)

	// Quit when all windows are closed.
	app.on('window-all-closed', function () {
	  // On OS X it is common for applications and their menu bar
	  // to stay active until the user quits explicitly with Cmd + Q
	  if (process.platform !== 'darwin') {
	    app.quit()
	  }
	})

	app.on('activate', function () {
	  // On OS X it's common to re-create a window in the app when the
	  // dock icon is clicked and there are no other windows open.
	  if (mainWindow === null) {
	    createWindow()
	  }
	})

	// In this file you can include the rest of your app's specific main process
	// code. You can also put them in separate files and require them here.

```

