## How to run
本仓库是文章 -https://0x400.com/2020-08-29-dev-getting-started-with-sapui5.html 的代码，如下启动项目
```shell
npm install --global @ui5/cli
ui5 use sapui5@latest
ui5 add sap.ui.core sap.m themelib_sap_belize
npm start
```

## 背景
SAPUI5 官方文档有专门的一节是讲 SAPUI5 的开会环境的 - [Development Environment](https://sapui5.hana.ondemand.com/#/topic/7bb04e05f9484e1b95b38a2e48ecef4f)，按照官方文档介绍，搭建 SAPUI5 的开发环境主要有三种方式：

- SAP Web IDE - Cloud
- ui5 tooling - Local 
- OpenUI5 - Local

为什么还要写这篇文章呢？
<!--more-->
官方文档主要详细介绍了使用 SAP Web IDE 搭建 SAPUI5 的开发环境，但这种方式有诸多不足，如编码效率不高，不方便调试，不能使用 Vim 编码，还需要申请 Cloud 账号等，因此搭建本地开发环境是我学习 SAPUI5 的首选。文档虽然也有简单介绍使用 OpenUI5 和 使用 [ui5](https://sap.github.io/ui5-tooling/) 工具搭建本地开发环境，但通篇晦涩并缺少实践步骤，按照文档搭建环境的过程还可能会遇到一些奇怪的错误。所以本文的出现主要是为了记录我搭建环境中遇到的一些问题，以便后续查阅。

SAPUI5 的核心文件是 *sap-ui-core.js*，理论上只需要引入该文件就可以使用 SAPUI5 了，但开发过程中我们往往更需要的是一个本地的 server，本文会主要讲述使用 ui5 工具启动的 SAPUI5 开发环境。

> 注释： 本文中 ui5 表示的是 ui5 tooling - (https://sap.github.io/ui5-tooling/)，SAPUI5 表示 SAPUI5 框架。

## 使用 ui5 搭建 SAPUI5 开发环境
使用 ui5 搭建 SAPUI5 主要分为如下四个步骤，
1. 创建项目
2. 创建 Hello world 入口文件
3. 使用 ui5 引入 SAPUI5 框架
4. 启动

下文将详述描述着四个步骤。

### 创建项目
本节我们将创建一个基本的 SAPUI5 项目目录。

创建项目 *demo-ui5*，并在 *demo-ui5* 目录下创建子目录 *src/com/0x400/hello*，所有 SAPUI5 相关的代码我们都将放在 *src/com/0x400/hello* 下面，这个目录结构也将会映射成一个命名空间 `com.0x400.hello`

```shell
$ mkdir -p demo-ui5
$ cd demo-ui5
$ yarn init -y
yarn init v1.22.4
warning The yes flag has been set. This will automatically answer yes to all questions, which may have security implications.
success Saved package.json
Done in 0.05s.

$ mkdir -p src/com/0x400/hello
```



### Hello world
本节我们将创建 *index.html* 和 *index.js* 文件，并在 *index.html* 中引入 SAPUI5，并在 SAPUI5 启动时自动加载 *index.js* 文件。


在项目入口路径 *src/com/0x400/hello* 下创建 *index.html* 文件，

*index.html*

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="utf-8">
	<title>Getting started</title>
	<script id="sap-ui-bootstrap"
		src="resources/sap-ui-core.js"
		data-sap-ui-theme="sap_belize"
		data-sap-ui-libs="sap.m"
		data-sap-ui-resourceroots='{"com.0x400.hello": "./"}'
		data-sap-ui-onInit="module:com/0x400/hello/index"
		data-sap-ui-compatVersion="edge"
		data-sap-ui-async="true">
	</script>
</head>
<body class="sapUiBody" id="content"></body>
</html>
```

`src="resources/sap-ui-core.js"` 这里使用相对路径引入了 SAPUI5 框架的核心代码 `sap-ui-core.js` 我们可以使用绝对路径引入 SAPUI5，比如使用官方提供的 CDN https://sapui5.hana.ondemand.com/1.81.1/resources/sap-ui-core.js 即可引入 1.81.1 版本。我们并没有这样做，而是使用相对路径引入 SAPUI5，这就意味着项目启动后我们可以通过相对路径访问到 SAPUI5 的核心代码 - *sap-ui-core.js*。比如项目启动后监听在本机的 8082 端口，那么通过地址 http://localhost:8082/index.html 访问到 *index.html*，通过地址 
http://localhost:8082/resources/sap-ui-core.js 加载 SAPUI5，我们将使用 ui5 工具达到此目的。

`data-sap-ui-resourceroots='{"com.0x400.hello": "./"}'`  是将命名空间 `com.0x400.hello` 和当前路径 *src/com/0x400/hello* 进行了映射，命名空间 `com.0x400.hello` 即等价于 *src/com/0x400/hello* 路径。 

`data-sap-ui-onInit="module:com/0x400/hello/index"` 表明框架启动时会加载当前目录下的 `index.js`。此处 `module:com/0x400/hello` 是命名空间 `com.0x400.hello` 的路径表示。



创建 *index.js* 文件，

*index.js*

```js
sap.ui.define([
    "sap/m/Button",
    "sap/m/MessageToast"
], function(Button, MessageToast) {
    new Button({
        text: "Ready...",
        press: function () {
            MessageToast.show("Hello World");
        }
    }).placeAt("content");
});

```



### 安装 ui5
我们上文所提到，项目启动后将从 http://localhost:8082/resources/sap-ui-core.js 加载 SAPUI5 的核心代码，本节将使用 ui5 工具自动下载我们依赖的 SAPUI5 版本并，并使用 `ui5 serve` 启动项目使得我们能从正确的路径中加载 SAPUI5。

安装 ui5,
```shell
$ npm install --global @ui5/cli
```

创建 *ui5.yaml* 文件用于配置项目启动选项，

```shell
$ ui5 init
```

默认情况下 `ui5 init` 创建的 *ui5.yaml* 文件中 `name` 即为项目目录，项目的类型为 `libary`，我们需要更改为 `application` 类型，并且使用 `resources` 字段下的 `webapp` 配置项目的启动路径，

**标注**： 如果项目类型不正确 `ui5 serve` 可能会报错

*ui5.yaml*

```yaml
specVersion: '2.2'
metadata:
  name: demo-ui5
type: application
resources:
  configuration:
    paths:
      webapp: src/com/0x400/hello
```

使用 `ui5` 加载 SAPUI5 的核心文件和 *index.html* 中用到主题和 libs 

```shell
$ ui5 use sapui5@latest
$ ui5 add sap.ui.core sap.m themelib_sap_belize
```

在官网 https://sapui5.hana.ondemand.com/ 可以查阅 SAPUI5 的版本列表，使用 `ui5 use sapui5@<version>` 安装相应的版本。


因为我们的项目是 Application 类型，所以需要一个 *manifest.json* 文件，所有的项目配置将放在该文件下，使得业务代码和配置分离，在目录 *src/com/0x400/hello* 下创建 *manifest.json* 配置文件，

*manifest.json*

```json
{
  "_version": "1.0.0",
  "sap.app": {
    "id": "com.0x400.hello",
    "type": "application"
  }
}
```
id 可以为项目的根命名空间 `com.0x400.hello`。

**标注**: 对于 Application 类型，*manifest.json* 文件必须的，缺少该文件 `ui5 serve` 时也将会报错，而 ui5 官方文档似乎没有这个提醒。

### 启动

在 `scripts` 中添加启动脚本 `start: ui5 server`。

*package.json*

```json
{
  "name": "demo-ui5",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "scripts": {
    "start": "ui5 serve"
  }
}

```

运行 `npm start` 启动项目，项目启动后监听在 8082 端口上，访问 http://localhost:8082/index.html 点击 Ready... 按钮弹出 Hello World。到此，SAPUI5 的开发环境就部署完成了。
![hello world](images/2020-08/sapui5-hello-world.png)



### Git 提交

```shell
$ git init
```

创建 `.gitignore` 文件并添加如下内容，

*.gitignore*

```shell
# Logs
logs
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Dependency directories
node_modules/

dist/
.idea/

```

创建提交记录：

```shell
$ git add .
$ git commit -m "feat: getting started sapui5"
```
