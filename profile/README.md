这里是[ 2024 秋季学期中山大学分布式系统课程实验](https://bk.tencent.com/s-mart/community/question/14194)所用的各种仓库

使用开发容器作为更现代化的环境配置方式，也方便同学们减少配置环境上浪费时间

一些统一的注意事项：
- [如何使用 Github Package](https://docs.github.com/zh/packages/working-with-a-github-packages-registry/working-with-the-container-registry)（或者说 ghcr.io 容器镜像注册表）
- docker 拉取的时候，格式均为：`docker pull ghcr.io/ds-hw-fuuzen/<repo-name>:<branch-name>`
- 为了方便使用 git 开发，我将宿主机器的 `${HOME}/.ssh` 挂载到了容器里，使用 ssh 方式对 github 进行 pull 和 push 等操作更加方便
- 环境变量通过 `docker-compose.yml` 或 `devcontainer.json` 硬编码注入容器
- 所有开发容器我都用 fish 作为 shell
