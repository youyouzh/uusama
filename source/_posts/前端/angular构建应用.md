---
title: angular构建应用
tags:
  - angular
url: 847.html
id: 847
categories:
  - 前端
  - angular
date: 2018-07-06 16:56:01
---

## 设置开发环境
首先需要`node`和`npm`来管理angular，你需要安装`nodejs`，`npm`是node的包管理工具，一般安装node是自动安装。

为了能够使用angular，需要node 8.x 和 npm 5.x 以上的版本。更老的版本可能会出现错误，更新的版本则没问题。

全局安装angular，命令行运行下面命令安装angular
```bash
# -g表示全局安装angular-cli
npm install -g @angular/cli
```

## 新建angular项目
安装完angular-cli以后，可以使用angular终端命令穿件一个angular应用：
```bash
# 使用下面的命令创建一个angular应用，应用名称为uusama
ng new uusama
```

## 启动开发服务器
创建完应用以后，可以使用下面的命令启动服务器，挂载我们的应用：
```bash
# 首先去到应用根目录，打开开发服务器
cd uusama
ng serve --open
```

`ng serve`命令会启动开发服务器，监听文件变化，并在修改这些文件时重新构建此应用。

使用`--open`（或`-o`）参数可以自动打开浏览器并访问 http://localhost:4200/。

## 项目文件结构
下面列出根目录下的每一个文件含义：

|文件|用途|
|:---|:---|
|e2e/ |在 e2e/ 下是端到端（end-to-end）测试 |
|node_modules/ |Node.js 创建了这个文件夹，并且把 package.json 中列举的所有第三方模块都放在其中 |
|.editorconfig |基本的编辑器配置 |
|.gitignore |Git 的忽略配置文件 |
|angular.json |Angular CLI 的配置文件 |
|package.json |npm 配置文件，其中列出了项目使用到的第三方依赖包，你还可以在这里添加自己的自定义脚本 |
|protractor.conf.js |给Protractor使用的端到端测试配置文件，当运行 ng e2e 的时候会用到它 |
|README.md |关于该项目的基础文档 |
|tsconfig.json |TypeScript 编译器的配置 |
|tslint.json |给TSLint和Codelyzer用的配置信息，当运行 ng lint 时会用到 |

下面列出src文件夹下项目代码文件的作用：

|文件|用途|
|:---|:---|
|app/app.component.{ts,html,css,spec.ts} |使用 HTML 模板、CSS 样式和单元测试定义 AppComponent 根组件。  |
|app/app.module.ts |定义 AppModule，根模块为 Angular 描述如何组装应用 |
|assets/* |图片等资源文件放在这儿 |
|environments/* |各个目标环境文件 |
|browserslist |共享浏览器配置文件 |
|favicon.ico |书签栏图标 |
|index.html |主页面的 HTML 文件，angular自动管理 |
|karma.conf.js |给Karma的单元测试配置，当运行 ng test 时会用到它 |
|main.ts |应用的主要入口点 |
|polyfills.ts |浏览器 Web 标准 |
|styles.css |全局样式 |
|test.ts |单元测试的主要入口点 |
|tsconfig.{app|spec}.json |TypeScript 编译器的配置文件 |
|tslint.json |额外的 Linting 配置。当运行 ng lint 时，它会供带有 Codelyzer 的 TSLint 使用 |

更加详细的介绍可以参考官方文档：<https://angular.cn/guide/quickstart>