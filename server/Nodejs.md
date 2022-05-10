# Nodejs

![Nodejs](img/nodejs.png)

Node.js 是运行在服务端的 JavaScript, 是基于 Chrome JavaScript V8 引擎建立的平台。

## 使用官方编译过的二进制数据包的方式安装

下载 `nodejs 16.15.0` 到 `/data` 目录:

```shell
cd /data

wget https://nodejs.org/dist/v16.15.0/node-v16.15.0-linux-x64.tar.xz
```

下载完成后将其解压：

```shell
tar xvJf node-v16.15.0-linux-x64.tar.xz
```

将解压的 Node.js 移动到 `/usr/local` 目录下：

```shell
mv node-v16.15.0-linux-x64 /usr/local/node-v16
```

配置 `node` 软链接到 `/bin` 目录：

```shell
ln -s /usr/local/node-v16/bin/node /bin/node
```

## 配置环境变量

```shell
echo 'export PATH=/usr/local/node-v16/bin:$PATH' >> /etc/profile
source /etc/profile
```

## 使用 npm

通过 `npm` 配置淘宝源：

```shell
npm config set registry https://registry.npm.taobao.org

npm get registry
```

全局安装 `yarn` 并配置淘宝源：

```shell
npm install yarn -g

yarn config set registry http://registry.npm.taobao.org/

yarn config get registry

ln -s /usr/local/node-v16/lib/node_modules/yarn/bin/yarn /bin/yarn
```
