# ADR-0001: 模型文件 u2netp.onnx 直接 commit，不使用 Git LFS

**Date:** 2026-08-10
**Status:** Accepted

## Context

task4 的工作区包含 `tool/models/u2netp.onnx`（4.4 MB），这是 rembg 的预训练 U2Net 模型权重。Makefile 的 `docker build` 目标会把整个 `tool/` 目录（含此模型文件）COPY 进 Docker 镜像，运行时用该模型对 input_images 做 AI 扣图。

Git LFS 3.7.0 已在本地可用。旧仓 `Boyuan-IT-Club/Interview-Autograding` 未使用 LFS（无 `.gitattributes`），4.4 MB 文件直接在 git 历史中。

## Decision

将 `tool/models/u2netp.onnx` 直接 commit 到模板仓的 git 历史，**不**使用 Git LFS。

## Rationale

LFS 在常规场景下是大文件存储的正确方案——它把二进制对象移出主历史、按需拉取。但在这个仓库的具体约束下，直接 commit 更合适：

1. **候选人零设置是硬约束。** Spec 的 User Story 1、2 要求候选人 "Use this template" → clone → `npm install` → `npx autograding grade` 即可完成评测，不需要安装额外工具。LFS 要求候选人本地预装 `git-lfs`（Windows 默认不含），否则 clone 后 onnx 是 46 字节的指针文件，`docker build` 时 `COPY . .` 拷入的是指针而非真实模型，扣图运行会失败。

2. **"Use this template" 派生路径的 LFS 传递不可靠。** GitHub Template Repository 派生时，LFS 对象的传递存在已知问题——候选人派生仓可能拿到悬空指针而非真实文件，需要额外 `git lfs install && git lfs pull` 才能修复。这对候选人是隐蔽陷阱，违反零设置约束。

3. **与旧仓先例一致。** 旧仓直接 commit 模型文件，未用 LFS。旧仓的模型文件是任务必需的静态资产，不频繁变动，4.4 MB 的 clone 成本可接受。

4. **不消耗 LFS 配额。** GitHub 免费 LFS 带宽 1 GB/月、存储 1 GB。此仓库的模型文件是静态的、单次的，没有变动需要版本化存储，LFS 的增量存储优势无从发挥。

## Consequences

- 仓库 clone 体积多约 4.4 MB（单文件，可接受）。
- 候选人 clone 即得完整工作区，无需任何额外步骤。
- 旧仓先例一致，无认知负担。
- 若未来模型文件频繁更新或体积显著增大（如换更大的模型），可重新评估引入 LFS。
