# 🔥 NextCyber VulnHub

> [中文文档](README.md)

<div align="center">

**Making Cybersecurity Learning Easy!**

[![CVE Docs](https://img.shields.io/badge/CVE%20Docs-Continuously%20Updated-brightgreen?style=for-the-badge)](https://github.com/NextCyber/nextcyber-vulnhub)
[![Beginner Friendly](https://img.shields.io/badge/Beginner-Friendly-blue?style=for-the-badge)](#)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)](LICENSE)
[![Platform](https://img.shields.io/badge/Platform-NextCyber-red?style=for-the-badge)](https://app.nextcyber.cn)

</div>

---

## 📖 About This Project

**NextCyber VulnHub** is a curated collection of CVE vulnerability reproduction documents, maintained by the [NextCyber Platform](https://www.nextcyber.cn) team.

> 🎯 **Designed for beginners** — every document is written to be clear, well-structured, and hands-on.

Each reproduction document includes:

- ✅ Vulnerability background and impact scope
- ✅ Step-by-step lab environment setup
- ✅ Complete reproduction walkthrough (screenshots + commands)
- ✅ Remediation advice and defensive measures
- ✅ Companion hands-on lab available on the NextCyber Platform

---

## 📚 Vulnerability Index

### 🔴 RCE (Remote Code Execution)

| CVE ID | Component | Summary | Difficulty | Docs |
|--------|-----------|---------|------------|------|
| CVE-2025-55182 | React Server Components | React Server Components deserialization RCE | ⭐⭐⭐ | [View](./react/) |
| CVE-2015-5254 | Apache ActiveMQ | JMS `ObjectMessage` deserialization RCE | ⭐⭐ | [View](./activemq/CVE-2015-5254/) |
| CVE-2016-3088 | Apache ActiveMQ | `fileserver` arbitrary file write → RCE | ⭐⭐ | [View](./activemq/CVE-2016-3088/) |
| CVE-2022-41678 | Apache ActiveMQ | Jolokia MBean arbitrary file write → RCE | ⭐⭐⭐ | [View](./activemq/CVE-2022-41678/) |
| CVE-2023-46604 | Apache ActiveMQ | OpenWire protocol deserialization RCE (Critical) | ⭐⭐ | [View](./activemq/CVE-2023-46604/) |

### 🟠 SQL Injection *(coming soon)*

> Stay tuned...

### 🟡 XSS *(coming soon)*

> Stay tuned...

### 🔵 File Upload Vulnerabilities *(coming soon)*

> Stay tuned...

---

## 🗂️ Repository Structure

```
nextcyber-vulnhub/
├── README_CN.md           # Chinese introduction
├── README_EN.md           # English introduction
├── react/                 # React vulnerabilities
│   └── CVE-2025-55182/
│       ├── README_CN.md   # Chinese reproduction doc
│       ├── README_EN.md   # English reproduction doc
│       └── poc/           # PoC code (for educational use only)
├── activemq/              # Apache ActiveMQ vulnerabilities
│   ├── CVE-2015-5254/
│   │   ├── README_CN.md   # Deserialization RCE
│   │   └── README_EN.md
│   ├── CVE-2016-3088/
│   │   ├── README_CN.md   # Arbitrary file write RCE
│   │   └── README_EN.md
│   ├── CVE-2022-41678/
│   │   ├── README_CN.md   # Jolokia MBean RCE
│   │   └── README_EN.md
│   └── CVE-2023-46604/
│       ├── README_CN.md   # OpenWire deserialization RCE
│       └── README_EN.md
├── nginx/                 # Nginx series (coming soon)
├── wordpress/             # WordPress series (coming soon)
└── CONTRIBUTING.md        # Contribution guide
```

---

## 🚀 Getting Started

### Option 1: Use the NextCyber Lab Platform *(Recommended for beginners)*

1. Register at [app.nextcyber.cn](https://app.nextcyber.cn)
2. Select a CVE course
3. Launch the lab environment with one click
4. Follow the reproduction docs in this repository step by step

### Option 2: Set Up Locally

```bash
# Clone the repository
git clone https://github.com/NextCyber/nextcyber-vulnhub.git

# Navigate to a specific vulnerability
cd nextcyber-vulnhub/activemq/CVE-2023-46604

# Read README_CN.md or README_EN.md and follow the setup instructions
```

> ⚠️ **Disclaimer**: All content in this repository is strictly for educational and research purposes. Do not use it for any illegal activities.

---

## 🤝 Contributing

We warmly welcome community contributions! You can:

- 📝 Submit new CVE reproduction documents
- 🐛 Fix errors in existing documents
- 💡 Suggest improvements via Issues

**How to contribute:**

1. Fork this repository
2. Create a new branch: `git checkout -b feat/CVE-XXXX-XXXXX`
3. Write your reproduction document following the template below
4. Submit a Pull Request

See [CONTRIBUTING.md](./CONTRIBUTING.md) for details.

---

## 📝 Document Template

Each reproduction document should follow this structure:

```markdown
# CVE-XXXX-XXXXX: Vulnerability Name

## Overview

## Affected Versions

## Environment Setup

## Reproduction Steps

## Vulnerability Analysis

## Remediation
```

---

## 🌐 Resources

- 🏠 [NextCyber Website](https://www.nextcyber.cn)
- 🎮 [Online Lab Platform](https://app.nextcyber.cn)
- 📬 Contact us: info@nextcyber.ai

---

<div align="center">
  <sub>⭐ If you find this useful, please give it a Star to help others discover this project!</sub><br/>
  <sub>Made with ❤️ by <a href="https://github.com/NextCyber">NextCyber Team</a></sub>
</div>
