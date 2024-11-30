这里是[ 2024 秋季学期中山大学分布式系统课程实验](https://bk.tencent.com/s-mart/community/question/14194)所用的各种仓库

使用开发容器作为更现代化的环境配置方式，开箱即用，也方便同学们减少配置环境上浪费时间

|  仓库  | 镜像状态 |
| --- | --- |
| [hw1](https://github.com/ds-hw-fuuzen/hw1) |[![hw1](https://github.com/ds-hw-fuuzen/hw1/actions/workflows/docker-publish.yml/badge.svg)](https://github.com/ds-hw-fuuzen/hw1/actions/workflows/docker-publish.yml) |
| [hw2-frontend](https://github.com/ds-hw-fuuzen/hw2-frontend) | [![hw2-frontend](https://github.com/ds-hw-fuuzen/hw2-frontend/actions/workflows/docker-publish.yml/badge.svg)](https://github.com/ds-hw-fuuzen/hw2-frontend/actions/workflows/docker-publish.yml)  |
[hw2-backend](https://github.com/ds-hw-fuuzen/hw2-backend) | [![](https://github.com/ds-hw-fuuzen/hw2-backend/actions/workflows/docker-publish.yml/badge.svg)](https://github.com/ds-hw-fuuzen/hw2-backend/actions/workflows/docker-publish.yml) |
| [hw3-frontend](https://github.com/ds-hw-fuuzen/hw3-frontend) | [![hw3-frontend](https://github.com/ds-hw-fuuzen/hw3-frontend/actions/workflows/docker-publish.yml/badge.svg)](https://github.com/ds-hw-fuuzen/hw3-frontend/actions/workflows/docker-publish.yml)  |
[hw3-backend](https://github.com/ds-hw-fuuzen/hw3-backend) | [![](https://github.com/ds-hw-fuuzen/hw2-backend/actions/workflows/docker-publish.yml/badge.svg)](https://github.com/ds-hw-fuuzen/hw2-backend/actions/workflows/docker-publish.yml) |

## 使用 VSCode + docker 开发

### 准备工作

如果您没有使用过 ghcr.io 容器镜像注册表，请先参考[如何使用 Github Package](https://docs.github.com/zh/packages/working-with-a-github-packages-registry/working-with-the-container-registry)，在 github 上生成 token 用于登陆 ghcr.io

- docker 登录 ghcr.io
- 克隆对应实验仓库到您的电脑中
- 确保您的 VSCode 安装了 Dev-Containers 插件
- VSCode 打开仓库

### SSH

为了方便使用 git 开发，我将宿主机器的 `${HOME}/.ssh` 挂载到了容器里，使用 ssh 方式对 github 进行 pull 和 push 等操作更加方便

如果你是 Windows 平台,请注意 `.devcontainer/docker-compose.yml` 中如下内容：

``` yaml yaml
services:
  app:
    # ......
    volumes:
      - ${HOME}/.ssh:/root/.ssh
```

修改为硬编码正斜杠路径，例如我的用户名 `fuuzen`:

``` yaml yaml
services:
  app:
    # ......
    volumes:
      - /c/Users/fuuzen/.ssh:/root/.ssh
```

### 环境变量

环境变量通过 `docker-compose.yml` 或 `devcontainer.json` 硬编码注入容器。

以 `docker-compose.yml` 为例：

```yaml yaml
services:
  app:
    # ......
    environment:
      - BKPAAS_APP_ID=${YOUR_APP_ID}
      - BKPAAS_APP_SECRET=${YOUR_APP_SECRET}
      - BKPAAS_MAJOR_VERSION=3
      - BK_PAAS2_URL=https://ce.bktencent.com
      - BK_COMPONENT_API_URL=https://bkapi.ce.bktencent.com
      - BKPAAS_LOGIN_URL=https://ce.bktencent.com/login/
      - DEV_DB_HOST=db
      - DEV_DB_PORT=3306
      - DEV_DB_USER=root
      - DEV_DB_PASSWORD=root
      - DEV_DB_NAME=${YOUR_APP_ID}
    # ......
  db:
    # ......
    environment:
      MYSQL_DATABASE=${YOUR_APP_ID}
    # ......
```

在你的开发中，请先到腾讯蓝鲸平台创建自己的 APP，将 APP 的 ID 和 SECRET 填写到这里，替换 `${YOUR_APP_ID}` 和 `${YOUR_APP_SECRET}`。

不同的仓库对其他环境变量要求可能不一样，注意自己修改。

### 前后端对接

> 对于一些前后端分离的项目，由于 VSCode 进行 docker compose 的时候会强制指定自定义的 project_name，会导致 `docker-compose.yml` 中自定义网络接口被强制加上 project_name 前缀，导致前后端会创建 2 个不同网桥，但不影响前后端对接。

由于鉴权需要，本系列实验都要求开发主机名为 `dev.ce.bktencent.com`，使用本地浏览器调试的时候都需要配置自己机器的 `hosts` 添加：

```
127.0.0.1 dev.ce.bktencent.com
```

前端访问后端的 API 来自环境变量 `BK_BACKEND_API_PREFIX=http://dev.ce.bktencent.com:8000`，这里后端 Django 默认使用 `8000` 端口，物理主机会转发这个端口到自己的 `8000` 端口上（就像转发前端的 `5000` 端口一样）。所以前端访问后端服务时，会直接访问主机转发的后端服务端口。

前后端开发时同时打开两个项目 VSCode 窗口即可，不需要额外配置。

### 开始开发

`ctrl + shift + p` (`command + shift + p` for mac) 输入 Reopen with container 使用开发容器打开,若没有本地还镜像将自动拉取镜像。

您也可以手动拉取:

```shell shell
docker pull ghcr.io/ds-hw-fuuzen/<repo-name>:<branch-name>
```

所有开发容器我都用 fish 作为 shell，这是构建容器的时候已经决定的（fish 蛮好用的）
