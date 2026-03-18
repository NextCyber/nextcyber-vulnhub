# 🔥 NextCyber VulnHub

<div align="center">

**让网安学习变简单！Make Cybersecurity Learning Easy!**

[![CVE文档数量](https://img.shields.io/badge/CVE%20文档-持续更新-brightgreen?style=for-the-badge)](https://github.com/NextCyber/nextcyber-vulnhub)
[![零基础友好](https://img.shields.io/badge/零基础-友好-blue?style=for-the-badge)](#)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)](LICENSE)
[![平台](https://img.shields.io/badge/配套平台-NextCyber-red?style=for-the-badge)](https://app.nextcyber.cn)

</div>

---

## 📖 项目简介

**NextCyber VulnHub** 是由 [NextCyber 平台](https://www.nextcyber.cn) 维护的 CVE 漏洞复现文档合集。

> 🎯 **专为零基础学员设计** — 每一篇文档都力求做到：看得懂、学得会、能动手

每篇复现文档包含：
- ✅ 漏洞背景与影响范围
- ✅ 详细的实验环境搭建步骤
- ✅ 完整的漏洞复现过程（截图 + 命令）
- ✅ 修复建议与防御措施
- ✅ 配套靶场链接（在 NextCyber 平台在线练习）

---

## 📚 漏洞索引

### 🔴 RCE（远程代码执行）

| CVE ID | 影响组件 | 漏洞简介 | 难度 | 文档 |
|--------|---------|---------|------|------|
| CVE-2025-55182 | React Server Components | RSC 反序列化导致 RCE | ⭐⭐⭐ | [查看](./react/) |

### 🟠 SQL 注入（持续更新）

> 敬请期待...

### 🟡 XSS（持续更新）

> 敬请期待...

### 🔵 文件上传漏洞（持续更新）

> 敬请期待...

---

## 🗂️ 目录结构

```
nextcyber-vulnhub/
├── README.md               # 本文件（漏洞总索引）
├── react/                  # React 系列漏洞
│   └── CVE-2025-55182/
│       ├── README.md       # 复现文档
│       └── poc/            # PoC 代码（仅供学习）
├── apache/                 # Apache 系列（待更新）
├── nginx/                  # Nginx 系列（待更新）
├── wordpress/              # WordPress 系列（待更新）
└── CONTRIBUTING.md         # 贡献指南
```

---

## 🚀 快速开始

### 方式一：配合 NextCyber 靶场（推荐新手）

1. 前往 [app.nextcyber.cn](https://app.nextcyber.cn) 注册账号
2. 选择对应的 CVE 课程
3. 一键启动靶场环境
4. 对照本仓库文档进行复现练习

### 方式二：本地自行搭建

```bash
# 克隆仓库
git clone https://github.com/NextCyber/nextcyber-vulnhub.git

# 进入对应漏洞目录
cd nextcyber-vulnhub/react/CVE-2025-55182

# 阅读 README.md 按步骤搭建环境
```

> ⚠️ **免责声明**：本仓库所有内容仅供学习研究使用，请勿用于非法用途。

---

## 🤝 如何贡献

我们非常欢迎社区贡献！你可以：

- 📝 提交新的 CVE 复现文档
- 🐛 修复已有文档中的错误
- 💡 提出改进建议（通过 Issues）

**贡献步骤：**

1. Fork 本仓库
2. 创建新分支：`git checkout -b feat/CVE-XXXX-XXXXX`
3. 按照文档模板编写复现文档
4. 提交 Pull Request

详见 [CONTRIBUTING.md](./CONTRIBUTING.md)

---

## 📝 文档模板

每篇复现文档应包含以下结构：

```markdown
# CVE-XXXX-XXXXX：漏洞名称

## 漏洞概述
## 影响版本
## 环境搭建
## 漏洞复现
## 漏洞分析
## 修复建议
## 参考链接
```

---

## 🌐 相关资源

- 🏠 [NextCyber 官网](https://www.nextcyber.cn)
- 🎮 [在线靶场](https://app.nextcyber.cn)
- 📬 联系我们：contact@nextcyber.cn

---

<div align="center">
  <sub>⭐ 觉得有用请点 Star，让更多人发现这个项目！</sub><br/>
  <sub>Made with ❤️ by <a href="https://github.com/NextCyber">NextCyber Team</a></sub>
</div>
