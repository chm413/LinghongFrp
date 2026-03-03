# LinghongFrp 安全设计文档

## 概述

LinghongFrp采用多层安全防护策略，确保用户的登录凭据、配置数据和通信过程的安全性。本文档详细说明了系统的安全设计和最佳实践。

## 核心安全原则

1. **零明文存储**：所有敏感数据（密码、Token、API Key）必须加密存储
2. **最小权限原则**：临时文件和配置文件使用最严格的权限设置
3. **纵深防御**：多层加密和验证机制
4. **安全默认**：默认启用所有安全功能
5. **透明审计**：记录所有敏感操作的审计日志

## 加密架构

### 1. 主密码系统

#### 首次启动
```
用户设置主密码
    ↓
密码强度验证（8位+，大小写+数字+特殊字符）
    ↓
生成随机Salt（32字节）
    ↓
PBKDF2密钥派生（100,000次迭代，SHA-256）
    ↓
生成主加密密钥（256位）
```

#### 密钥派生算法
```go
masterKey = PBKDF2(
    password:   用户主密码,
    salt:       随机生成的32字节salt,
    iterations: 100000,
    keyLen:     32,  // AES-256
    hash:       SHA-256
)
```

### 2. 凭据加密存储

#### 加密流程
```
平台凭据（JSON）
    ↓
序列化为字节数组
    ↓
AES-256-GCM加密
    ├─ 密钥：主加密密钥
    ├─ Nonce：随机生成（12字节）
    └─ 认证标签：自动生成（16字节）
    ↓
Base64编码
    ↓
存储到加密配置文件
```

#### 存储格式
```json
{
    "version": "1",
    "encryption": {
        "algorithm": "AES-256-GCM",
        "kdf": "PBKDF2-SHA256",
        "iterations": 100000
    },
    "salt": "base64_encoded_32_bytes_salt",
    "platforms": {
        "openfrp": {
            "encrypted_data": "base64_encoded_ciphertext_with_nonce_and_tag",
            "created_at": "2024-01-01T00:00:00Z",
            "updated_at": "2024-01-01T00:00:00Z"
        }
    }
}
```

### 3. TOML配置安全

#### 问题
frpc的TOML配置文件需要包含Token，但不能明文存储。

#### 解决方案：运行时注入
```toml
# 配置模板（不包含真实Token）
[auth]
method = "token"
token = "${ENCRYPTED_TOKEN}"  # 占位符
```

```go
// 运行时流程
1. 从加密存储读取Token
2. 解密Token
3. 替换TOML中的占位符
4. 写入临时文件（严格权限：0600）
5. 启动frpc进程
6. 进程结束后删除临时文件
```

#### 临时文件安全
- **位置**：系统临时目录 + 随机子目录
- **权限**：0600（仅当前用户可读写）
- **生命周期**：进程结束后立即删除
- **命名**：随机UUID，避免预测

### 4. 系统密钥环集成

#### 支持的平台
| 平台 | 密钥环 | 用途 |
|------|--------|------|
| Windows | Credential Manager | 存储主密钥 |
| macOS | Keychain | 存储主密钥 |
| Linux | Secret Service (libsecret) | 存储主密钥 |

#### 降级策略
```
尝试使用系统密钥环
    ↓
    失败？
    ↓
使用加密文件存储
    ↓
    失败？
    ↓
仅内存缓存（会话结束后需重新输入）
```

## 会话管理

### 自动锁定机制

#### 触发条件
1. 用户长时间不活动（默认15分钟，可配置）
2. 系统休眠/锁屏
3. 用户手动锁定

#### 锁定时的操作
```go
1. 清除内存中的主密钥
2. 清除所有解密的凭据
3. 覆盖敏感数据内存区域（写入零）
4. 显示锁定屏幕
5. 停止所有敏感操作
```

#### 解锁流程
```
用户输入主密码
    ↓
验证密码（尝试解密测试数据）
    ↓
    成功？
    ↓
重新派生主密钥
    ↓
恢复正常操作
```

### 内存安全

#### 敏感数据清理
```go
// 使用后立即清理
func clearSensitiveData(data []byte) {
    for i := range data {
        data[i] = 0
    }
}

// 自动清理模式
defer func() {
    clearSensitiveData(passwordBytes)
    clearSensitiveData(tokenBytes)
}()
```

## 日志安全

### 敏感信息脱敏

#### 脱敏规则
| 类型 | 原始值 | 脱敏后 |
|------|--------|--------|
| 密码 | `mypassword123` | `***` |
| Token | `abc123def456` | `abc***456` |
| API Key | `sk-1234567890abcdef` | `sk-***def` |
| 用户名 | `user@example.com` | `u***@example.com` |

#### 实现示例
```go
func sanitizeLog(message string) string {
    // 替换密码字段
    message = regexp.MustCompile(`"password":\s*"[^"]+"`).
        ReplaceAllString(message, `"password": "***"`)
    
    // 替换Token字段
    message = regexp.MustCompile(`"token":\s*"[^"]+"`).
        ReplaceAllString(message, `"token": "***"`)
    
    return message
}
```

## 备份安全

### 加密备份格式

```json
{
    "version": "1",
    "created_at": "2024-01-01T00:00:00Z",
    "encryption": {
        "algorithm": "AES-256-GCM",
        "kdf": "PBKDF2-SHA256",
        "iterations": 100000,
        "salt": "base64_encoded_salt"
    },
    "data": {
        "iv": "base64_encoded_iv",
        "ciphertext": "base64_encoded_backup_data",
        "tag": "base64_encoded_auth_tag"
    },
    "checksum": {
        "algorithm": "SHA-256",
        "value": "hex_encoded_sha256_of_plaintext"
    }
}
```

### 备份流程
```
1. 收集所有配置数据
2. 序列化为JSON
3. 计算SHA-256校验和
4. 用户提供备份密码
5. PBKDF2派生加密密钥
6. AES-256-GCM加密
7. 保存加密备份文件
```

### 恢复流程
```
1. 读取加密备份文件
2. 用户提供备份密码
3. PBKDF2派生解密密钥
4. AES-256-GCM解密
5. 验证SHA-256校验和
6. 反序列化JSON
7. 导入配置数据
```

## 审计日志

### 记录的敏感操作

| 操作 | 记录内容 | 不记录内容 |
|------|----------|------------|
| 用户登录 | 时间、结果 | 密码 |
| 凭据存储 | 平台名称、时间 | 凭据内容 |
| 凭据读取 | 平台名称、时间 | 凭据内容 |
| 主密码修改 | 时间、结果 | 新旧密码 |
| 配置导出 | 时间、文件路径 | 配置内容 |
| 配置导入 | 时间、来源 | 配置内容 |

### 审计日志格式
```json
{
    "id": "uuid",
    "timestamp": "2024-01-01T12:00:00Z",
    "action": "credential_store",
    "resource": "platform:openfrp",
    "result": "success",
    "details": "Stored encrypted credentials",
    "source_ip": "127.0.0.1"
}
```

## 安全最佳实践

### 用户指南

#### 主密码设置
✅ **推荐**：
- 使用至少12位的强密码
- 包含大小写字母、数字和特殊字符
- 不使用常见单词或个人信息
- 使用密码管理器生成和存储

❌ **避免**：
- 使用简单密码（如"12345678"）
- 使用与其他服务相同的密码
- 与他人共享主密码

#### 系统使用
✅ **推荐**：
- 启用自动锁定功能
- 定期更改主密码
- 定期备份配置（使用强密码）
- 在公共计算机上使用后立即锁定

❌ **避免**：
- 在不安全的环境中输入主密码
- 禁用自动锁定功能
- 将备份文件存储在不安全的位置

### 开发者指南

#### 代码规范
```go
// ✅ 正确：使用defer确保清理
func processCredentials(creds *AuthCredentials) error {
    defer clearSensitiveData([]byte(creds.Password))
    // 处理凭据...
    return nil
}

// ❌ 错误：忘记清理
func processCredentials(creds *AuthCredentials) error {
    // 处理凭据...
    return nil
}
```

#### 日志规范
```go
// ✅ 正确：脱敏后记录
log.Infof("User logged in: %s", sanitizeUsername(username))

// ❌ 错误：记录敏感信息
log.Infof("User logged in with password: %s", password)
```

## 威胁模型

### 防护的威胁

| 威胁 | 防护措施 |
|------|----------|
| 配置文件泄露 | AES-256-GCM加密 |
| 内存转储攻击 | 敏感数据及时清理 |
| 日志泄露 | 敏感信息脱敏 |
| 临时文件泄露 | 严格权限 + 自动删除 |
| 中间人攻击 | TLS 1.3加密传输 |
| 暴力破解 | PBKDF2高迭代次数 |
| 未授权访问 | 主密码保护 + 自动锁定 |

### 已知限制

1. **内存攻击**：无法完全防止具有管理员权限的恶意软件读取内存
2. **键盘记录**：无法防止键盘记录器捕获主密码输入
3. **物理访问**：无法防止物理访问计算机的攻击者

### 缓解建议

1. 使用可信的操作系统和安全软件
2. 保持系统和应用程序更新
3. 在安全的环境中使用应用程序
4. 使用全磁盘加密
5. 启用系统防火墙和杀毒软件

## 合规性

### 数据保护
- 符合GDPR数据保护要求
- 用户完全控制自己的数据
- 支持数据导出和删除
- 不收集或上传用户数据到云端

### 加密标准
- 使用NIST推荐的加密算法
- AES-256-GCM（FIPS 140-2认证）
- PBKDF2-SHA256（NIST SP 800-132）
- TLS 1.3（RFC 8446）

## 安全更新

### 报告安全问题
如果您发现安全漏洞，请通过以下方式报告：
- 邮箱：security@linghongfrp.com
- 不要公开披露，直到我们发布修复

### 安全更新策略
- 关键安全问题：24小时内发布补丁
- 高危安全问题：7天内发布补丁
- 中危安全问题：30天内发布补丁
- 低危安全问题：下一个版本修复

## 参考资料

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [NIST Cryptographic Standards](https://csrc.nist.gov/projects/cryptographic-standards-and-guidelines)
- [CWE/SANS Top 25](https://cwe.mitre.org/top25/)
- [PBKDF2 Specification (RFC 2898)](https://tools.ietf.org/html/rfc2898)
- [AES-GCM (NIST SP 800-38D)](https://csrc.nist.gov/publications/detail/sp/800-38d/final)
