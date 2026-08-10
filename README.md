# 面试评测模板

这是一个用于面试技术评测的模板仓库。候选人通过 "Use this template" 在自己账号下创建派生仓库，在仓库目录内运行评测工具完成四个 task 的环境检查，commit 加密报告单后 push，GitHub Actions 会自动解密并在运行记录里展示得分面板。

## 前置依赖

开始前请确保你的机器已安装以下软件。四个 task 的评测分别在本地运行，环境检查会测试你的机器。

| 依赖 | 版本要求 | 说明 | 官方下载 |
| --- | --- | --- | --- |
| Node.js | 20+ | 运行评测工具（`npx autograding grade`） | https://nodejs.org/ |
| Docker Desktop | 最新（需运行中） | Task 2 和 Task 3 需要；Task 4 需要构建镜像 | https://www.docker.com/products/docker-desktop/ |
| Git | 任意 | Task 1 检查；评测报告归属需要 `git config user.name` | https://git-scm.com/ |
| Python 3 | 任意 | Task 1 检查 | https://www.python.org/ |

> **Windows 用户**：Task 1 建议使用 WSL2（Windows Subsystem for Linux）提供 Linux 环境，详见 [How to install Linux on Windows with WSL](https://learn.microsoft.com/en-us/windows/wsl/install)。如果你使用 macOS 或原生 Linux，可跳过 WSL 直接开始。

## 快速开始

1. **创建派生仓库**：在本仓库页面点击右上角 **"Use this template"**，在你自己的 GitHub 账号下创建一个新仓库。
2. **克隆到本地**：
   ```bash
   git clone <你的派生仓库地址>
   cd <你的派生仓库目录>
   ```
3. **安装依赖**：
   ```bash
   npm install
   ```
4. **完成 Task 4 的 Makefile**（可选但推荐先做，否则 Task 4 会得 0 分）：
   ```bash
   cp starter_makefile Makefile
   # 编辑 Makefile，完成所有 TODO 标记的部分
   ```
5. **运行评测**（在仓库根目录）：
   ```bash
   npx autograding grade
   ```
   工具会依次运行四个 task 的环境检查，并在终端交互式回答 Task 3 的选择题。评分结束后，仓库根会生成加密报告单 `autograding_report.json`。
6. **提交并推送报告单**：
   ```bash
   git add autograding_report.json
   git commit -m "提交评测报告"
   git push
   ```
7. **查看得分**：push 后打开仓库的 **Actions** 标签页，找到最新一次运行记录，在 Job Summary 里可以看到你的总分、各 task 得分和各检查项 pass/fail 明细。

> 你可以想什么时候交就什么时候交——完成一个 task 就交一次，或全部做完一次性交，系统只看最后一次 push 的报告单。

## 四个 Task

点击下方链接查看每个 task 的详细说明：

- [**Task 1: 配置运行环境**](./tasks/task1.md) — WSL2 / Linux 环境配置，检查 OS、Git、Python3
- [**Task 2: 安装 Docker**](./tasks/task2.md) — 安装并配置 Docker，检查命令可用、服务运行、用户权限、容器执行
- [**Task 3: 基础 Linux 操作**](./tasks/task3.md) — 在 Docker 容器内做文件操作 + 15 道选择题
- [**Task 4: Makefile**](./tasks/task4.md) — 为 AI 扣图工具编写 Makefile，自动化构建、处理、打包、清理

## 故障排查

- **`npx autograding grade` 报错找不到命令**：确认已运行 `npm install`，且 Node.js 版本 ≥ 20。
- **GitHub Actions 显示"报告单解密失败"**：确认 `autograding_report.json` 是运行 `npx autograding grade` 后生成的原始文件，未经手动编辑。
- **Task 4 得 0 分**：确认你在仓库根做了 `cp starter_makefile Makefile` 并完成了所有 TODO，且 Docker Desktop 正在运行。
- **Task 3 选择题卡住**：`npx autograding grade` 会逐题在终端提问，直接键入选项字母（A/B/C/D）并回车即可。
- **报告归属不对**：评测报告会读取 `git config user.name`。可运行 `git config --global user.name "你的名字"` 设置。
- **Docker 命令需要 sudo**：Task 2 要求不使用 sudo 运行 Docker。通常需要将当前用户加入 docker 组并重启终端。

## 端到端烟测

改动模板仓后，可以用引导脚本重跑一遍完整的候选人烟测，验证从 "Use this template" 到在 Actions 看到得分面板的全链路：

```bash
./scripts/smoke-test.sh
```

脚本会逐步引导你完成：创建派生仓 → clone → npm install → 完成 Makefile → 运行 grade → 提交并推送报告单 → 在 Actions 查看 Job Summary。