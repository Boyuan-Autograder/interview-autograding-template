# Task 2: 安装 Docker

在先前的任务中，你已经成功搭建了 WSL2 环境并学习了 Linux 的基本使用方式。现在，我们将安装和配置 Docker，这是现代软件开发中最重要的工具之一。Docker 允许你在隔离的容器中打包和运行应用程序，确保它们在任何地方都能以相同的方式工作。

**我们鼓励你通过查阅手册、上网搜索、向大语言模型提问来解决你遇到的问题。**

## 参考资料

Docker 官方的安装教程: https://docs.docker.com/engine/install/

## 评测内容

1. Docker 命令可用: 在终端可以直接运行 docker 命令
2. Docker 服务正在运行: Docker 的后台守护进程必须是启动且在运行状态
3. 用户权限正确: 你必须能够不使用 sudo 就运行 Docker 命令
    - hint: 这通常需要将你的当前用户添加到 docker 用户组，并重启终端
4. 容器能成功运行: 能够成功从 Docker Hub 拉取 hello-world 镜像并运行它
    - hint: 确保网络环境正常，如果无法正常连通，可使用第三方镜像进行加速
5. Docker Compose 已安装: 支持 `docker compose`（v2 插件）或 `docker-compose`（v1 独立命令）任一
6. Compose 拉起服务: 你需手动用预置的 [task2/compose.yml](../task2/compose.yml) 拉起一个 nginx 服务，评测工具只验证该服务是否真的可达

### 关于 Compose 拉起验证

这一步考察"会用 Compose 编排服务"，因此**拉起与清理由你手动完成**，评测工具只做只读验证。你**无需修改**预置的 `task2/compose.yml`：

```bash
# 1. 在运行 grade 之前，你手动拉起服务：
docker compose -f task2/compose.yml up -d

# 2. 运行评测（此时 nginx 必须已处于 running）：
npx autograding grade
#    评测工具会执行：
#    docker compose -f task2/compose.yml ps     # 确认 autograding-task2-nginx 容器 running
#    curl http://localhost:8080                 # 验证 nginx 默认页含 "Welcome to nginx!"

# 3. grade 结束后，你手动清理：
docker compose -f task2/compose.yml down
```

本地需能正常拉取 `nginx` 镜像。若镜像拉取慢或失败，可配置镜像加速或用代理确保容器运行时能访问 Docker Hub。

## 评测

完成任务后，在仓库根目录运行评测命令：

```bash
npx autograding grade
```

评测结束会生成加密报告单 `autograding_report.json`，commit 并 push 后可在 Actions 运行记录里查看得分。
