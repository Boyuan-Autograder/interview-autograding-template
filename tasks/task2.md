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
   - Docker Desktop 已自带 Compose v2 插件，通常无需额外安装
6. Compose 拉起服务: 能通过预置的 [task2/compose.yml](../task2/compose.yml) 拉起一个 nginx 服务并验证

### 关于 Compose 拉起验证

评测工具会用仓库里预置的 `task2/compose.yml` 自动执行一次完整的编排闭环，你**无需修改**该文件：

```bash
# 评测工具会依次执行：
docker compose -f task2/compose.yml up -d    # 拉起服务
docker compose -f task2/compose.yml ps       # 检查容器处于 running
curl http://localhost:8080                    # 验证 nginx 默认页返回
docker compose -f task2/compose.yml down     # 清理，不留副作用
```

这一步考察的是你"会用 Compose 编排服务"，因此**本地需能正常拉取 `nginx` 镜像**。若镜像拉取慢或失败，可配置镜像加速，或用代理确保容器运行时能访问 Docker Hub。

## 评测

完成安装后，在仓库根目录运行评测命令：

```bash
npx autograding grade
```

评测结束会生成加密报告单 `autograding_report.json`，commit 并 push 后可在 Actions 运行记录里查看得分。
