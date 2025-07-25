# 📦 Sync Release from Other Repo

本项目包含一个 GitHub Actions 工作流，用于**从其他仓库同步指定 Release 到当前仓库**，包括 Release 的元信息和所有附件资源。

> ✅ 支持手动触发，自动判断是否需要创建 Release，自动上传资源。

---

## 🚀 功能简介

该工作流可完成以下操作：

1. 从指定仓库获取目标 Release 信息（标题、内容、是否为草稿/预发布等）；
2. 若当前仓库不存在该标签对应的 Release，则自动创建；
3. 下载目标 Release 中的全部附件；
4. 上传附件至当前仓库的 Release，并附带来源说明。

---

## ⚙️ 使用方式

### 手动触发

在 GitHub Actions 页面点击 **“Run workflow”**，填写以下参数：

| 参数名 | 类型 | 是否必填 | 描述 |
|--------|------|----------|------|
| `repo` | `string` | ✅ 是 | 要同步的目标仓库名（格式如 `owner/repo`） |
| `release_tag` | `string` | ✅ 是 | 要同步的 Release 标签（如 `v1.2.3`） |

---

## 🧱 工作流逻辑

### 🧩 环境准备

- 安装 `jq`：用于解析 JSON；
- 安装 `gh`：GitHub CLI 工具，用于管理 Release。

### 🔍 获取源 Release 信息

使用 GitHub API 获取指定仓库与标签的 Release 元数据：

- 标题：`name` 或 `tag_name`；
- 描述：`body`；
- 是否草稿/预发布：`draft` / `prerelease`；
- 追加来源追溯信息至描述底部。

### 📝 创建 Release（如未存在）

判断当前仓库是否已存在该 `release_tag` 对应的 Release：

- 若不存在，则创建并附加标题与描述；
- 若已存在，则跳过创建。

### ⬇️ 下载资源 → ⬆️ 上传资源

- 获取源 Release 的附件下载链接；
- 依次下载每个资源；
- 上传至当前仓库对应的 Release（自动覆盖已有资源）；
- 上传后自动清理临时文件。

---

## 🧾 示例

同步 `ROCKNIX/distribution` 仓库的 `20250517` Release：

```text
repo: ROCKNIX/distribution
release_tag: 20250517
```

同步成功后，你将看到当前仓库中生成了一个名为 `20250517` 的 Release，包含原始说明和附件，并附带如下来源说明：

```
---
本 Release 由自动同步脚本生成，来源：[ROCKNIX/distribution@20250517](https://github.com/ROCKNIX/distribution/releases/tag/20250517)
```

---

## 🔐 权限说明

该工作流需使用默认的 `GITHUB_TOKEN` 来执行以下操作：

- 读取 Release 数据；
- 创建 Release；
- 上传 Release 资产。

请确保 `permissions` 已包含：

```yaml
permissions:
  contents: write
```

---

## 📁 文件结构

```
.github/
└── workflows/
    └── sync-release.yml    # 工作流文件
README.md                  # 本说明文档
```

---

## 🧩 后续可扩展功能（建议）

- 定时同步某仓库最新 Release；
- 同步多个标签；
- 同步 tag/commit 本体（非仅 release）；
- 支持删除旧版本。

---

## 📮 联系方式

如有任何问题或需求，欢迎提 Issue 或 PR 反馈与改进建议。
