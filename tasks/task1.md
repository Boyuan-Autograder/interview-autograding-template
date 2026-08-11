# Task 1: 配置运行环境

对于学习计算机科学的学生来说, Linux 是必修技能, 之后我们的评测都会在 Linux 环境下进行. 所以配置一个趁手的 Linux 环境, 不仅能顺利通过我们的测试, 也为之后在计算机领域深入学习打下一个基础.

> 如果你现在使用的是 **macOS** 或者**原生运行 Linux**, 你可以选择跳过使用 WSL2, 直接开始完成任务2与任务3.

大多数同学使用 Windows, 好在 **WSL2 (Windows Subsystem for Linux)** 可以轻松提供 Linux 环境, 无需在硬盘上单独安装系统. 安装 WSL2 的教程, 可以参阅微软官方链接 [_How to install Linux on Windows with WSL_](https://learn.microsoft.com/en-us/windows/wsl/install). 在这里, 我们不给出详细的安装过程, 你可以在网上搜寻到很多关于 WSL 的安装教程, 甚至可以使用 AI 辅助你安装, 我们相信这同样也是锻炼你解决问题能力的机会.

我们推荐你安装的[发行版](https://zh.wikipedia.org/zh-cn/Linux%E5%8F%91%E8%A1%8C%E7%89%88)是 **Ubuntu**, 这是因为后继的 README 当中有相当一部分都是以 **Debian/Ubuntu** 作为参考, 使用我们推荐的环境可以省去很多麻烦. 当然, **我们也鼓励你尝试使用不同的发行版**.

## 任务

1. 检测你的运行环境 (**30pts**):
    - 如果你运行的是**原生 Linux / macOS**, 你将直接获得 30pts.

    - 如果你运行的是 **WSL**:
        - **WSL2**: 获得 30pts.
        - **WSL1**: 获得 15pts.

2. 检测是否安装了 **Git** 且版本 ≥ 2.30 (**15pts**)
3. 检测是否安装了 **Python3** 且版本 ≥ 3.10 (**15pts**)
4. 检测是否安装了 **Node.js** 且版本 ≥ 20 (**20pts**):

    > Node.js® 是一个免费、开源、跨平台的 JavaScript 运行时环境，它让开发人员能够创建服务器、 Web 应用、命令行工具和脚本。—https://nodejs.org/
    - 推荐遵循 `https://nodejs.org/zh-cn/download` 的指引安装。
    - 推荐选择 "Get Node.js® _v24.19.0LTS_ for _Linux_ using _fnm_ with _npm_"
    - Node.js 是运行评测工具（`npx autograding grade`）本身的运行时，必须 ≥ 20 才能顺利评测。

5. 检测是否配置了 **Git SSH 密钥** (**20pts**, 需同时满足以下三项):
    - `~/.ssh/` 下存在公钥文件（`id_*.pub`）。
    - `ssh-add -l` 已加载密钥（输出不是 "no identities"）。
    - `ssh-keygen -l -f <公钥文件>` 能成功解析指纹。

## 版本检查与 SSH 说明

- 版本检查的语义是"**已装且版本达标** 才给分"：`git --version`、`python3 --version`、`node --version` 解析出的版本号满足最低要求才算通过, 不拆分"装了但版本旧"的部分分。
- SSH 密钥配置是本地的富检查, 不连接 GitHub, 主要用于考察你配置 Git SSH 的本地能力。若你的 `~/.ssh` 下还没有密钥, 请按下方指南生成并添加。

## 提示

我们假设你已经完成了运行环境的安装. 如果你对于完成这一步感到困难, 可以尽你所能寻求解决方案, 包括但不限于b站甚至是 AI 工具.

接下来的操作可能需要**特定的网络环境**. 如果你正在使用 WSL2 与 Clash, 推荐将 WSL2 的网络模式改为 **mirrored（镜像）模式**, 这样 Windows 上的代理在 WSL2 中也能生效, 无需开启 TUN 模式. 如果你感觉到下载很慢, 那么这一步可能是必要的.

启用 mirrored 模式的方法:

首推使用 WSL Settings (随WSL一同安装) 图形化界面进行配置。

然后在终端运行 `wsl --shutdown` 并重新打开 WSL, 使配置生效. mirrored 模式下 Windows 与 WSL2 共享回环地址, 因此 Clash 默认监听的 `127.0.0.1` 端口（如 `7890`）可以直接在 WSL2 中访问, 无需开启 TUN 模式.

> mirrored 网络模式需要 **Windows 11 22H2 及以上** 与 **WSL 2.0 及以上**. 如果你的系统版本较旧, 无法使用该模式, 再退回使用 TUN 模式.

对于不同的操作系统, 安装`Git`和`Python3`的方法不一样, 我们将以`Ubuntu`与`macOS`为例.

### Ubuntu

首先很有必要介绍 **[APT](<https://en.wikipedia.org/wiki/APT_(software)>)** , 这类似于你手机上的"应用商店".

> Advanced Package Tool (APT) is a free-software user interface that works with core libraries to handle the installation and removal of software on Debian and Debian-based Linux distributions. APT simplifies the process of managing software on Unix-like computer systems by automating the retrieval, configuration and installation of software packages, either from precompiled files or by compiling source code.
> 高级打包工具（英语：Advanced Packaging Tools，缩写为APT）是Debian及其派生的Linux软件包管理器。APT可以自动下载，配置，安装二进制或者源代码格式的软件包，因此简化了Unix系统上管理软件的过程。APT最早被设计成dpkg的前端，用来处理deb格式的软件包。现在经过APT-RPM组织修改，APT已经可以安装在支持RPM的系统管理RPM包。
> –wikipedia

你可以输入如下的命令并按下回车:

```bash
sudo apt update
```

并且输入你在安装过程中设置的密码. 请注意, **你的密码并不会显示出来**. `update`会做如下的事情:

1. 访问 `/etc/apt/sources.list` 和 `/etc/apt/sources.list.d/` 中定义的软件源。

2. 下载最新的**软件包列表** (包括版本号、依赖信息等).

3. 更新本地缓存, 让系统知道哪些软件包有更新.

> 你可以自己尝试着更换软件源(比如清华镜像源).

之后, 你的软件包列表会更新到最新. 这个时候键入:

```bash
sudo apt upgrade
```

这个时候会提示你有哪些软件可以被更新或者修改, 你可以一路回车.

当你想要安装某个软件包, 你可以输入:

```bash
sudo apt install <软件包名>
```

软件包名通常是小写, 这也提醒了你如何安装`Git`和`Python3`.

你也可以键入:

```bash
apt
```

会列出`apt`的用法, 你可以自己尝试使用不同的命令. 但是请记住, 在绝大多数情况下, 你需要在`apt`前面加上`sudo`. 在这里, 我们暂时不对`sudo`进行解释, 我们会在`task3`详细展开 Linux 的基本命令.

### macOS

我们强烈推荐你使用 **[Homebrew](https://brew.sh/)**.

> Homebrew is a free and open-source software package management system that simplifies the installation of software on Apple's operating system, macOS, as well as Linux.

使用 Homebrew 的方法和 APT 类似, 但是你需要手动安装这个软件. 你可以在 macOS 的终端输入如下指令来安装:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

brew --version
```

若展示了版本号, 即说明安装成功.

> 如果出现网络问题, 请确保你已经科学上网.

```bash
brew install <软件包名>
```

软件包名通常是小写, 这也提醒了你如何安装`Git`和`Python3`.

### 配置 Git SSH 密钥

非常建议自行学习Github文档 [使用 SSH 连接到GitHub](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh) 来配置ssh密钥。

SSH 密钥的设计目的, 是解决身份验证的问题——如何向服务器证明"你是你". 传统的密码认证依赖"你知道什么": 密码本身可能被窃取, 也有在传输过程中被截获的风险. 而 SSH 改用一对非对称密钥: 公钥可以公开, 交给 GitHub 保存; 私钥只留在你自己的电脑上, 绝不外传. 连接时, GitHub 通过密码学签名验证你是否真的持有与公钥配对的私钥——**持有私钥本身就是身份的证明**, 且私钥全程不出本地、不经过网络. 这正是本任务要求生成密钥对、并把公钥提供给 GitHub 的原因: 之后无论是 `git clone` 还是 `git push`, 你都是在用私钥向 GitHub 证明自己的身份.

本任务只检查以上本地能力 (公钥文件存在 + agent 已加载 + 指纹可解析), 不连接 GitHub。

## 评测

在仓库根目录运行评测命令，五个 task 会一起执行，可以只关注 task1 的结果：

```bash
# 如果未执行过 npm install 请先执行
npm install

# 在仓库根目录（clone 下来的 interview-autograding-template 目录）
npx autograding grade
```

运行结束后，仓库根会生成一份加密报告单 `autograding_report.json`，命令行会显示你的总分和各项得分。请 commit 并 push 这份报告单，push 后 GitHub Actions 会自动解密并在运行记录里展示你的得分面板。
