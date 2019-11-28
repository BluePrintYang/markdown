# 初识vue

### 安装vue

#### 采用npm方法安装

- 先要安装node.js环境（npm包管理器）
- vue-cli脚手架构建工具
- cnpm npm淘宝镜像

我之前已经安装好了npm与cnpm淘宝镜像

下面直接安装vue-cli脚手架

命令行执行

```bash
npm install -g vue-cli
```

创建一个vue文件夹

cd进入此文件夹

```bash
vue init webpack firstApp
```

解释一下这个命令，这个命令的意思是初始化一个项目，其中webpack是构建工具，也就是整个项目是基于webpack的。其中firstApp是整个项目文件夹的名称，这个文件夹会自动生成在你指定的目录中

```bash
cd firstApp
cnpm install
```

运行初始化命令的时候会让用户输入几个基本的配置选项，如项目名称、项目描述、作者信息，对于有些不明白或者不想填的信息可以一直按回车去填写就好了，等待一会，就会显示创建项目创建成功

如图

![1569769640712](..\img\1569769640712.png)

```bash
cd firstApp
npm run dev
```

运行项目



