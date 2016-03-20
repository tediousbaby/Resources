### 淘宝NPM镜像使用及搭建私有的NPM镜像

[http://npm.taobao.org/]()  
[https://github.com/cnpm/cnpmjs.org]()

这是一个完整 npmjs.org 镜像，你可以用此代替官方版本(只读)，同步频率目前为 10分钟 一次以保证尽量与官方服务同步。

- 当前 registry.npm.taobao.org 是从 r.cnpmjs.org 进行全量同步的.
- 当前 npm.taobao.org 运行版本是: cnpmjs.org@2.8.1
- 本系统运行在 Node.js@v4.2.6 上.

#### 用法

单独安装：
`$ npm install -g cnpm --registry=https://registry.npm.taobao.org`

或者你直接通过添加npm参数alias一个新命令:

```
alias cnpm="npm --registry=https://registry.npm.taobao.org \  
--cache=$HOME/.npm/.cache/cnpm \  
--disturl=https://npm.taobao.org/dist \  
--userconfig=$HOME/.cnpmrc"
```
或者alias it in .bashrc or .zshrc

```
$ echo '\n#alias for cnpm\nalias cnpm="npm --registry=https://registry.npm.taobao.org \
  --cache=$HOME/.npm/.cache/cnpm \
  --disturl=https://npm.taobao.org/dist \
  --userconfig=$HOME/.cnpmrc"' >> ~/.zshrc && source ~/.zshrc
```

#### 安装

从registry.npm.taobao.org安装所有模块. 当安装的时候发现安装的模块还没有同步过来, 淘宝NPM会自动在后台进行同步, 并且会让你从官方NPM registry.npmjs.org 进行安装. 下次你再安装这个模块的时候, 就会直接从淘宝NPM安装了.

`$ cnpm install [name]`

#### 同步

直接通过 sync 命令马上同步一个模块  
`$ cnpm sync connect`


### cnpm私有仓库

![架构图](https://raw.githubusercontent.com/tediousbaby/pictures/master/node.js/cnpm-npm-relation.png)
>从CNPM的架构图中，我们可以看出CNPM是对NPM做的镜像服务，CNPM会定期同步NPM的资源库，同时CNPM支持发布私有的库，这样就非常方便地集成了公有库和私有库，对于公司内部的开发者来说，基本感觉不到两种库的区别。另外，我们使用NPM下载依赖包时，经常性地会遇到一些包下载失败的情况，主要原因了NPM的注册服务器在国外，国内的网络环境访问国外的IP并不是太好。所以，直接配置到国内的NPM镜像，可以减少NPM下载出错机会。  

##### 是什么？
  
> Private npm registry and web for Enterprise, base on koa, MySQL and Simple Store Service.
Our goal is to provide a low cost maintenance and easy to use solution for private npm.

##### 可以做什么？

- Build a private npm for your own enterprise. (alibaba is using cnpmjs.org now)
- Build a mirror NPM. (we use it to build a mirror in China: cnpmjs.org)
- Build a completely independent NPM registry to store whatever you like.

##### 如何部署

```
~ git clone https://github.com/cnpm/cnpmjs.org.git
~ cd cnpmjs.org
~ sudo npm install 
~ mysql -uroot -e 'CREATE DATABASE cnpmjs;' &&\
~ mysql -uroot 'cnpmjs' < node_modules/cnpmjs.org/docs/db.sql &&\
~ mysql -uroot 'cnpmjs' -e 'show tables;' #创建数据库
~ vi ./config/index.js  #修改配置文件
~ npm start #启动服务
```

##### 如何使用私有仓库

- 下载指定私有库 `npm install connect --registry=http://192.168.1.20:7001`
- 项目设置私有库 `npm config set registry http://192.168.1.20:7001`
- 用户设置私有库 ` vi ~/.npmrc  写入 registry=http://192.168.1.20:7001`

