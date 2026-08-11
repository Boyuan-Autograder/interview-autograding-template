# Task 4: Makefile

在这个 TASK 中，你将为一个基于 Docker 的 AI 扣图工具编写一套完整的自动化工作流

## 任务目标
你需要完成一个 Makefile，使其能够自动化以下整个流程：

- 构建：自动构建包含 AI 工具的 Docker 镜像
- 处理：批量处理 `input_images/` 目录下的所有图片，将移除背景后的图片存放到 `output_images/` 目录
- 打包：将所有处理完成的图片打包成一个名为 `processed_images.zip` 的压缩文件
- 清理：提供一个命令来清理所有生成的文件（输出目录、压缩包和构建标记）

本 task 的工作区集中在 `task4/` 子目录，分门别类。

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
