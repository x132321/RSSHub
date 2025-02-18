---
sidebar: auto
---

# 部署

部署 RSSHub 需要基本的计算机编程常识，如果您在部署过程中遇到无法解决的问题请到 [issues](https://github.com/DIYgod/RSSHub/issues) 寻找类似的问题或 [向我们提问](https://github.com/DIYgod/RSSHub/issues/new/choose)，我们会尽快给您答复。

部署涉及到以下基本编程常识：

1.  命令行操作
2.  [Git](https://git-scm.com/)
3.  [Node.js](https://nodejs.org/)
4.  [npm](https://www.npmjs.com/get-npm) 或 [yarn](https://yarnpkg.com/zh-Hans/docs/install)

部署到可外网访问则可能涉及到：

1.  [Nginx](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/)
2.  [Docker](https://www.docker.com/get-started) 或 [docker-compose](https://docs.docker.com/compose/install/)
3.  [Redis](https://redis.io/download)
4.  [Heroku](https://devcenter.heroku.com/articles/getting-started-with-nodejs)
5.  [Google App Engine](https://cloud.google.com/appengine/)
6.  [Fly.io](https://fly.io/)
7.  [Zeabur](https://zeabur.com)

## Docker 镜像

默认推荐使用 `diygod/rsshub` 即 `diygod/rsshub:latest` 最新版镜像以获取最新路由。

当 `diygod/rsshub:latest` 存在问题时，可以使用以日期为标签的近期镜像临时使用，例如:

```bash
$ docker pull diygod/rsshub:2021-06-18
```

待最新镜像更新后再切换回 `diygod/rsshub:latest` 最新版镜像。

如需启用 puppeteer，可使用 `diygod/rsshub:chromium-bundled`；若指定日期则为 `diygod/rsshub:chromium-bundled-2021-06-18`。

亦可使用 Docker Compose 部署以启用 puppeteer，但更消耗磁盘空间和内存。通过修改 `docker-compose.yml`，也可以使用 `diygod/rsshub:chromium-bundled`，这样就没有更消耗资源的问题了。

## Docker Compose 部署

### 安装

下载 [docker-compose.yml](https://github.com/DIYgod/RSSHub/blob/master/docker-compose.yml)

```bash
$ wget https://raw.githubusercontent.com/DIYgod/RSSHub/master/docker-compose.yml
```

检查有无需要修改的配置

```bash
$ vi docker-compose.yml  # 也可以是你喜欢的编辑器
```

创建 volume 持久化 Redis 缓存

```bash
$ docker volume create redis-data
```

启动

```bash
$ docker-compose up -d
```

### 更新

删除旧容器

```bash
$ docker-compose down
```

如果之前已经下载 / 使用过镜像，下方命令可以帮助你获取最新版本：这可能可以解决一些问题。

```bash
$ docker pull diygod/rsshub
```

然后重复安装步骤

### 添加配置

修改 [docker-compose.yml](https://github.com/DIYgod/RSSHub/blob/master/docker-compose.yml) 中的 `environment` 进行配置

## Docker 部署

:::tip 提示

如需启用 puppeteer，请在**每条**命令中均将 `diygod/rsshub` 替换为 `diygod/rsshub:chromium-bundled`。

:::

### 安装

运行下面的命令下载 RSSHub 镜像

```bash
$ docker pull diygod/rsshub
```

然后运行 RSSHub 即可

```bash
$ docker run -d --name rsshub -p 1200:1200 diygod/rsshub
```

在浏览器中打开 <http://127.0.0.1:1200/>，enjoy it! ✅

您可以使用下面的命令来关闭 RSSHub

```bash
$ docker stop rsshub
```

### 更新

删除旧容器

```bash
$ docker stop rsshub
$ docker rm rsshub
```

然后重复安装步骤

### 添加配置

配置运行在 docker 中的 RSSHub，最便利的方法是使用 docker 环境变量

以设置缓存时间为 1 小时举例，只需要在运行时增加参数：`-e CACHE_EXPIRE=3600`

```bash
$ docker run -d --name rsshub -p 1200:1200 -e CACHE_EXPIRE=3600 -e GITHUB_ACCESS_TOKEN=example diygod/rsshub
```

该部署方式不包括 puppeteer（除非改用 `diygod/rsshub:chromium-bundled`）和 redis 依赖，如有需要请改用 Docker Compose 部署方式或自行部署外部依赖

更多配置项请看 [#配置](#pei-zhi)

## Ansible 部署

这个 Ansible playbook 包括了 RSSHub, Redis, browserless (依赖 Docker) 以及 Caddy 2

目前只支持 Ubuntu 20.04

需要 sudo 权限和虚拟化能力（Docker 将会被自动安装）

### 安装

```bash
sudo apt update
sudo apt install ansible
git clone https://github.com/DIYgod/RSSHub.git ~/RSSHub
cd ~/RSSHub/scripts/ansible
sudo ansible-playbook rsshub.yaml
# 当提示输入 domain name 的时候，输入该主机所使用的域名
# 举例：如果您的 RSSHub 用户使用 https://rsshub.example.com 访问您的 RSSHub 实例，输入 rsshub.example.com（去掉 https://）
```

### 更新

```bash
cd ~/RSSHub/scripts/ansible
sudo ansible-playbook rsshub.yaml
# 当提示输入 domain name 的时候，输入该主机所使用的域名
# 举例：如果您的 RSSHub 用户使用 https://rsshub.example.com 访问您的 RSSHub 实例，输入 rsshub.example.com（去掉 https://）
```

## 手动部署

部署 `RSSHub` 最直接的方式，您可以按照以下步骤将 `RSSHub` 部署在您的电脑、服务器或者其他任何地方

### 安装

首先是下载 `RSSHub` 的源码

```bash
$ git clone https://github.com/DIYgod/RSSHub.git
$ cd RSSHub
```

下载完成后，需要安装依赖（开发不要加 `--production` 参数）

<code-group>
<code-block title="pnpm" active>

```bash
pnpm install --prod
```

</code-block>
<code-block title="yarnv1">

```bash
yarn --production
```

</code-block>
<code-block title="npm">

```bash
npm install --omit=dev
```

</code-block>
</code-group>

由于众所周知的原因，在中国使用 `npm` 下载依赖十分缓慢，建议挂一个代理或者考虑使用 [NPM 镜像](https://npm.taobao.org/)

### 启动

然后在 `RSSHub` 文件夹中运行下面的命令就可以启动

```bash
$ yarn start
```

或

```bash
$ npm start
```

或使用 [PM2](https://pm2.keymetrics.io/docs/usage/quick-start/)

```bash
$ pm2 start lib/index.js --name rsshub
```

在浏览器中打开 <http://127.0.0.1:1200/>，enjoy it! ✅

详细使用说明参照 [指南](/zh/)，替换所有路由例子中的 `https://rsshub.app/` 为 `http://localhost:1200` 即可正常使用

### 添加配置

:::tip 提示

在 arm/arm64 上，此部署方式不包含 puppeteer 依赖。要启用 puppeteer，你需要先从发行版安装 Chromium，然后设置 `CHROMIUM_EXECUTABLE_PATH` 为其可执行路径。

Debian:

```bash
$ apt install chromium
$ echo >> .env
$ echo 'CHROMIUM_EXECUTABLE_PATH=chromium' >> .env
```

Ubuntu/Raspbian:

```bash
$ apt install chromium-browser
$ echo >> .env
$ echo 'CHROMIUM_EXECUTABLE_PATH=chromium-browser' >> .env
```

:::

可以通过设置环境变量来配置 RSSHub

在项目根目录新建一个 `.env` 文件，每行以 `NAME=VALUE` 格式添加环境变量，例如

```env
CACHE_TYPE=redis
CACHE_EXPIRE=600
```

注意它不会覆盖已有的环境变量，更多规则请参考 [dotenv](https://github.com/motdotla/dotenv)

该部署方式不包括 redis 依赖，如有需要请改用 Docker Compose 部署方式或自行部署外部依赖

更多配置项请看 [#配置](#pei-zhi)

### 更新

在 `RSSHub` 文件夹中运行下面的命令就从 github 仓库拉取最新版本

```bash
$ git pull
```

然后重复安装步骤。

### Nix 用户提示

通过 `nix-shell` 配置简化安装 nodejs, yarn 和 jieba：

```nix
let
    pkgs = import <nixpkgs> {};
    node = pkgs.nodejs-12_x;
in pkgs.stdenv.mkDerivation {
    name = "nodejs-yarn-jieba";
    buildInputs = [node pkgs.yarn pkgs.pythonPackages.jieba];
}
```

## 部署到 Railway

包含自动更新。

[![Deploy on Railway](https://railway.app/button.svg)](https://railway.app/template/QxW\_\_f?referralCode=9wT3hc)

## 部署到 Heroku

### 注意

:::caution 更新

Heroku [不再](https://blog.heroku.com/next-chapter) 提供免费服务。

:::

~~未验证支付方式的 heroku 账户每月仅有 550 小时额度（约 23 天），验证支付方式后可达每月 1000 小时。~~

### 一键部署（无自动更新）

[![Deploy to Heroku](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https%3A%2F%2Fgithub.com%2FDIYgod%2FRSSHub)

### 自动更新部署

1.  将 RSSHub [分叉（fork）](https://github.com/DIYgod/RSSHub/fork) 到自己的账户下。
2.  把自己的分叉部署到 Heroku：`https://heroku.com/deploy?template=URL`，其中 `URL` 改为分叉地址 （例如 `https://github.com/USERNAME/RSSHub`)。
3.  检查 Heroku 设置，随代码库更新自动部署。
4.  安装 [Pull](https://github.com/apps/pull) 应用，定期将 RSSHub 改动自动同步至你的分叉。

## 部署到 Vercel (ZEIT Now)

### 一键部署（无自动更新）

[![Deploy to Vercel](https://vercel.com/button)](https://vercel.com/import/project?template=https://github.com/DIYgod/RSSHub)

### 自动更新部署

1.  将 RSSHub [分叉（fork）](https://github.com/DIYgod/RSSHub/fork) 到自己的账户下
2.  去 Vercel 部署一个新项目：使用 GitHub 账户登录 Vercel，进入[项目创建页面](https://vercel.com/new/) 选择导入 RSSHub 仓库进行部署
3.  安装 [Pull](https://github.com/apps/pull) 应用，定期将 RSSHub 改动自动同步至你的仓库

## 部署到 Fly.io

### 方案一：Fork

1.  将 RSSHub [Fork](https://github.com/DIYgod/RSSHub/fork) 到自己的账户下；

2.  下载分叉的源码

    ```bash
    $ git clone https://github.com/<your username>/RSSHub.git
    $ cd RSSHub
    ```

3.  前往 [Fly.io 完成注册](https://fly.io/app/sign-up)，并安装 [flyctl CLI](https://fly.io/docs/hands-on/install-flyctl/)；

4.  运行 `fly launch`, 并选择一个唯一的名称和实例地区；

5.  使用 `fly secrets set KEY=VALUE` [对部分模块进行配置](#pei-zhi-bu-fen-rss-mo-kuai-pei-zhi)；

6.  [配置通过 GitHub Actions 自动部署](https://fly.io/docs/app-guides/continuous-deployment-with-github-actions/)；

7.  （可选）利用 `fly certs add 你的域名` 来配置自定义域名，并根据指引在你的 DNS 服务商配置相关域名解析（你可在 Dashboard Certificate 页面查看域名的配置状态）。

更新：在你 Fork 出来的仓库首页点击「Sync fork - Update Branch」来手动更新至官方最新的 master 分支，或安装 [Pull](https://github.com/apps/pull) 应用来定期自动同步。

### 方案二：自行维护 fly.toml

1.  前往 [Fly.io 完成注册](https://fly.io/app/sign-up)，并安装 [flyctl CLI](https://fly.io/docs/hands-on/install-flyctl/)；
2.  自行在本地新建一个空目录，在其中运行 `fly launch`, 并选择一个唯一的名称和实例地区；
3.  编辑生成的 fly.toml 文件，新增

    ```toml
    [build]
    image = "diygod/rsshub:latest"
    ```

    根据实际情况，你可能希望使用其他镜像标签，请阅读 [Docker 镜像](#docker-jing-xiang) 的有关内容；
4.  修改 fly.toml 中的 `[env]` 段或使用`fly secrets set KEY=VALUE` [对部分模块进行配置](#pei-zhi-bu-fen-rss-mo-kuai-pei-zhi)；
5.  执行 `fly deploy` 启动应用；
6.  （可选）利用 `fly certs add 你的域名` 来配置自定义域名，并根据指引在你的 DNS 服务商配置相关域名解析（你可在 Dashboard Certificate 页面查看域名的配置状态）。

更新：进入你存储了 `fly.toml` 文件的目录，执行 `fly deploy` 即可触发拉取最新镜像、启动应用的步骤。

### 配置内置的 Upstash Redis 缓存

在 `RSSHub` 文件夹下运行

```bash
$ fly redis create
```

来创建一个新的 Redis 数据库，地域选择与你上面创建 RSSHub app 时相同的地域，建议选择开启 [eviction](https://redis.io/docs/reference/eviction/)。创建完成后会输出类似于 `redis://default:<password>@<domain>.upstash.io` 的字符串。

因目前[上游依赖的一个 bug](https://github.com/luin/ioredis/issues/1576)，你暂时需要在 Fly.io 给你的连接 URL 后追加 `family=6` 的参数，即使用 `redis://default:<password>@<domain>.upstash.io/?family=6` 作为连接 URL。

再配置 fly.toml 中的 `[env]` 段或运行

```bash
$ fly secrets set CACHE_TYPE=redis REDIS_URL='<刚才的连接 URL>'
```

并执行 `fly deploy` 触发重新部署来完成配置。

## 部署到 PikaPods

每月只需 1 美元即可运行 RSSHub。包括自动更新和 5 美元的免费起始额度。

[![Run on PikaPods](https://www.pikapods.com/static/run-button.svg)](https://www.pikapods.com/pods?run=rsshub)

## 部署到 Zeabur

1.  前往 [Zeabur 完成注册](https://dash.zeabur.com)
2.  创建一个新项目
3.  在项目中选择创建新服务，选择从**服务市场**部署。
4.  添加域名，若使用自定义域名，可参见 [Zeabur 的域名绑定文档](https://docs.zeabur.com/zh-CN/deploy/domain-binding)。

[![Deploy on Zeabur](https://zeabur.com/button.svg)](https://dash.zeabur.com/templates/X46PTP)

## 部署到 Google App Engine

### 准备

[Before you begin](https://cloud.google.com/appengine/docs/flexible/nodejs/quickstart)

按照这里的引导完成 GCP 账号设置，创建 GCP 项目，创建 App Engine 项目，开通付费功能（必须），安装 git 与 gcloud 工具。并完成 gcloud 工具的初始化，初始化具体方式 [请查看这个链接](https://cloud.google.com/sdk/gcloud/?hl=zh-CN)。如果你不打算在本地调试本项目，可以不安装 Node.js 环境。

请注意，GAE 免费用量不支持 Flexible Environment，部署到 Flexible Environment 前请确认收费标准。

Node.JS 的 standard environment 仍在测试中，您可能会在部署或使用中遇到某些不可预期的问题。

运行 `git clone https://github.com/DIYgod/RSSHub.git` 拉取本项目的最新版本。

### app.yaml 配置

#### 部署到 Flexible Environment

在 RSSHub 项目根目录下建立一个 app.yaml 文件，内容示例如下：

```yaml
# [START app_yaml]
runtime: custom
env: flex

# This sample incurs costs to run on the App Engine flexible environment.
# The settings below are to reduce costs during testing and are not appropriate
# for production use. For more information, see:
# https://cloud.google.com/appengine/docs/flexible/nodejs/configuring-your-app-with-app-yaml
manual_scaling:
    instances: 1
# 以下是 app engine 资源配置，可以自行修改，硬盘最低为 10G
resources:
    cpu: 1
    memory_gb: 0.5
    disk_size_gb: 10
network:
    forwarded_ports:
        - 80:1200
        - 443:1200
# 以下是环境配置示例，具体可配置项见本文档配置章节
env_variables:
    CACHE_EXPIRE: '300'
# [END app_yaml]
```

#### 部署到 standard environment

在 RSSHub 项目根目录下建立一个 app.yaml 文件，内容示例如下：

```yaml
# [START app_yaml]
runtime: nodejs8

network:
    forwarded_ports:
        - 80:1200
        - 443:1200
# 以下是环境配置示例，具体可配置项见本文档配置章节
env_variables:
    CACHE_EXPIRE: '300'
# [END app_yaml]
```

### 安装

在 RSSHub 项目根目录下运行

```bash
gcloud app deploy
```

进行项目部署，如果您需要变更 app.yaml 文件名称或者变更部署的项目 ID 或者指定版本号等，请参考 [Deploying a service](https://cloud.google.com/appengine/docs/flexible/nodejs/testing-and-deploying-your-app#deploying_a_service\_2)。

部署完成后可访问您的 Google App Engine URL 查看部署情况。

## Play with Docker

如果想要测试因为反爬规则导致无法访问的路由，您可以点击下方按钮拉起一套免费，临时，专属于您的 RSSHub

[![Try in PWD](https://raw.githubusercontent.com/play-with-docker/stacks/master/assets/images/button.png)](https://labs.play-with-docker.com/?stack=https://raw.githubusercontent.com/DIYgod/RSSHub/master/docker-compose.yml)

:::caution 注意

-   需要 [DockerHub](https://hub.docker.com) 账号
-   [Play with Docker](https://labs.play-with-docker.com/) 一次仅能使用 4 小时，不能作为持久化解决方案，应当用于测试 / 验证路由规则
-   如果部署完成后不能看到自动识别的端口，请手动点击顶部按钮`open port`并输入`1200`
-   有的时候 PWD 会抽风，如果遇到点击`Start`后空白页面，或者拉起失败，请重试

:::

## 配置

通过设置环境变量来配置 RSSHub

### 网络配置

`PORT`: 监听端口，默认为 `1200`

`SOCKET`: 监听 Unix Socket，默认 `null`

`LISTEN_INADDR_ANY`: 是否允许公网连接，默认 `1`

`REQUEST_RETRY`: 请求失败重试次数，默认 `2`

`REQUEST_TIMEOUT`: 请求超时毫秒数，默认 `3000`

`UA`: 用户代理，默认为随机用户代理用户代理（macOS 上的 Chrome）

`NO_RANDOM_UA`: 是否禁用随机用户代理，默认 `null`

### 跨域请求

RSSHub 默认对跨域请求限制为当前连接所在的域名，即不允许跨域。可以通过 `ALLOW_ORIGIN: *` 或者 `ALLOW_ORIGIN: www.example.com` 以对跨域访问进行修改。

### 缓存配置

RSSHub 支持 `memory` 和 `redis` 两种缓存方式

`CACHE_TYPE`: 缓存类型，可为 `memory` 和 `redis`，设为空可以禁止缓存，默认为 `memory`

`CACHE_EXPIRE`: 路由缓存过期时间，单位为秒，默认 `5 * 60`

`CACHE_CONTENT_EXPIRE`: 内容缓存过期时间，每次访问会重新计算过期时间，单位为秒，默认 `1 * 60 * 60`

`REDIS_URL`: Redis 连接地址（redis 缓存类型时有效），默认为 `redis://localhost:6379/`

`MEMORY_MAX`: 最大缓存数量（memory 缓存类型时有效），默认 `256`

### 代理配置

部分路由反爬严格，可以配置使用代理抓取。

可通过**代理 URI**或**代理选项**或**反向代理**三种方式来配置代理。

#### 代理 URI

`PROXY_URI`: 代理 URI，支持 socks4, socks5（本地查询域名的 SOCKS5，不推荐使用）, socks5h（传域名的 SOCKS5，推荐使用，以防止 DNS 污染或 DNS 泄露）, http, https，具体以 [socks-proxy-agent](https://www.npmjs.com/package/socks-proxy-agent) NPM 包的支持为准，也可参考[curl 中 SOCKS 代理协议的用法](https://daniel.haxx.se/blog/2020/05/26/curl-ootw-socks5/)。

> 代理 URI 的格式为：
>
> -   `{protocol}://{host}:{port}`
> -   `{protocol}://{username}:{password}@{host}:{port}` （带身份凭证）
>
> 一些示例：
>
> -   `socks4://127.0.0.1:1080`
> -   `socks5h://user:pass@127.0.0.1:1080` （用户名为 `user`, 密码为 `pass`）
> -   `socks://127.0.0.1:1080` （protocol 为 socks 时表示 `socks5h`）
> -   `http://127.0.0.1:8080`
> -   `http://user:pass@127.0.0.1:8080`
> -   `https://127.0.0.1:8443`

#### 代理选项

`PROXY_PROTOCOL`: 使用代理，支持 socks，http，https

`PROXY_HOST`: 代理服务器域名或 IP

`PROXY_PORT`: 代理服务器端口

`PROXY_AUTH`: 给代理服务器的身份验证凭证，`Proxy-Authorization: Basic ${process.env.PROXY_AUTH}`

`PROXY_URL_REGEX`: 启用代理的 URL 正则表达式，默认全部开启 `.*`

### 反向代理

:::caution 注意

这种代理方式无法代理包含 cookie 的请求。

:::

`REVERSE_PROXY_URL`: 反向代理地址，RSSHub 将会使用该地址作为前缀来发起请求，例如 `https://proxy.example.com/?target=`，对 `https://google.com` 发起的请求将被自动转换为 `https://proxy.example.com/?target=https%3A%2F%2Fgoogle.com`

你可以使用 Cloudflare Workers 来搭建一个简易的反向代理，例如：

```js
addEventListener('fetch', event => {
  event.respondWith(handleRequest(event.request))
})

async function handleRequest(request) {
  const url = new URL(request.url)
  let target = url.searchParams.get('target')

  if (!target) {
    return new Response('Hello, this is Cloudflare Proxy Service. To proxy your requests, please use the "target" URL parameter.')
  } else {
    target = decodeURIComponent(target)
    const newRequest = new Request(target, {
      headers: request.headers,
      method: request.method,
      body: request.body
    })
    return await fetch(newRequest)
  }
}
```

### 用户认证

`protected_route.js` 内的路由将启用 HTTP Basic Authentication 认证

支持该认证协议的阅读器，在添加源地址时，需要在源地址前添加认证信息，例如：`http://usernam3:passw0rd@rsshub.app/protected/rsshub/routes`。

对于不支持该认证协议的阅读器，请参考 [访问控制配置](#fang-wen-kong-zhi-pei-zhi)。

`HTTP_BASIC_AUTH_NAME`: Http basic authentication 用户名，默认为 `usernam3`，请务必修改

`HTTP_BASIC_AUTH_PASS`: Http basic authentication 密码，默认为 `passw0rd`，请务必修改

### 访问控制配置

RSSHub 支持使用访问密钥 / 码，白名单和黑名单三种方式进行访问控制。开启任意选项将会激活全局访问控制，没有访问权限将会导致访问被拒绝。同时可以通过 `ALLOW_LOCALHOST: true` 赋予所有本地 IP 访问权限。

#### 黑白名单

-   `WHITELIST`: 白名单，设置白名单后黑名单无效

-   `BLACKLIST`: 黑名单

黑白名单支持 IP、路由和 UA，模糊匹配，设置多项时用英文逗号 `,` 隔开，例如 `WHITELIST=1.1.1.1,2.2.2.2,/qdaily/column/59`

#### 访问密钥 / 码

-   `ACCESS_KEY`: 访问密钥，用于直接访问所有路由或者生成访问码

访问码为 访问密钥 + 路由 共同生成的 md5，例如：

| 访问密钥    | 路由              | 生成过程                                 | 访问码                           |
| ----------- | ----------------- | ---------------------------------------- | -------------------------------- |
| ILoveRSSHub | /qdaily/column/59 | md5('/qdaily/column/59' + 'ILoveRSSHub') | 0f820530128805ffc10351f22b5fd121 |

-   此时可以通过 `code` 访问路由，例如：<https://rsshub.app/qdaily/column/59?code=0f820530128805ffc10351f22b5fd121>

-   或使用访问密钥 `key` 直接访问所有路由，例如：<https://rsshub.app/qdaily/column/59?key=ILoveRSSHub>

访问密钥 / 码与黑白名单的访问控制关系如下：

|            | 正确访问密钥 / 码 | 错误访问密钥 / 码 | 无访问密钥 / 码 |
| ---------- | ----------------- | ----------------- | --------------- |
| 在白名单中 | ✅                | ✅                | ✅              |
| 在黑名单中 | ✅                | ❌                | ❌              |
| 无黑白名单 | ✅                | ❌                | ❌              |

### 日志配置

`DEBUG_INFO`: 是否在首页显示路由信息。值为非 `true` `false` 时，在请求中带上参数 `debug` 开启显示，例如：<https://rsshub.app/?debug=value_of_DEBUG_INFO> 。默认 `true`

`LOGGER_LEVEL`: 指明输出到 console 和日志文件的日志的最大 [等级](https://github.com/winstonjs/winston#logging-levels)，默认 `info`

`NO_LOGFILES`: 是否禁用日志文件输出，默认 `false`

`SENTRY`: [Sentry](https://sentry.io) dsn，用于错误追踪

`SENTRY_ROUTE_TIMEOUT`: 路由耗时超过此毫秒值上报 Sentry，默认 `3000`

### 图片处理

:::tip 新配置方式

我们正在试验新的，更灵活的配置方式。如果有需要，请转到 [通用参数 -> 多媒体处理](/zh/parameter#多媒体处理) 了解更多。

在使用新配置时，请将下方环境变量留空。否则默认图片模版会继续遵循下方配置。

:::

`HOTLINK_TEMPLATE`: 用于处理描述中图片的 URL，绕过防盗链等限制，留空不生效。用法参考 [#2769](https://github.com/DIYgod/RSSHub/issues/2769)。可以使用 [URL](https://developer.mozilla.org/en-US/docs/Web/API/URL#Properties) 的所有属性（加上后缀 `_ue` 则会对其进行 URL 编码），格式为 JS 变量模板。例子：`${protocol}//${host}${pathname}`, `https://i3.wp.com/${host}${pathname}`, `https://images.weserv.nl?url=${href_ue}`

`HOTLINK_INCLUDE_PATHS`: 限制需要处理的路由，只有匹配成功的路由会被处理，设置多项时用英文逗号 `,` 隔开。若不设置，则所有路由都将被处理

`HOTLINK_EXCLUDE_PATHS`: 排除不需处理的路由，所有匹配成功的路由都不被处理，设置多项时用英文逗号 `,` 隔开。可单独使用，也可用于排除已被前者包含的路由。若不设置，则没有任何路由会被过滤

:::tip 路由匹配模式

`HOTLINK_INCLUDE_PATHS` 和 `HOTLINK_EXCLUDE_PATHS` 均匹配路由根路径及其所有递归子路径，但并非子字符串匹配。注意必须以 `/` 开头，且结尾不需要 `/`。

例：`/example`, `/example/sub` 和 `/example/anthoer/sub/route` 均可被 `/example` 匹配，但 `/example_route` 不会被匹配。

也可带有路由参数，如 `/weibo/user/2612249974` 也是合法的。

:::

### 功能特性

:::tip 测试特性

这个板块控制的是一些新特性的选项，他们都是**默认关闭**的。如果有需要请阅读对应说明后按需开启

:::

`ALLOW_USER_HOTLINK_TEMPLATE`: [通用参数 -> 多媒体处理](/zh/parameter#多媒体处理)特性控制

`FILTER_REGEX_ENGINE`: 控制 [通用参数 -> 内容过滤](/zh/parameter#内容过滤) 使用的正则引擎。可选`[re2, regexp]`，默认`re2`。我们推荐公开实例不要调整这个选项，这个选项目前主要用于向后兼容。

`ALLOW_USER_SUPPLY_UNSAFE_DOMAIN`: 允许用户为路由提供域名作为参数。建议公共实例不要调整此选项，开启后可能会导致 [服务端请求伪造（SSRF）](https://owasp.org/www-community/attacks/Server_Side_Request_Forgery)

### 其他应用配置

`DISALLOW_ROBOT`: 阻止搜索引擎收录，默认开启，设置 false 或 0 关闭

`ENABLE_CLUSTER`: 是否开启集群模式，默认 `false`

`NODE_ENV`: 是否显示错误输出，默认 `production` （即关闭输出）

`NODE_NAME`: 节点名，用于负载均衡，识别当前节点

`PUPPETEER_WS_ENDPOINT`: 用于 puppeteer.connect 的浏览器 websocket 链接，见 [browserWSEndpoint](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&show=api-browserwsendpoint)

`CHROMIUM_EXECUTABLE_PATH`: Chromium（或 Chrome）的可执行路径。若 puppeteer 没有下载捆绑的 Chromium（主动跳过下载或体系架构为 arm/arm64），设置此项可启用 puppeteer。或者，偏好 Chrome 而不是 Chromium 时，此项也很有用。**注意**：`PUPPETEER_WS_ENDPOINT` 被设置时，此项不生效；仅在手动部署时有用，对于 Docker 部署，请改用 `chromium-bundled` 版本镜像。

`TITLE_LENGTH_LIMIT`: 限制输出标题的字节长度，一个英文字符的长度为 1 字节，部分语言如中文，日文，韩文或阿拉伯文等，统一算作 2 字节，默认 `150`

### 部分 RSS 模块配置

:::tip 提示

此处信息不完整。完整配置请参考路由对应的文档和 `lib/config.js`。

:::

-   4399 论坛

    -   `GAME_4399`: 对应登录后的 cookie 值，获取方式：
        1.  在 4399 首页登录。
        2.  打开开发者工具，切换到 Network 面板，刷新
        3.  查找`www.4399.com`的访问请求，点击请求，在右侧 Headers 中找到 Cookie.

-   bilibili 用户关注动态系列路由

    -   `BILIBILI_COOKIE_{uid}`: 对应 uid 的 b 站用户登录后的 Cookie 值，`{uid}` 替换为 uid，如 `BILIBILI_COOKIE_2267573`，获取方式：
        1.  打开 <https://api.vc.bilibili.com/dynamic_svr/v1/dynamic_svr/dynamic_new?uid=0&type=8>
        2.  打开控制台，切换到 Network 面板，刷新
        3.  点击 dynamic_new 请求，找到 Cookie
        4.  视频和专栏，UP 主粉丝及关注只要求 `SESSDATA` 字段，动态需复制整段 Cookie

-   Bitbucket: [Basic auth with App passwords](https://developer.atlassian.com/cloud/bitbucket/rest/intro/#basic-auth)

    -   `BITBUCKET_USERNAME`: 你的 Bitbucket 用户名
    -   `BITBUCKET_PASSWORD`: 你的 Bitbucket 密码

-   BTBYR

    -   `BTBYR_HOST`: 支持 ipv4 访问的 BTBYR 镜像，默认为原站 `https://bt.byr.cn/`。
    -   `BTBYR_COOKIE`: 注册用户登录后的 Cookie 值，获取方式：
        1.  登录后打开网站首页
        2.  打开控制台，刷新
        3.  找到 <https://bt.byr.cn/index.php> 请求
        4.  找到请求头中的 Cookie

-   BUPT

    -   `BUPT_PORTAL_COOKIE`: 登录后获得的 Cookie 值，获取方式
        1.  打开<https://webapp.bupt.edu.cn/wap/login.html?redirect=https://>并登录
        2.  无视掉报错，并打开 <https://webapp.bupt.edu.cn/extensions/wap/news/list.html?p-1&type=xnxw>
        3.  打开控制台，刷新
        4.  找到 <https://webapp.bupt.edu.cn/extensions/wap/news/list.html?p-1&type=xnxw> 请求
        5.  找到请求头中的 Cookie

-   Civitai
    -   `CIVITAI_COOKIE`: Civitai 登录后的 cookie 值

-   discuz cookies 设定

    -   `DISCUZ_COOKIE_{cid}`: 某 Discuz 驱动的论坛，用户注册后的 Cookie 值，cid 可自由设定，取值范围 \[00, 99], 使用 discuz 通用路由时，通过指定 cid 来调用该 cookie

-   disqus 全部路由：[申请地址](https://disqus.com/api/applications/)

    -   `DISQUS_API_KEY`: Disqus API

-   E-Hentai

    -   `EH_IPB_MEMBER_ID`: E-Hentai 账户登录后 cookie 的 `ipb_member_id` 值
    -   `EH_IPB_PASS_HASH`: E-Hentai 账户登录后 cookie 的 `ipb_pass_hash` 值
    -   `EH_SK`: E-Hentai 账户登录后 cookie 中的`sk`值
    -   `EH_IGNEOUS`: ExHentai 账户登录后 cookie 中的`igneous`值。若设置此值，RSS 数据将全部从里站获取
    -   `EH_STAR`: E-Hentai 账户获得捐赠等级后将出现该 cookie。若设置此值，图片访问量限制将与账号关联而非 IP 地址
    -   `EH_IMG_PROXY`: 封面代理访问地址。若设置此值，封面图链接将被替换为以此值开头。使用 ExHentai 时，封面图需要有 Cookie 才能访问，在一些阅读软件上没法显示封面，可以使用此值搭配一个加 Cookie 的代理服务器实现阅读软件无 Cookie 获取封面图。

-   Fantia

    -   `FANTIA_COOKIE`: 登录后的 `cookie` , 可以在控制台中查看请求头获取。如果不填会导致部分需要登录后才能阅读的帖子获取异常

-   Gitee 全部路由：[申请地址](https://gitee.com/api/v5/swagger)

    -   `GITEE_ACCESS_TOKEN`: Gitee 私人令牌

-   GitHub 全部路由：[申请地址](https://github.com/settings/tokens)

    -   `GITHUB_ACCESS_TOKEN`: GitHub Access Token

-   Google Fonts：[申请地址](https://developers.google.com/fonts/docs/developer_api#a_quick_example)

    -   `GOOGLE_FONTS_API_KEY`: API key

-   Instagram:

    -   `IG_USERNAME`: Instagram 用户名（仅 Private API）
    -   `IG_PASSWORD`: Instagram 密码（仅 Private API）
    -   `IG_PROXY`: Instagram 代理 URL（仅 Private API，可选）
    -   `IG_COOKIE`: Instagram 登录后的 Cookie（仅 Cookie）

    注意，暂**不支持**两步验证。

-   Iwara:

    -   `IWARA_USERNAME`: Iwara 用户名
    -   `IWARA_PASSWORD`: Iwara 密码

-   Last.fm 全部路由：[申请地址](https://www.last.fm/api/)

    -   `LASTFM_API_KEY`: Last.fm API Key

-   Mastodon 用户时间线路由：访问 `https://mastodon.example/settings/applications` 申请（替换掉 `mastodon.example`）。需要 `read:search` 权限

    -   `MASTODON_API_HOST`: API 请求的实例，仅域名，不包括 `http://` 或 `https://` 协议头
    -   `MASTODON_API_ACCESS_TOKEN`: 用户 access token, 申请应用后，在应用配置页可以看到申请者的 access token
    -   `MASTODON_API_ACCT_DOMAIN`: 该实例本地用户 acct 标识的域名，Webfinger account URI，形如 `user@host`

-   Medium 相关路由：打开控制台，复制 Cookie（理论上只需要 uid 和 sid 即可）

    -   `MEDIUM_ARTICLE_COOKIE`：请求全文时使用的 Cookie，存在活跃的 Member 订阅时可获取付费内容全文
    -   `MEDIUM_COOKIE_{username}`：对应 username 的用户的 Cookie，个性推荐相关路由需要

-   MiniFlux 全部路由：

    -   `MINIFLUX_INSTANCE`： 用户所用的实例，默认为 MiniFlux 官方提供的 [付费服务地址](https://reader.miniflux.app)
    -   `MINIFLUX_TOKEN`: 用户的 API 密钥，请登录所用实例后于 `设置` -> `API 密钥` -> `创建一个新的 API 密钥` 处获取

-   NGA BBS 用于获取帖子内文

    -   `NGA_PASSPORT_UID`: 对应 cookie 中的 `ngaPassportUid`.
    -   `NGA_PASSPORT_CID`: 对应 cookie 中的 `ngaPassportCid`.

-   nhentai torrent：[注册地址](https://nhentai.net/register/)

    -   `NHENTAI_USERNAME`: nhentai 用户名或邮箱
    -   `NHENTAI_PASSWORD`: nhentai 密码

-   Notion
    -   `NOTION_TOKEN`: Notion 内部集成 Token，请按照[Notion 官方指引](https://developers.notion.com/docs/authorization#internal-integration-auth-flow-set-up)申请 Token

-   pianyuan 全部路由：[注册地址](https://pianyuan.org)

    -   `PIANYUAN_COOKIE`: 对应 cookie 中的 `py_loginauth`, 例: PIANYUAN_COOKIE='py_loginauth=xxxxxxxxxx'

-   pixiv 全部路由：[注册地址](https://accounts.pixiv.net/signup)

    -   `PIXIV_REFRESHTOKEN`: Pixiv Refresh Token, 请参考 [此文](https://gist.github.com/ZipFile/c9ebedb224406f4f11845ab700124362) 获取，或自行对客户端抓包获取
    -   `PIXIV_BYPASS_CDN`: 绕过 Pixiv 前置的 Cloudflare CDN, 使用`PIXIV_BYPASS_HOSTNAME`指示的 IP 地址访问 Pixiv API, 可以解决因 Cloudflare 机器人验证导致的登录失败问题，默认关闭，设置 true 或 1 开启
    -   `PIXIV_BYPASS_HOSTNAME`: Pixiv 源站的主机名或 IP 地址，主机名会被解析为 IPv4 地址，默认为`public-api.secure.pixiv.net`；仅在`PIXIV_BYPASS_CDN`开启时生效
    -   `PIXIV_BYPASS_DOH`: 用于解析 `PIXIV_BYPASS_HOSTNAME` 的 DoH 端点 URL，需要兼容 Cloudflare 或 Google 的 DoH 服务的 JSON 查询格式，默认为 `https://1.1.1.1/dns-query`
    -   `PIXIV_IMG_PROXY`: 用于图片地址的代理，因为 pixiv 图片有防盗链，默认为 `https://i.pixiv.re`

-   pixiv fanbox 用于获取付费内容

    -   `FANBOX_SESSION_ID`: 对应 cookies 中的`FANBOXSESSID`。

-   Saraba1st 用于获取帖子里的图片

    -   `SARABA1ST_COOKIE`: 对应网页端的 Cookie。

-   Sci-hub 设置，用于科学期刊路由。

    -   `SCIHUB_HOST`: 可访问的 sci-hub 镜像地址，默认为 `https://sci-hub.se`。

-   Spotify 全部路由：[注册地址](https://developer.spotify.com)

    -   `SPOTIFY_CLIENT_ID`: Spotify 应用的 client ID
    -   `SPOTIFY_CLIENT_SECRET`: Spotify 应用的 client secret

-   Spotify 用户相关路由

    -   `SPOTIFY_REFRESHTOKEN`：用户在此 Spotify 应用的 refresh token。可以利用 [此 gist](https://gist.github.com/outloudvi/d1bbeb5e989db5385384a223a7263744) 获取。

-   Telegram - 贴纸包路由：[Telegram 机器人](https://telegram.org/blog/bot-revolution)

    -   `TELEGRAM_TOKEN`: Telegram 机器人 token

-   Twitter 全部路由：[申请地址](https://apps.twitter.com)

    -   `TWITTER_CONSUMER_KEY`: Twitter Developer API key，支持多个 key，用英文逗号 `,` 隔开
    -   `TWITTER_CONSUMER_SECRET`: Twitter Developer API key secret，支持多个 key，用英文逗号 `,` 隔开，顺序与 key 对应
    -   `TWITTER_WEBAPI_AUTHORIZAION`: Twitter Web API authorization，格式为 `key:secret` 或 `Bearer ****`，支持多个，用英文逗号 `,` 隔开。如果上述两个环境变量中的任意一个未设置，就会使用 Twitter Web API。然而，没有必要设置这个环境变量，因为 RSSHub 已经内置了目前已知可用的 token。
    -   `TWITTER_TOKEN_{handler}`: 对应 Twitter 用户名生成的 token，`{handler}` 替换为用于生成该 token 的 Twitter 用户名，值为 `Twitter API key, Twitter API key secret, Access token, Access token secret` 用逗号隔开，例如：`TWITTER_TOKEN_RSSHub=bX1zry5nG4d1RbESQbnADpVIo,2YrD8qo9sXbB8VlYfVmo1Qtw0xsexnOliU5oZofq7aPIGou0Xx,123456789-hlkUHFYmeXrRcf6SEQciP8rP4lzmRgMgwdqIN9aK,pHcPnfa28rCIKhSICUCiaw9ppuSSl7T2f3dnGYpSM0bod`

-   Wordpress

    -   `WORDPRESS_CDN`: 用于中转 http 图片链接。可供考虑的服务见下表：

        | url                                      | backbone     |
        | ---------------------------------------- | ------------ |
        | <https://imageproxy.pimg.tw/resize?url=> | akamai       |
        | <https://images.weserv.nl/?url=>         | cloudflare   |
        | <https://pic1.xuehuaimg.com/proxy/>      | cloudflare   |
        | <https://cors.netnr.workers.dev/>        | cloudflare   |
        | <https://netnr-proxy.openode.io/>        | digitalocean |

-   YouTube：[申请地址](https://console.developers.google.com/)

    -   全部路由
        -   `YOUTUBE_KEY`: YouTube API Key，支持多个 key，用英文逗号 `,` 隔开
    -   订阅列表路由额外设置
        -   `YOUTUBE_CLIENT_ID`: YouTube API 的 OAuth 2.0 客户端 ID
        -   `YOUTUBE_CLIENT_SECRET`: YouTube API 的 OAuth 2.0 客户端 Secret
        -   `YOUTUBE_REFRESH_TOKEN`: YouTube API 的 OAuth 2.0 客户端 Refresh Token。可以按照[此 gist](https://gist.github.com/Kurukshetran/5904e8cb2361623498481f4a9a1338aa) 获取。

-   ZodGame:

    -   `ZODGAME_COOKIE`: ZodGame 登录后的 Cookie 值

-   北大未名 BBS 全站十大

    -   `PKUBBS_COOKIE`: BBS 注册用户登录后的 Cookie 值，获取方式：
        1.  登录后打开论坛首页
        2.  打开控制台， 刷新
        3.  找到 <https://bbs.pku.edu.cn/v2/home.php> 请求
        4.  找到请求头中的 Cookie

-   吹牛部落 栏目更新

    -   `CHUINIU_MEMBER`: 吹牛部落登录后的 x-member，获取方式
        1.  登陆后点开文章正文
        2.  打开控制台，刷新
        3.  找到 <http://api.duanshu.com/h5/content/detail/> 开头的请求
        4.  找到请求头中的 x-member

-   滴答清单

    -   `DIDA365_USERNAME`: 滴答清单用户名
    -   `DIDA365_PASSWORD`: 滴答清单密码

-   端传媒设置，用于获取付费内容全文：

    -   `INITIUM_BEARER_TOKEN`: 端传媒 Web 版认证 token。获取方式：登陆后打开端传媒站内任意页面，打开浏览器开发者工具中 “网络”(Network) 选项卡，筛选 URL 找到任一个地址为`api.initium.com`开头的请求，点击检查其 “消息头”，在 “请求头” 中找到`Authorization`字段，将其值复制填入配置即可。你的配置应该形如`INITIUM_BEARER_TOKEN: 'Bearer eyJxxxx......xx_U8'`。使用 token 部署的好处是避免占据登陆设备数的额度，但这个 token 一般有效期为两周，因此只可作临时测试使用。

    -   `INITIUM_IAP_RECEIPT`: 端传媒 iOS 版内购回执认证 token。获取方式：登陆后打开端传媒 iOS app 内任意页面，打开抓包工具，筛选 URL 找到任一个地址为`api.initium.com`开头的请求，点击检查其 “消息头”，在 “请求头” 中找到`X-IAP-Receipt`字段，将其值复制填入配置即可。你的配置应该形如`INITIUM_IAP_RECEIPT: 'ef81dee9e4e2fe084a0af1ea82da2f7b16e75f756db321618a119fa62b52550e'`。

    Web 版认证 token 和 iOS 内购回执认证 token 只需选择其一填入即可。你也可选择直接在环境设置中填写明文的用户名和密码：

    -   `INITIUM_USERNAME`: 端传媒用户名 （邮箱）
    -   `INITIUM_PASSWORD`: 端传媒密码

-   豆瓣想看

    -   `DOUBAN_COOKIE`: 豆瓣登陆后的 Cookie 值

-   饭否 全部路由：[申请地址](https://github.com/FanfouAPI/FanFouAPIDoc/wiki/Oauth)

    -   `FANFOU_CONSUMER_KEY`: 饭否 Consumer Key
    -   `FANFOU_CONSUMER_SECRET`: 饭否 Consumer Secret
    -   `FANFOU_USERNAME`: 饭否登录用户名、邮箱、手机号
    -   `FANFOU_PASSWORD`: 饭否密码

-   和风天气：[申请地址](https://id.qweather.com/#/register?redirect=https%3A%2F%2Fconsole.qweather.com)

    -   `HEFENG_KEY`:API key

-   南方周末付费全文

    -   `INFZM_COOKIE`: infzm 账户登陆后的 cookie，目前只需要 `passport_session=...` 即可获取全文

-   轻小说文库

    -   `WENKU8_COOKIE`: 登陆轻小说文库后的 cookie

-   色花堂

    -   `SEHUATANG_COOKIE`: 登陆色花堂后的 cookie 值。

-   邮箱 邮件列表路由：

    -   `EMAIL_CONFIG_{email}`: 邮箱设置，替换 `{email}` 为 邮箱账号，邮件账户的 `@` 与 `.` 替换为 `_`，例如 `EMAIL_CONFIG_xxx_qq_com`。Linux 内容格式为 `password=密码&host=服务器&port=端口`，docker 内容格式为 `password=密码&host=服务器&port=端口`，例如：
        -   Linux 环境变量：`EMAIL_CONFIG_xxx_qq_com="password=123456&host=imap.qq.com&port=993"`
        -   docker 环境变量：`EMAIL_CONFIG_xxx_qq_com=password=123456&host=imap.qq.com&port=993`，请勿添加引号 `'`，`"`。

-   网易云歌单及听歌排行

    -   `NCM_COOKIES`: 网易云音乐登陆后的 cookie 值.

-   微博 个人时间线路由：[申请地址](https://open.weibo.com/connect)

    -   `WEIBO_APP_KEY`: 微博 App Key
    -   `WEIBO_APP_SECRET`: 微博 App Secret
    -   `WEIBO_REDIRECT_URL`: 微博登录授权回调地址，默认为 `RSSHub 地址/weibo/timeline/0`，自定义回调地址请确保最后可以转跳到 `RSSHub 地址/weibo/timeline/0?code=xxx`

-   微博 自定义分组

    -   `WEIBO_COOKIES`: 用户访问网页微博时所使用的 cookie, 获取方式:
        1.  打开并登录 <https://m.weibo.cn> (确保打开页面为手机版，如果强制跳转电脑端可尝试使用可更改 UserAgent 的浏览器插件)
        2.  按下`F12`打开控制台，切换至`Network(网络)`面板
        3.  在该网页切换至任意关注分组，并在面板打开最先捕获到的请求 (该情形下捕获到的请求路径应包含`/feed/group`)
        4.  查看该请求的`Headers(请求头)`, 找到`Cookie`字段并复制内容

-   小宇宙：需要 App 登陆后抓包获取相应数据。

    -   `XIAOYUZHOU_ID`: 即数据包中的 `x-jike-device-id`。
    -   `XIAOYUZHOU_TOKEN`: 即数据包中的 `x-jike-refresh-token`。

-   新榜

    -   `NEWRANK_COOKIE`: 登陆后的 COOKIE 值，其中 token 是必要的，其他可删除

-   喜马拉雅

    -   `XIMALAYA_TOKEN`: 对应 cookie 中的 `1&_token`，获取方式：
        1.  登陆喜马拉雅网页版
        2.  打开控制台，刷新
        3.  查找名称为`1&_token`的`cookie`，其内容即为`XIMALAYA_TOKEN`的值（即在`cookie` 中查找 `1&_token=***;`，并设置 `XIMALAYA_TOKEN = ***`）

-   知乎用户关注时间线

    -   `ZHIHU_COOKIES`: 知乎登录后的 cookie 值.
        1.  可以在知乎网页版的一些请求的请求头中找到，如 `GET /moments` 请求头中的 `cookie` 值.
