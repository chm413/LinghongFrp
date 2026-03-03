# LinghongFrp

<div align="center">

![LinghongFrp Logo](https://via.placeholder.com/200x200?text=LinghongFrp)

**统一管理多个FRP服务提供商的跨平台图形客户端**

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Go Version](https://img.shields.io/badge/go-%3E%3D1.21-blue.svg)](https://golang.org/)
[![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20Linux%20%7C%20macOS-lightgrey.svg)](https://github.com/chm413/LinghongFrp)
[![Status](https://img.shields.io/badge/status-In%20Development-yellow.svg)](https://github.com/chm413/LinghongFrp)

[English](README_EN.md) | 简体中文

</div>

## 📖 项目简介

LinghongFrp 是一个基于 Go 语言开发的跨平台内网穿透管理工具，旨在统一管理多个国内 FRP 服务提供商的隧道服务。通过现代化的图形界面和强大的功能，让内网穿透管理变得简单高效。

### ✨ 核心特性

- 🌐 **多平台支持** - 统一管理 OpenFRP、乐青映射、MeFRP、ChmlFRP、StellarFRP、LoCyanFRP、MossFRP、樱花FRP、Natapp 等主流平台
- 🖥️ **跨平台GUI** - 支持 Windows、Linux、macOS 的原生图形界面，完美支持中文显示
- 🔐 **安全加固** - AES-256-GCM 加密存储、主密码保护、IP 白名单、访问控制
- 🚀 **智能运维** - 健康检查、异常告警、自动故障转移、多节点负载均衡
- 📊 **流量统计** - 实时流量监控、历史数据分析、可视化图表展示
- 🔧 **系统服务** - 支持作为系统服务运行，开机自启，后台持续工作
- 🌟 **高级功能** - P2P 穿透、SSL 证书管理、网络扫描、配置导入导出

## 🎯 功能亮点

### 统一管理多平台
在一个客户端中管理所有 FRP 服务提供商的隧道，告别多个客户端切换的烦恼。

### 原版 frpc + TOML 配置
使用官方原版 frpc 客户端，通过标准 TOML 配置文件启动隧道，确保最佳兼容性和可靠性。

### 智能节点选择
内置节点延迟测试功能，自动推荐最快最稳定的节点，支持多节点智能切换和负载均衡。

### 安全可靠
- 主密码保护，所有平台凭据加密存储
- 支持 IP 白名单/黑名单、访问控制、速率限制
- 审计日志记录所有敏感操作

### 便捷易用
- 网络扫描自动发现局域网设备，一键创建映射
- 支持导入现有 FRP 配置文件（INI/TOML）
- 隧道分组管理，批量操作

## 🚀 快速开始

### 系统要求

- **操作系统**: Windows 10+, Linux (Ubuntu 20.04+, Debian 11+), macOS 11+
- **架构**: x86_64 (amd64), ARM64
- **内存**: 至少 100MB 可用内存
- **磁盘**: 至少 50MB 可用空间

### 安装

#### Windows

```powershell
# 下载最新版本
# 运行安装程序
LinghongFrp-Setup.exe
```

#### Linux

```bash
# Debian/Ubuntu
sudo dpkg -i linghongfrp_*.deb

# CentOS/Fedora
sudo rpm -i linghongfrp_*.rpm

# 通用 AppImage
chmod +x LinghongFrp-*.AppImage
./LinghongFrp-*.AppImage
```

#### macOS

```bash
# 使用 Homebrew
brew install linghongfrp

# 或下载 DMG 安装包
open LinghongFrp-*.dmg
```

### 首次使用

1. **启动应用** - 首次启动会要求设置主密码
2. **添加平台** - 在"平台配置"中添加你的 FRP 服务提供商
3. **创建隧道** - 在"隧道管理"中创建你的第一个隧道
4. **启动隧道** - 点击启动按钮，开始使用内网穿透服务

## 📚 文档

- [需求文档](.kiro/specs/universal-frp-client/requirements.md) - 完整的功能需求和验收标准
- [设计文档](.kiro/specs/universal-frp-client/design.md) - 系统架构和技术设计
- [任务列表](.kiro/specs/universal-frp-client/tasks.md) - 开发任务和进度跟踪
- [安全文档](.kiro/specs/universal-frp-client/SECURITY.md) - 安全策略和最佳实践
- [变更日志](.kiro/specs/universal-frp-client/CHANGELOG.md) - 版本更新记录

## 🛠️ 技术栈

- **后端**: Go 1.21+
- **前端**: Vue 3 + TypeScript + Vite
- **GUI框架**: Wails v2
- **UI组件**: Element Plus
- **图表**: ECharts
- **数据库**: SQLite (嵌入式)
- **加密**: AES-256-GCM, PBKDF2

## 🏗️ 架构设计

```
┌─────────────────────────────────────────────────────────┐
│                    图形界面层 (Wails v2)                │
├─────────────────────────────────────────────────────────┤
│                    核心业务层                           │
│  隧道控制器 | 配置管理器 | 认证管理器 | 事件总线      │
├─────────────────────────────────────────────────────────┤
│                    平台适配层                           │
│  OpenFRP | 乐青 | MeFRP | ChmlFRP | Stellar | ...     │
├─────────────────────────────────────────────────────────┤
│                    扩展功能层                           │
│  健康检查 | 通知 | 流量统计 | 证书 | 安全网关 | ...   │
├─────────────────────────────────────────────────────────┤
│                    系统服务层                           │
│  Systemd (Linux) | Windows Service | LaunchAgent (macOS)│
├─────────────────────────────────────────────────────────┤
│                    数据存储层                           │
│  配置存储 | 隧道存储 | 认证存储 | 统计存储 | 日志     │
└─────────────────────────────────────────────────────────┘
```

## 🤝 贡献指南

我们欢迎所有形式的贡献！无论是报告 bug、提出新功能建议，还是提交代码改进。

### 如何贡献

1. Fork 本仓库
2. 创建你的特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交你的更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启一个 Pull Request

### 开发环境设置

```bash
# 克隆仓库
git clone https://github.com/chm413/LinghongFrp.git
cd LinghongFrp

# 安装依赖
go mod download

# 安装前端依赖
cd frontend
npm install

# 运行开发服务器
cd ..
wails dev
```

## 📋 路线图

- [x] 需求分析和设计文档
- [ ] 核心框架搭建
- [ ] 平台适配器实现
- [ ] GUI 界面开发
- [ ] 系统服务集成
- [ ] 扩展功能开发
- [ ] 测试和优化
- [ ] 首个稳定版本发布

查看详细的开发进度：[任务列表](.kiro/specs/universal-frp-client/tasks.md)

## 📄 许可证

本项目采用 MIT 许可证 - 查看 [LICENSE](LICENSE) 文件了解详情。

## 🙏 致谢

- [fatedier/frp](https://github.com/fatedier/frp) - 优秀的内网穿透工具
- [wailsapp/wails](https://github.com/wailsapp/wails) - 跨平台 GUI 框架
- 所有 FRP 服务提供商的支持

## 📞 联系方式

- **项目主页**: https://github.com/chm413/LinghongFrp
- **问题反馈**: https://github.com/chm413/LinghongFrp/issues
- **讨论区**: https://github.com/chm413/LinghongFrp/discussions

## ⭐ Star History

如果这个项目对你有帮助，请给我们一个 Star ⭐

[![Star History Chart](https://api.star-history.com/svg?repos=chm413/LinghongFrp&type=Date)](https://star-history.com/#chm413/LinghongFrp&Date)

---

<div align="center">

**[官网](https://linghongfrp.com)** • **[文档](https://docs.linghongfrp.com)** • **[社区](https://community.linghongfrp.com)**

Made with ❤️ by LinghongFrp Team

</div>
