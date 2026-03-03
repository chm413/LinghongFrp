# LinghongFrp Spec 更新日志

## 2024-01-XX - 安全增强：登录数据加密存储

### 新增需求

#### Requirement 20: 登录数据加密存储与安全管理
- 强制设置主密码保护所有平台凭据
- 密码强度验证（最少8位，包含大小写字母、数字、特殊字符）
- 使用AES-256-GCM加密存储所有敏感数据
- PBKDF2密钥派生（100,000次迭代）
- 运行时Token注入，不在TOML文件中明文保存
- 自动锁定和会话管理
- 主密码修改和凭据重新加密
- 安全的临时文件管理
- 加密备份和SHA-256完整性校验
- 系统密钥环集成（Windows/macOS/Linux）
- 审计日志和敏感信息脱敏

### 设计更新

#### 新增安全组件
- **SecureCredentialStore**: 加密凭据存储管理器
  - AES-256-GCM加密/解密
  - PBKDF2密钥派生
  - 凭据CRUD操作
  - 完整性验证

- **KeyringStore**: 系统密钥环集成
  - Windows Credential Manager
  - macOS Keychain
  - Linux Secret Service

#### 安全最佳实践
1. 首次启动强制设置主密码
2. 长时间不活动自动锁定
3. 内存敏感数据安全清理
4. 日志中敏感信息脱敏
5. 临时文件严格权限控制

### 实现任务

#### Task 22: 登录数据加密存储实现
- 22.1: 主密码管理
- 22.2: 加密凭据存储
- 22.3: 系统密钥环集成
- 22.4: TOML配置安全注入
- 22.5: 会话管理和自动锁定
- 22.6: 主密码修改功能
- 22.7: 安全日志和审计
- 22.8: 加密备份和恢复
- 22.9: 属性测试

#### Task 23: 安全功能界面实现
- 23.1: 首次启动向导
- 23.2: 锁定/解锁界面
- 23.3: 安全设置页面
- 23.4: 集成测试

#### Task 24: 检查点 - 安全功能验证

### 正确性属性

#### Property 35: Credential Encryption Security
验证凭据加密的安全性和可逆性

#### Property 36: Master Key Derivation Consistency
验证密钥派生的一致性和确定性

#### Property 37: Secure Memory Cleanup
验证敏感数据的安全清理

### 加密存储格式

```json
{
    "version": "1",
    "salt": "base64_encoded_salt",
    "platforms": {
        "openfrp": {
            "encrypted_data": "base64_encoded_encrypted_credentials",
            "created_at": "2024-01-01T00:00:00Z",
            "updated_at": "2024-01-01T00:00:00Z"
        }
    }
}
```

---

## 2024-01-XX - FRP客户端管理功能添加（使用原版frpc + TOML配置）

### 架构决策

**重要变更**：LinghongFrp使用官方原版frpc客户端，通过生成标准TOML配置文件启动隧道。

**优势**：
1. 完全兼容官方frpc的所有功能
2. 使用经过充分测试的官方版本
3. 跟随官方版本更新，无需维护自定义版本
4. 使用frpc官方推荐的TOML配置格式

### 新增需求

#### Requirement 18: FRP客户端版本管理（更新）
- 从官方GitHub下载原版frpc
- 为每个隧道生成标准TOML配置文件
- 使用frpc命令行工具（frpc -c config.toml）启动独立进程
- 运行时Token注入，不在TOML中明文保存

#### Requirement 19: FRP客户端源管理（更新）
- 支持导入标准frpc TOML配置文件
- 导出为标准TOML格式，可直接用于原版frpc
- 自动识别服务器地址并匹配平台

### 设计更新

#### 新增组件
- **FRPCConfigManager**: TOML配置文件管理器
  - 生成标准TOML配置
  - 解析和导入TOML配置
  - 配置验证和转换

- **FRPCProcessManager**: frpc进程管理器
  - 启动/停止frpc进程
  - 进程监控和日志管理
  - 批量操作支持

#### TOML配置结构
完整支持frpc官方TOML配置格式，包括：
- 服务器配置（serverAddr, serverPort）
- 认证配置（auth.method, auth.token）
- 传输配置（transport.protocol, transport.tls）
- 日志配置（log.to, log.level）
- 代理配置（proxies数组）
- 访问端配置（visitors数组，用于P2P）

### 存储结构

```
~/.linghongfrp/
├── frpc/
│   ├── versions/
│   │   ├── v0.51.0/frpc
│   │   ├── v0.52.0/frpc
│   │   └── v0.53.0/frpc
│   ├── current -> versions/v0.52.0/frpc
│   └── versions.json
├── config/
│   ├── tunnels/
│   │   ├── tunnel-001.toml
│   │   ├── tunnel-002.toml
│   │   └── tunnel-003.toml
│   ├── platforms.json (加密存储)
│   └── app.json
├── logs/
│   ├── frpc/
│   │   ├── tunnel-001.log
│   │   ├── tunnel-002.log
│   │   └── tunnel-003.log
│   └── app.log
```

---

## 之前的更新

### 2024-01-XX - 初始版本
- 完成17个核心需求
- 完成系统架构设计
- 完成实现计划（17个主要任务）
- 修复所有中文乱码问题
