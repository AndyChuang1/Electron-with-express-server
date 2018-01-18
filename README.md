# Electron-with-express-server
Packing express server to exe  using electron
Electron procedure of packing with express server
	本文件介紹 Electron 如何包進express server輸出成exe。詳細流程詳看基本操作與介紹，步驟雷同之地方將會跳過，此文件主要提醒如果express server包進Electron需要注意的地方。
1.	首先安裝套件：
專案裡打開cmd

安裝electron:
npm install electron --save-dev

安裝electron-packager:
npm install electron-packager --save-dev
npm install electron-packager -g
2.	準備好程式與配置檔
•	我們範例以簡單的express專案做展示，server listen localhost:443， 讀取index.html 
 

 
•	main.js是最重要的檔案，這裡win.loadURL 就直接載入我們server所在位置。而架起express server的程式碼就寫在 紅色註解處。
main.js Docs
const { app, BrowserWindow } = require('electron')
const path = require('path')
const url = require('url')

// Keep a global reference of the window object, if you don't, the window will
// be closed automatically when the JavaScript object is garbage collected.
let win

function createWindow() {
    // Create the browser window.
    win = new BrowserWindow({ width: 800, height: 600 })
    win.webContents.openDevTools()
    // and load the index.html of the app.
    win.loadURL('http://localhost:' + 443);
    // Open the DevTools.
    if (process.env.NODE_ENV == 'development') {
        win.webContents.openDevTools();
    }

    // Emitted when the window is closed.
    win.on('closed', () => {
        // Dereference the window object, usually you would store windows
        // in an array if your app supports multi windows, this is the time
        // when you should delete the corresponding element.
        win = null
    })
}

// This method will be called when Electron has finished
// initialization and is ready to create browser windows.
// Some APIs can only be used after this event occurs.
app.on('ready', createWindow)

// Quit when all windows are closed.
app.on('window-all-closed', () => {
    // On macOS it is common for applications and their menu bar
    // to stay active until the user quits explicitly with Cmd + Q
    if (process.platform !== 'darwin') {
        app.quit()
    }
})

app.on('activate', () => {
    // On macOS it's common to re-create a window in the app when the
    // dock icon is clicked and there are no other windows open.
    if (win === null) {
        createWindow()
    }
})

// In this file you can include the rest of your app's specific main process// code. You can also put them in separate files and require them here.
var express = require('express');
var http = require('http');

var app2 = express();


var index = { index: 'index.html' }


app2.use('/', express.static(__dirname+ '/public', index));



app2.listen(443);

module.exports = app2;

紅框處，express.static 一定要給__dirname不然Electron 在輸出成exe時會找不到路徑。
•	網頁主程式(index.html)   
<script>if (typeof module === 'object') { window.module = module; module =  undefined; }</script>
<script src="javascripts/jquery-3.2.1.min.js"type='text/javascript'></script>
<script>if (window.module) module = window.module;</script>

我們要把需要的css,js 都加載近來，不一樣的是，這裡jQuery 要載入時必須加入紅框處，不然輸出exe檔，直接load server 內容一樣會無法使用jQuery
•	配置文件檔 (package.json)
{
  "name": "electronsimple",
  "version": "0.0.0",
  "description": "electron",
  "main": "main.js",
  "scripts": {
    "start": "electron main",
    "pack": "electron-packager . MyApp --platform=win32 --arch=x64 --version=1.7.10 --icon=myapp.ico --asar=true --ignore=node_modules/electron-* "
  },

3.	編譯成執行
執行『cmd』打開命令提示字元，進入專案錄，執行下面語法：
npm run start
npm run pack
	成功輸出成exe檔
 
 

