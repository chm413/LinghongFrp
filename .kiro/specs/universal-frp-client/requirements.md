# Requirements Document

## Introduction

LinghongFrp是一个跨平台的内网穿透管理工具，旨在统一管理多个国内FRP服务提供商的隧道服务。该系统将提供图形化界面，支持多平台服务集成，并通过系统服务方式运行后台管理程序。

## Glossary

- **LinghongFrp**: LinghongFrp系统（统一FRP管理客户端）
- **Platform_Provider**: FRP服务提供商（如乐青映射、OpenFRP、MeFRP等）
- **Tunnel_Manager**: 隧道管理模块
- **Service_Manager**: 系统服务管理模块
- **API_Adapter**: 平台API适配器
- **Authentication_Token**: 各平台的认证令牌
- **Tunnel_Configuration**: 隧道配置信息
- **Platform_Tag**: 平台标识标签

## Requirements

### Requirement 1: 多平台服务提供商支持

**User Story:** 作为用户，我希望能够连接和管理多个FRP服务提供商，以便在一个客户端中统一管理所有内网穿透服务。

#### Acceptance Criteria

1. WHEN 用户添加新的平台提供商 THEN THE LinghongFrp SHALL 支持乐青映射、OpenFRP、MeFRP、ChmlFRP、StellarFRP、MossFRP、LoCyanFRP、樱花FRP、Natapp等主流平台
2. WHEN 平台提供商被添加 THEN THE API_Adapter SHALL 根据不同平台的API文档实现相应的接口适配
3. WHEN 用户配置平台认证信息 THEN THE LinghongFrp SHALL 安全存储各平台的Authentication_Token
4. WHEN 平台API发生变化 THEN THE API_Adapter SHALL 提供可扩展的架构以支持新平台接入

### Requirement 2: 认证和令牌管理

**User Story:** 作为用户，我希望系统能够安全地管理各个平台的认证信息，以便自动完成平台登录和API调用。

#### Acceptance Criteria

1. WHEN 用户输入平台认证信息 THEN THE LinghongFrp SHALL 加密存储Authentication_Token
2. WHEN 系统调用平台API THEN THE API_Adapter SHALL 自动使用对应平台的认证方式
3. WHEN 认证令牌过期 THEN THE LinghongFrp SHALL 提示用户重新认证
4. WHEN 用户删除平台配置 THEN THE LinghongFrp SHALL 安全清除相关的认证信息

### Requirement 3: 系统服务管理

**User Story:** 作为用户，我希望FRP客户端能够作为系统服务运行，以便在系统启动时自动运行并在后台持续工作。

#### Acceptance Criteria

1. WHEN 在Linux系统上安装 THEN THE Service_Manager SHALL 使用systemctl注册和管理服务
2. WHEN 在Windows系统上安装 THEN THE Service_Manager SHALL 使用Windows服务管理器注册和管理服务
3. WHEN 服务启动 THEN THE LinghongFrp SHALL 自动加载所有配置的隧道
4. WHEN 服务停止 THEN THE LinghongFrp SHALL 优雅地关闭所有活动隧道
5. WHEN 系统重启 THEN THE Service_Manager SHALL 确保服务自动启动

### Requirement 4: 统一隧道管理界面

**User Story:** 作为用户，我希望在一个统一的界面中管理所有平台的隧道，以便方便地查看、搜索和管理所有隧道配置。

#### Acceptance Criteria

1. WHEN 用户打开隧道管理页面 THEN THE Tunnel_Manager SHALL 显示所有平台的隧道在统一表格中
2. WHEN 显示隧道信息 THEN THE Tunnel_Manager SHALL 为每个隧道显示Platform_Tag标识其所属平台
3. WHEN 用户搜索隧道 THEN THE Tunnel_Manager SHALL 支持按隧道名称进行搜索
4. WHEN 用户筛选隧道 THEN THE Tunnel_Manager SHALL 支持按Platform_Tag进行筛选
5. WHEN 隧道状态发生变化 THEN THE Tunnel_Manager SHALL 实时更新隧道状态显示

### Requirement 5: 隧道配置和操作

**User Story:** 作为用户，我希望能够创建、编辑、启动和停止隧道，以便灵活管理我的内网穿透需求。

#### Acceptance Criteria

1. WHEN 用户创建新隧道 THEN THE Tunnel_Manager SHALL 支持配置隧道类型（HTTP、HTTPS、TCP、UDP等）
2. WHEN 用户编辑隧道配置 THEN THE LinghongFrp SHALL 验证配置的有效性
3. WHEN 用户启动隧道 THEN THE LinghongFrp SHALL 通过对应平台的API启动隧道服务
4. WHEN 用户停止隧道 THEN THE LinghongFrp SHALL 优雅地停止隧道连接
5. WHEN 隧道配置保存 THEN THE LinghongFrp SHALL 持久化存储Tunnel_Configuration

### Requirement 6: 跨平台图形界面

**User Story:** 作为用户，我希望在不同操作系统上都能使用相同的图形界面，以便获得一致的用户体验。

#### Acceptance Criteria

1. WHEN 应用程序启动 THEN THE LinghongFrp SHALL 在Windows、Linux、macOS上提供一致的图形界面
2. WHEN 用户操作界面 THEN THE LinghongFrp SHALL 提供直观的用户交互体验
3. WHEN 系统运行在后台 THEN THE LinghongFrp SHALL 提供系统托盘图标和快捷操作
4. WHEN 用户查看日志 THEN THE LinghongFrp SHALL 提供实时日志查看功能
5. WHEN 界面显示中文文本 THEN THE LinghongFrp SHALL 正确渲染所有中文字符，不出现乱码或方框
6. WHEN 使用GUI框架 THEN THE LinghongFrp SHALL 使用支持中文字体的跨平台GUI框架（如Fyne v2.4+或Wails）

### Requirement 7: API文档集成和适配

**User Story:** 作为开发者，我希望系统能够根据各平台的API文档实现标准化的接口适配，以便支持不同平台的特定功能。

#### Acceptance Criteria

1. WHEN 集成新平台 THEN THE API_Adapter SHALL 根据平台API文档实现标准接口
2. WHEN 调用平台API THEN THE API_Adapter SHALL 处理不同平台的请求格式和响应格式
3. WHEN API调用失败 THEN THE LinghongFrp SHALL 提供详细的错误信息和重试机制
4. WHEN 平台API更新 THEN THE API_Adapter SHALL 支持版本兼容性管理

### Requirement 8: 配置数据管理

**User Story:** 作为用户，我希望系统能够安全地存储和管理所有配置数据，以便在重启后恢复所有设置。

#### Acceptance Criteria

1. WHEN 用户修改配置 THEN THE LinghongFrp SHALL 实时保存配置更改
2. WHEN 系统启动 THEN THE LinghongFrp SHALL 自动加载所有保存的配置
3. WHEN 配置文件损坏 THEN THE LinghongFrp SHALL 提供配置恢复机制
4. WHEN 用户导出配置 THEN THE LinghongFrp SHALL 支持配置的导入和导出功能


### Requirement 9: P2P (XTCP/STCP) 穿透支持

**User Story:** 作为用户，我希望能够配置 XTCP/STCP 隧道，以便在远程桌面或大文件传输时获得不限速的点对点传输体验。

#### Acceptance Criteria

1. WHEN 用户创建隧道 THEN THE Tunnel_Manager SHALL 支持选择 P2P 模式（XTCP/STCP）
2. WHEN 配置 P2P 隧道 THEN THE LinghongFrp SHALL 提供 NAT 类型检测工具，帮助用户判断是否支持 P2P
3. WHEN 设置 P2P 隧道 THEN THE LinghongFrp SHALL 提供图形化配置访问端（Visitor）和服务器端的界面
4. WHEN P2P 连接建立失败 THEN THE LinghongFrp SHALL 自动回退到中继模式并通知用户
5. WHEN 用户查看 P2P 隧道状态 THEN THE LinghongFrp SHALL 显示连接模式（直连/中继）和实时带宽

### Requirement 10: 自动SSL证书管理

**User Story:** 作为用户，我希望系统能自动为我的 HTTP 隧道申请并配置 SSL 证书，以便安全地暴露内网服务到公网。

#### Acceptance Criteria

1. WHEN 用户启用 SSL 证书管理 THEN THE LinghongFrp SHALL 集成 ACME 协议支持 Let's Encrypt 证书申请
2. WHEN 创建 HTTP 隧道 THEN THE LinghongFrp SHALL 提供"自动申请 SSL 证书"选项
3. WHEN 证书即将过期 THEN THE LinghongFrp SHALL 自动续期证书并重启对应隧道
4. WHEN 证书申请失败 THEN THE LinghongFrp SHALL 记录详细错误日志并通知用户
5. WHEN 用户配置自定义域名 THEN THE LinghongFrp SHALL 验证域名 DNS 解析是否正确

### Requirement 11: 智能健康检查与异常告警

**User Story:** 作为用户，当我的关键隧道掉线或后端服务异常时，我希望通过微信、钉钉或邮件收到通知，以便快速处理问题。

#### Acceptance Criteria

1. WHEN 隧道状态从 "Online" 变为 "Offline" THEN THE LinghongFrp SHALL 触发通知逻辑
2. WHEN 用户配置了 Webhook 地址 THEN THE LinghongFrp SHALL 发送包含隧道名称、错误原因和时间的 JSON 负载
3. WHEN 系统检测到本地后端服务不可用 THEN THE LinghongFrp SHALL 在界面上标记为 "Service Error" 而非单纯的 "Tunnel Offline"
4. WHEN 健康检查失败 THEN THE LinghongFrp SHALL 支持自动重试策略，并在配置了多个节点时尝试切换到备用节点
5. WHEN 用户配置通知渠道 THEN THE LinghongFrp SHALL 支持 Server酱、PushDeer、Telegram Bot、钉钉、企业微信和邮件通知
6. WHEN 隧道恢复正常 THEN THE LinghongFrp SHALL 发送恢复通知

### Requirement 12: 流量统计与可视化看板

**User Story:** 作为用户，我希望看到实时的流量图表和历史统计数据，以便监控穿透服务的运行状况和流量消耗。

#### Acceptance Criteria

1. WHEN 用户打开流量统计页面 THEN THE LinghongFrp SHALL 实时显示每个隧道的上传/下载速度
2. WHEN 隧道运行时 THEN THE LinghongFrp SHALL 记录并展示历史流量数据（日/周/月报表）
3. WHEN 用户查看统计图表 THEN THE LinghongFrp SHALL 提供可视化的流量趋势图和带宽占用图
4. WHEN 流量超过阈值 THEN THE LinghongFrp SHALL 支持配置流量告警并发送通知
5. WHEN 用户导出数据 THEN THE LinghongFrp SHALL 支持导出流量统计数据为 CSV 或 JSON 格式

### Requirement 13: 配置文件热导入与一键迁移

**User Story:** 作为老用户，我希望直接导入现有的 FRP 配置文件（frpc.ini/frpc.toml），而不是手动逐条输入，并能方便地在不同机器间同步配置。

#### Acceptance Criteria

1. WHEN 用户导入配置文件 THEN THE LinghongFrp SHALL 支持解析原生 FRP 配置文件（INI 或 TOML 格式）
2. WHEN 解析配置文件 THEN THE LinghongFrp SHALL 自动识别所属平台或允许用户手动指定
3. WHEN 导入多个配置 THEN THE LinghongFrp SHALL 批量创建隧道配置
4. WHEN 用户导出配置 THEN THE LinghongFrp SHALL 支持将所有配置导出为加密备份文件
5. WHEN 在新机器上恢复 THEN THE LinghongFrp SHALL 支持一键导入加密备份文件并恢复所有配置

### Requirement 14: 本地网络环境扫描与一键映射

**User Story:** 作为新手用户，我希望系统自动扫描我局域网内的设备（如 NAS、路由器、摄像头），并一键创建映射，降低配置门槛。

#### Acceptance Criteria

1. WHEN 用户启动网络扫描 THEN THE LinghongFrp SHALL 通过 ARP 或 mDNS 发现内网服务
2. WHEN 扫描完成 THEN THE LinghongFrp SHALL 显示发现的设备列表，包括 IP、端口和服务类型
3. WHEN 用户选择设备 THEN THE LinghongFrp SHALL 提供针对 RDP、SSH、Web 管理界面、Minecraft 服务的预设配置模板
4. WHEN 用户确认映射 THEN THE LinghongFrp SHALL 自动创建隧道配置并启动服务
5. WHEN 检测到常见服务 THEN THE LinghongFrp SHALL 智能推荐最佳的隧道类型和端口配置

### Requirement 15: 增强的安全防护（安全网关）

**User Story:** 作为用户，我希望给我的隧道增加一层额外的身份验证和访问控制，防止未授权访问和暴力破解。

#### Acceptance Criteria

1. WHEN 用户配置隧道安全策略 THEN THE LinghongFrp SHALL 支持配置 IP 白名单/黑名单
2. WHEN 启用前端认证 THEN THE LinghongFrp SHALL 在 FRP 之上增加 HTTP Basic Auth 代理层
3. WHEN 检测到异常访问 THEN THE LinghongFrp SHALL 记录可疑 IP 并支持自动封禁
4. WHEN 用户启用访问日志 THEN THE LinghongFrp SHALL 记录所有访问请求的来源 IP、时间和请求路径
5. WHEN 配置安全策略 THEN THE LinghongFrp SHALL 支持设置访问频率限制，防止 DDoS 攻击
6. WHEN 用户启用双因素认证 THEN THE LinghongFrp SHALL 支持 TOTP 或短信验证码作为额外认证层

### Requirement 16: 多节点智能切换与负载均衡

**User Story:** 作为用户，我希望系统能够在节点故障或延迟过高时自动切换到最佳节点，以确保服务的高可用性。

#### Acceptance Criteria

1. WHEN 用户配置多个节点 THEN THE LinghongFrp SHALL 支持为同一隧道配置多个备用节点
2. WHEN 检测到节点异常 THEN THE LinghongFrp SHALL 自动切换到延迟最低的可用节点
3. WHEN 系统启动 THEN THE LinghongFrp SHALL 自动测试所有节点的延迟和可用性
4. WHEN 用户查看节点状态 THEN THE LinghongFrp SHALL 显示每个节点的实时延迟、负载和可用性
5. WHEN 启用负载均衡 THEN THE LinghongFrp SHALL 支持将流量分散到多个节点以提高性能

### Requirement 17: 隧道分组与批量操作

**User Story:** 作为管理多个隧道的用户，我希望能够将隧道分组管理，并支持批量启动、停止和配置，以提高管理效率。

#### Acceptance Criteria

1. WHEN 用户创建隧道时 THEN THE LinghongFrp SHALL 支持创建自定义分组并为隧道分配标签
2. WHEN 用户选择多个隧道 THEN THE LinghongFrp SHALL 支持批量启动、停止和删除操作
3. WHEN 用户编辑分组 THEN THE LinghongFrp SHALL 支持批量修改隧道配置（如节点、认证信息）
4. WHEN 用户查看分组 THEN THE LinghongFrp SHALL 支持按分组筛选和折叠显示隧道列表
5. WHEN 用户导出配置 THEN THE LinghongFrp SHALL 支持按分组导出配置文件

### Requirement 18: FRP客户端（frpc）版本管理

**User Story:** 作为用户，我希望系统能够自动下载、更新和管理不同版本的官方原版frpc客户端，并通过标准TOML配置文件启动隧道，以便获得最佳的兼容性和可靠性。

#### Acceptance Criteria

1. WHEN 用户首次启动应用 THEN THE LinghongFrp SHALL 自动检测系统架构并从官方GitHub下载对应的原版frpc二进制文件
2. WHEN 用户查看frpc版本列表 THEN THE LinghongFrp SHALL 显示已安装的frpc版本和可用的官方发布版本
3. WHEN 用户下载frpc版本 THEN THE LinghongFrp SHALL 从官方GitHub Release或镜像源下载对应平台的frpc二进制文件
4. WHEN 下载完成 THEN THE LinghongFrp SHALL 验证文件完整性（SHA256校验）并自动解压
5. WHEN 用户切换frpc版本 THEN THE LinghongFrp SHALL 停止所有使用旧版本的隧道进程，切换到新版本，并提示用户重启隧道
6. WHEN 用户删除frpc版本 THEN THE LinghongFrp SHALL 检查是否有隧道正在使用该版本，如有则阻止删除并提示
7. WHEN 系统检测到新版本 THEN THE LinghongFrp SHALL 在界面上显示更新提示，支持一键更新
8. WHEN 用户配置平台 THEN THE LinghongFrp SHALL 允许为不同平台指定不同的frpc版本
9. WHEN frpc下载失败 THEN THE LinghongFrp SHALL 自动尝试备用下载源（GitHub、ghproxy、镜像站）
10. WHEN 用户查看frpc详情 THEN THE LinghongFrp SHALL 显示版本号、发布日期、文件大小、安装路径和使用该版本的隧道数量
11. WHEN 用户启动隧道 THEN THE LinghongFrp SHALL 为每个隧道生成标准TOML格式的配置文件
12. WHEN 隧道启动 THEN THE LinghongFrp SHALL 使用原版frpc命令行工具（frpc -c config.toml）启动独立进程
13. WHEN 用户修改隧道配置 THEN THE LinghongFrp SHALL 更新对应的TOML配置文件并重启frpc进程

### Requirement 19: FRP客户端源管理与配置导入

**User Story:** 作为高级用户，我希望能够配置自定义的frpc下载源，支持手动导入frpc二进制文件，并能够导入现有的TOML配置文件，以便在网络受限或需要特定版本时使用。

#### Acceptance Criteria

1. WHEN 用户配置下载源 THEN THE LinghongFrp SHALL 支持添加自定义的frpc下载源（GitHub、ghproxy、Gitee、自建服务器等）
2. WHEN 用户手动导入frpc THEN THE LinghongFrp SHALL 支持从本地文件系统导入frpc二进制文件
3. WHEN 导入frpc文件 THEN THE LinghongFrp SHALL 自动检测frpc版本号和架构兼容性
4. WHEN 用户配置镜像源 THEN THE LinghongFrp SHALL 支持配置下载源的优先级和超时设置
5. WHEN 下载源不可用 THEN THE LinghongFrp SHALL 自动切换到下一个可用的下载源
6. WHEN 用户启用代理 THEN THE LinghongFrp SHALL 支持通过HTTP/SOCKS5代理下载frpc文件
7. WHEN 用户查看下载历史 THEN THE LinghongFrp SHALL 记录所有frpc下载和更新的历史记录
8. WHEN 用户导入TOML配置文件 THEN THE LinghongFrp SHALL 解析标准frpc TOML配置并转换为隧道配置
9. WHEN 用户导出隧道配置 THEN THE LinghongFrp SHALL 生成标准TOML格式的配置文件，可直接用于原版frpc
10. WHEN 导入配置文件 THEN THE LinghongFrp SHALL 自动识别服务器地址并尝试匹配对应的平台


### Requirement 20: 登录数据加密存储与安全管理

**User Story:** 作为用户，我希望系统能够安全地加密存储所有平台的登录凭据，并提供主密码保护，以防止敏感信息泄露。

#### Acceptance Criteria

1. WHEN 用户首次启动应用 THEN THE LinghongFrp SHALL 强制用户设置主密码，用于加密所有平台的登录凭据
2. WHEN 用户设置主密码 THEN THE LinghongFrp SHALL 验证密码强度（最少8位，包含大小写字母、数字、特殊字符）
3. WHEN 用户保存平台认证信息 THEN THE LinghongFrp SHALL 使用AES-256-GCM算法加密存储用户名、密码、Token等敏感数据
4. WHEN 系统存储加密数据 THEN THE LinghongFrp SHALL 使用PBKDF2（100,000次迭代）从主密码派生加密密钥
5. WHEN 用户启动隧道 THEN THE LinghongFrp SHALL 从加密存储中读取并解密认证信息，运行时注入到TOML配置中
6. WHEN 生成TOML配置文件 THEN THE LinghongFrp SHALL 不在配置文件中明文保存Token，而是使用占位符并在运行时替换
7. WHEN 用户长时间不活动 THEN THE LinghongFrp SHALL 自动锁定应用，清除内存中的主密钥缓存
8. WHEN 应用锁定 THEN THE LinghongFrp SHALL 要求用户重新输入主密码才能访问敏感功能
9. WHEN 用户修改主密码 THEN THE LinghongFrp SHALL 使用新密码重新加密所有存储的凭据
10. WHEN 用户删除平台配置 THEN THE LinghongFrp SHALL 安全清除加密存储中的对应凭据，并覆盖内存数据
11. WHEN 系统记录日志 THEN THE LinghongFrp SHALL 不在日志中记录任何敏感信息（密码、Token使用***替代）
12. WHEN 创建临时TOML文件 THEN THE LinghongFrp SHALL 设置严格的文件权限（仅当前用户可读），并在进程结束后自动删除
13. WHEN 用户导出配置 THEN THE LinghongFrp SHALL 使用用户提供的密码加密备份文件，并添加SHA-256校验和
14. WHEN 系统支持 THEN THE LinghongFrp SHALL 可选使用系统密钥环（Windows Credential Manager / macOS Keychain / Linux Secret Service）存储主密钥
15. WHEN 执行敏感操作 THEN THE LinghongFrp SHALL 记录审计日志（登录、凭据修改、配置导出等），但不包含敏感信息


### Requirement 21: 节点延迟测试功能

**User Story:** 作为用户，我希望能够测试不同节点的延迟和连通性，以便选择最快最稳定的节点来创建隧道。

#### Acceptance Criteria

1. WHEN 用户选择测试节点 THEN THE LinghongFrp SHALL 在本地启动一个简易TCP测试服务器（随机端口）
2. WHEN 测试服务器启动 THEN THE LinghongFrp SHALL 自动创建一个临时TCP隧道连接到该测试服务器
3. WHEN 临时隧道建立 THEN THE LinghongFrp SHALL 通过远程端口连接回本地测试服务器
4. WHEN 建立连接 THEN THE LinghongFrp SHALL 发送测试数据包并测量往返时间（RTT）
5. WHEN 测试完成 THEN THE LinghongFrp SHALL 显示节点延迟、丢包率和连接成功率
6. WHEN 测试结束 THEN THE LinghongFrp SHALL 自动清理临时隧道、停止测试服务器并删除相关配置文件
7. WHEN 用户批量测试多个节点 THEN THE LinghongFrp SHALL 支持并发测试多个节点并显示对比结果
8. WHEN 测试失败 THEN THE LinghongFrp SHALL 显示详细的错误信息（连接超时、认证失败、端口被占用等）
9. WHEN 用户查看测试历史 THEN THE LinghongFrp SHALL 保存最近的测试结果供用户参考
10. WHEN 测试过程中 THEN THE LinghongFrp SHALL 显示实时进度（启动服务器、创建隧道、测试连接、清理资源）
11. WHEN 测试服务器启动失败 THEN THE LinghongFrp SHALL 自动尝试其他可用端口
12. WHEN 用户取消测试 THEN THE LinghongFrp SHALL 立即停止测试并清理所有临时资源
13. WHEN 测试完成 THEN THE LinghongFrp SHALL 根据延迟结果推荐最佳节点
14. WHEN 用户配置测试参数 THEN THE LinghongFrp SHALL 支持自定义测试次数、超时时间和数据包大小
15. WHEN 测试多个节点 THEN THE LinghongFrp SHALL 在界面上以图表形式展示各节点的延迟对比
