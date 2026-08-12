# Task 4: Makefile

在这个 TASK 中，你将为一个基于 Docker 的 AI 扣图工具编写一套完整的自动化工作流

## 任务目标

你需要完成一个 Makefile，使其能够自动化以下整个流程：

- 构建：自动构建包含 AI 工具的 Docker 镜像
- 处理：批量处理 `input_images/` 目录下的所有图片，将移除背景后的图片存放到 `output_images/` 目录
- 打包：将所有处理完成的图片打包成一个名为 `processed_images.zip` 的压缩文件
- 清理：提供一个命令来清理所有生成的文件（输出目录、压缩包和构建标记）

> Make是一个在软件开发中所使用的构建工具，用于自动化建构软件。 它通过一个名为 Makefile 的文本文件来描述源代码文件之间的依赖关系和构建规则。 Make 会根据这些规则和依赖关系，判断哪些文件需要重新编译，并执行相应的编译命令，以确保最终生成可执行文件或其他目标文件（这些目标被称为“target”）。 大多数情况下，它被用来编译源代码，生成结果代码，然后把结果代码连接起来生成可执行文件或者库文件。–wikipedia

本 task 的工作区集中在 `task4/` 子目录。

## 项目结构

```
├── README.md               # 仓库总览（本任务说明见 tasks/task4.md）
└── task4/                  # Task 4 工作区
    ├── starter_makefile    # 你需要复制为 Makefile 的模板
    ├── input_images/       # 存放待处理的图片
    │   └── ...
    ├── output_images/      # (自动生成) 存放处理结果
    │   └── ...
    ├── processed_images.zip# (自动生成) 最终的打包文件
    └── tool/               # AI 扣图工具源码 (无需修改)
        ├── Dockerfile
        ├── main.py
        ├── models/
        └── requirements.txt
```

## 如何开始

- 进入工作区: 先进入 `task4/` 目录。
- 复制文件: 将 `starter_makefile` 复制为 `Makefile`（保留原文件作参考）。
    ```bash
    cd task4
    cp starter_makefile Makefile
    ```
- 完成 Makefile: 打开 `task4/Makefile` 文件，找到并完成所有标记为 TODO 的部分 (`grep -rn "TODO"`)
    - 你需要编写规则来自动化构建、处理、打包和清理的流程。
- 运行评测: 在仓库根目录运行 `npx autograding grade`。工具会在 `task4/` 子目录定位到你的 `Makefile` 和 `input_images/`，用 `make -C task4` 调用并检查其功能是否符合要求。

### Docker 容器中的路径

运行图片处理工具时，需要将整个 `task4/` 工作区挂载到容器的 `/app`。该挂载会遮住镜像构建阶段复制到 `/app` 的文件，因此需覆盖 Dockerfile 的默认入口，并使用挂载后的路径：

- Python 入口：`python /app/tool/main.py`
- 模型目录：`U2NET_HOME=/app/tool/models`
- 输入和输出：`/app/input_images/...` 与 `/app/output_images/...`

打包时，压缩包根目录应只包含生成的 PNG 文件，不应包含 `output_images/` 目录项。可以查看 `zip` 的 `-j` 选项。

## 评分规则

评测工具会检查以下几点：

- make 或 make all 能否成功生成 processed_images.zip。
- processed_images.zip 中是否包含了所有输入图片对应的输出图片。
- make clean 能否成功清理所有生成的文件。

## 值得一用的材料

- GNU Make Manual: https://www.gnu.org/software/make/manual/
    - `info make`
- Make manpage: `man make`
- 大语言模型们
