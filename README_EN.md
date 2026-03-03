# LinghongFrp

<div align="center">

![LinghongFrp Logo](https://via.placeholder.com/200x200?text=LinghongFrp)

**Cross-platform GUI Client for Unified FRP Service Management**

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Go Version](https://img.shields.io/badge/go-%3E%3D1.21-blue.svg)](https://golang.org/)
[![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20Linux%20%7C%20macOS-lightgrey.svg)](https://github.com/chm413/LinghongFrp)
[![Status](https://img.shields.io/badge/status-In%20Development-yellow.svg)](https://github.com/chm413/LinghongFrp)

English | [简体中文](README.md)

</div>

## 📖 Introduction

LinghongFrp is a cross-platform intranet penetration management tool developed in Go, designed to unify the management of tunnel services from multiple Chinese FRP service providers. With a modern graphical interface and powerful features, it makes intranet penetration management simple and efficient.

### ✨ Core Features

- 🌐 **Multi-Platform Support** - Unified management of OpenFRP, LeQing, MeFRP, ChmlFRP, StellarFRP, LoCyanFRP, MossFRP, SakuraFRP, Natapp, and more
- 🖥️ **Cross-Platform GUI** - Native graphical interface for Windows, Linux, and macOS with perfect Chinese character support
- 🔐 **Security Hardening** - AES-256-GCM encrypted storage, master password protection, IP whitelist, access control
- 🚀 **Smart Operations** - Health checks, anomaly alerts, automatic failover, multi-node load balancing
- 📊 **Traffic Statistics** - Real-time traffic monitoring, historical data analysis, visual chart display
- 🔧 **System Service** - Run as a system service with auto-start on boot and continuous background operation
- 🌟 **Advanced Features** - P2P penetration, SSL certificate management, network scanning, configuration import/export

## 🎯 Highlights

### Unified Multi-Platform Management
Manage all FRP service provider tunnels in one client, eliminating the hassle of switching between multiple clients.

### Native frpc + TOML Configuration
Uses official native frpc client with standard TOML configuration files to start tunnels, ensuring optimal compatibility and reliability.

### Smart Node Selection
Built-in node latency testing, automatically recommends the fastest and most stable nodes, supports multi-node intelligent switching and load balancing.

### Secure and Reliable
- Master password protection with encrypted storage of all platform credentials
- Supports IP whitelist/blacklist, access control, rate limiting
- Audit logs record all sensitive operations

### Convenient and Easy to Use
- Network scanning automatically discovers LAN devices with one-click mapping
- Supports importing existing FRP configuration files (INI/TOML)
- Tunnel grouping management with batch operations

## 🚀 Quick Start

### System Requirements

- **Operating System**: Windows 10+, Linux (Ubuntu 20.04+, Debian 11+), macOS 11+
- **Architecture**: x86_64 (amd64), ARM64
- **Memory**: At least 100MB available memory
- **Disk**: At least 50MB available space

### Installation

#### Windows

```powershell
# Download the latest version
# Run the installer
LinghongFrp-Setup.exe
```

#### Linux

```bash
# Debian/Ubuntu
sudo dpkg -i linghongfrp_*.deb

# CentOS/Fedora
sudo rpm -i linghongfrp_*.rpm

# Universal AppImage
chmod +x LinghongFrp-*.AppImage
./LinghongFrp-*.AppImage
```

#### macOS

```bash
# Using Homebrew
brew install linghongfrp

# Or download DMG installer
open LinghongFrp-*.dmg
```

### First Use

1. **Launch Application** - First launch will prompt you to set a master password
2. **Add Platform** - Add your FRP service provider in "Platform Configuration"
3. **Create Tunnel** - Create your first tunnel in "Tunnel Management"
4. **Start Tunnel** - Click the start button to begin using the intranet penetration service

## 📚 Documentation

- [Requirements Document](.kiro/specs/universal-frp-client/requirements.md) - Complete functional requirements and acceptance criteria
- [Design Document](.kiro/specs/universal-frp-client/design.md) - System architecture and technical design
- [Task List](.kiro/specs/universal-frp-client/tasks.md) - Development tasks and progress tracking
- [Security Document](.kiro/specs/universal-frp-client/SECURITY.md) - Security policies and best practices
- [Changelog](.kiro/specs/universal-frp-client/CHANGELOG.md) - Version update records

## 🛠️ Tech Stack

- **Backend**: Go 1.21+
- **Frontend**: Vue 3 + TypeScript + Vite
- **GUI Framework**: Wails v2
- **UI Components**: Element Plus
- **Charts**: ECharts
- **Database**: SQLite (embedded)
- **Encryption**: AES-256-GCM, PBKDF2

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────┐
│                  UI Layer (Wails v2)                    │
├─────────────────────────────────────────────────────────┤
│                  Core Business Layer                    │
│  Tunnel Controller | Config Manager | Auth Manager     │
├─────────────────────────────────────────────────────────┤
│                  Platform Adapter Layer                 │
│  OpenFRP | LeQing | MeFRP | ChmlFRP | Stellar | ...   │
├─────────────────────────────────────────────────────────┤
│                  Extended Features Layer                │
│  Health Check | Notification | Traffic Stats | Cert    │
├─────────────────────────────────────────────────────────┤
│                  System Service Layer                   │
│  Systemd (Linux) | Windows Service | LaunchAgent (macOS)│
├─────────────────────────────────────────────────────────┤
│                  Data Storage Layer                     │
│  Config | Tunnel | Auth | Stats | Logs                │
└─────────────────────────────────────────────────────────┘
```

## 🤝 Contributing

We welcome all forms of contributions! Whether it's reporting bugs, suggesting new features, or submitting code improvements.

### How to Contribute

1. Fork this repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

### Development Environment Setup

```bash
# Clone repository
git clone https://github.com/chm413/LinghongFrp.git
cd LinghongFrp

# Install dependencies
go mod download

# Install frontend dependencies
cd frontend
npm install

# Run development server
cd ..
wails dev
```

## 📋 Roadmap

- [x] Requirements analysis and design documentation
- [ ] Core framework setup
- [ ] Platform adapter implementation
- [ ] GUI interface development
- [ ] System service integration
- [ ] Extended features development
- [ ] Testing and optimization
- [ ] First stable release

View detailed development progress: [Task List](.kiro/specs/universal-frp-client/tasks.md)

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- [fatedier/frp](https://github.com/fatedier/frp) - Excellent intranet penetration tool
- [wailsapp/wails](https://github.com/wailsapp/wails) - Cross-platform GUI framework
- Support from all FRP service providers

## 📞 Contact

- **Project Homepage**: https://github.com/chm413/LinghongFrp
- **Issue Tracker**: https://github.com/chm413/LinghongFrp/issues
- **Discussions**: https://github.com/chm413/LinghongFrp/discussions

## ⭐ Star History

If this project helps you, please give us a Star ⭐

[![Star History Chart](https://api.star-history.com/svg?repos=chm413/LinghongFrp&type=Date)](https://star-history.com/#chm413/LinghongFrp&Date)

---

<div align="center">

**[Website](https://linghongfrp.com)** • **[Documentation](https://docs.linghongfrp.com)** • **[Community](https://community.linghongfrp.com)**

Made with ❤️ by LinghongFrp Team

</div>
