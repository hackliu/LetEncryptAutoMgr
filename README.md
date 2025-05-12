# LetEncryptAutoMgr

LetEncryptAutoMgr 是一个用 Go 语言开发的自动化 SSL 证书管理工具，支持 Let's Encrypt 证书的申请、续期和格式转换。该工具可以自动为多个域名申请和管理 SSL 证书，并支持多种 Web 服务器的证书格式。

## 功能特点

- 支持 Let's Encrypt 证书的自动申请和续期
- 支持多种证书格式转换：
  - PEM 格式（适用于 Nginx）
  - PKCS#12 格式（适用于 IIS）
  - JKS 格式（适用于 Tomcat）
  - CRT/KEY 格式（标准 X.509 证书格式）
- 自动部署证书到多种 Web 服务器
- 支持邮件和 Webhook 通知
- 详细的日志记录
- 支持 Windows 和 Linux 系统
- 证书目录结构清晰，便于管理
- 可配置自动续期阈值和检查间隔

## 系统要求

- Go 1.16 或更高版本
- OpenSSL（用于证书格式转换）
- Java Keytool（用于 JKS 格式转换，仅在使用 Tomcat 时需要）

## 工具安装指南

### OpenSSL 安装方法

OpenSSL 是用于生成 PKCS#12 格式证书的必要工具。

#### Windows 安装 OpenSSL

1. 下载 OpenSSL 安装包：
   - 访问 [OpenSSL 官方下载页面](https://www.openssl.org/source/) 或
   - 使用 [Win32/Win64 OpenSSL 安装包](https://slproweb.com/products/Win32OpenSSL.html)（推荐新手使用）

2. 安装步骤：
   - 下载并运行安装程序
   - 按照安装向导提示完成安装
   - 建议选择"将 OpenSSL DLL 复制到 Windows 系统目录"选项

3. 设置环境变量：
   - 右键"此电脑" > "属性" > "高级系统设置" > "环境变量"
   - 在"系统变量"中编辑"Path"变量
   - 添加 OpenSSL 的 bin 目录路径（例如：`C:\Program Files\OpenSSL-Win64\bin`）
   - 点击"确定"保存更改

4. 验证安装：
   - 打开命令提示符（CMD）
   - 输入 `openssl version`
   - 应显示 OpenSSL 版本信息

#### Linux 安装 OpenSSL

大多数 Linux 发行版默认已安装 OpenSSL。如果需要安装或更新：

1. Debian/Ubuntu:
```bash
sudo apt-get update
sudo apt-get install openssl
```

2. CentOS/RHEL:
```bash
sudo yum update
sudo yum install openssl openssl-devel
```

3. 验证安装：
```bash
openssl version
```

### Java Keytool 安装方法

Keytool 是 JDK 的一部分，用于生成 JKS 格式证书（Tomcat 使用）。

#### Windows 安装 Java JDK 和 Keytool

1. 下载 Java JDK：
   - 访问 [Oracle JDK 下载页面](https://www.oracle.com/java/technologies/javase-downloads.html) 或
   - 使用 [OpenJDK](https://adoptopenjdk.net/)（开源版本）

2. 安装步骤：
   - 下载并运行安装程序
   - 按照安装向导提示完成安装

3. 设置环境变量：
   - 右键"此电脑" > "属性" > "高级系统设置" > "环境变量"
   - 在"系统变量"中新建"JAVA_HOME"，值为 JDK 安装路径（例如：`C:\Program Files\Java\jdk-11`）
   - 在"系统变量"中编辑"Path"变量
   - 添加 `%JAVA_HOME%\bin`
   - 点击"确定"保存更改

4. 验证安装：
   - 打开命令提示符（CMD）
   - 输入 `keytool -help`
   - 应显示 Keytool 帮助信息

#### Linux 安装 Java JDK 和 Keytool

1. Debian/Ubuntu:
```bash
sudo apt-get update
sudo apt-get install default-jdk
```

2. CentOS/RHEL:
```bash
sudo yum update
sudo yum install java-11-openjdk-devel
```

3. 验证安装：
```bash
keytool -help
```

## 安装方法

### 从源码编译

1. 克隆代码仓库：
```bash
git clone https://github.com/hackliu/LetEncryptAutoMgr.git
cd LetEncryptAutoMgr
```

2. 编译项目：
```bash
# Windows
go build -o bin/LetEncryptAutoMgr.exe cmd/LetEncryptAutoMgr/main.go

# Linux/Mac
go build -o bin/LetEncryptAutoMgr cmd/LetEncryptAutoMgr/main.go
```

编译参数说明：
- `-o`: 指定输出文件名
- `cmd/LetEncryptAutoMgr/main.go`: 主程序入口文件

如果需要交叉编译，可以使用以下命令：

```bash
# 编译 Windows 版本
GOOS=windows GOARCH=amd64 go build -o bin/LetEncryptAutoMgr.exe cmd/LetEncryptAutoMgr/main.go

# 编译 Linux 版本
GOOS=linux GOARCH=amd64 go build -o bin/LetEncryptAutoMgr cmd/LetEncryptAutoMgr/main.go

# 编译 Mac 版本
GOOS=darwin GOARCH=amd64 go build -o bin/LetEncryptAutoMgr cmd/LetEncryptAutoMgr/main.go
```

编译后的可执行文件将位于 `bin` 目录下。

### 直接下载

您也可以从 [Releases](https://github.com/hackliu/LetEncryptAutoMgr/releases) 页面下载预编译的二进制文件。

## 目录结构

安装完成后，系统会自动创建以下目录结构：

```
LetEncryptAutoMgr/
├── bin/                # 编译后的可执行文件
├── config.yaml         # 配置文件
├── logs/               # 日志文件目录
├── keys/               # ACME账户密钥
├── webroot/            # HTTP验证服务器根目录
└── SSLCertificate/     # 证书存储根目录
    └── example.com/    # 域名目录
        ├── pem/        # PEM 格式证书
        │   ├── cert.pem
        │   ├── privkey.pem
        │   ├── chain.pem
        │   └── fullchain.pem
        ├── crt/        # CRT/KEY 格式证书
        │   ├── example.com.crt
        │   └── example.com.key
        ├── pkcs12/     # PKCS#12 格式证书
        │   └── example.com.p12
        ├── jks/        # JKS 格式证书
        │   └── example.com.jks
        ├── nginx/      # Nginx 专用证书
        │   ├── fullchain.pem
        │   └── privkey.pem
        ├── tomcat/     # Tomcat 专用证书
        │   └── keystore.jks
        └── iis/        # IIS 专用证书
            └── example.com.pfx
```

## 配置说明

配置文件 `config.yaml` 包含以下主要部分：

### ACME 配置
```yaml
acme:
  environment: production  # 使用生产环境，可选值: production, staging
  email: your@email.com    # 注册邮箱，用于接收证书到期提醒
  keyDir: ./keys           # 密钥存储目录
```

> 注意：首次使用时建议将 environment 设置为 staging 进行测试，以避免触发 Let's Encrypt 的速率限制。

### HTTP 服务器配置
```yaml
http:
  port: 80              # 监听端口（必须为 80，Let's Encrypt 验证需要）
  webroot: ./webroot    # 网站根目录
```

> 注意：Let's Encrypt 的验证服务器始终使用 80 端口。如果端口被占用，需要配置端口转发。

### 证书存储配置
```yaml
certificate:
  rootDir: ./SSLCertificate  # 证书存储根目录
  formats:                   # 需要生成的证书格式
    - pem    # Nginx 格式
    - pkcs12 # IIS 格式
    - jks    # Tomcat 格式
    - crt    # 标准 X.509 证书格式
    - key    # 标准私钥格式
  deploy:                    # 中间件部署目录
    nginx: ./deploy/nginx
    tomcat: ./deploy/tomcat
    iis: ./deploy/iis
```

### 自动续期配置
```yaml
renewal:
  enabled: true         # 启用自动续期
  threshold: 30         # 到期前 30 天触发续期
  checkInterval: 24     # 每 24 小时检查一次
  retries: 3            # 失败重试次数
  retryInterval: 30     # 重试间隔（分钟）
```

### 通知配置
```yaml
notification:
  email:
    enabled: false      # 启用邮件通知
    smtp:
      host: smtp.example.com
      port: 587
      username: your@email.com
      password: your_password
    recipients:
      - admin@example.com
  webhook:
    enabled: false      # 启用 Webhook 通知
    url: https://hooks.example.com/services/xxx
    headers:
      Content-Type: application/json
      Authorization: Bearer your_token
```

### 日志配置
```yaml
logging:
  level: info          # 日志级别：debug, info, warn, error
  file: ./logs/LetEncryptAutoMgr.log
  retention: 30        # 日志保留天数
```

## 使用方法

### 基本使用

1. 修改配置文件 `config.yaml`，设置您的域名和邮箱等信息。

2. 运行程序：
```bash
# Windows
.\bin\LetEncryptAutoMgr.exe

# Linux/Mac
./bin/LetEncryptAutoMgr
```

3. 程序会自动：
   - 申请新的 SSL 证书
   - 转换为所需的格式
   - 部署到指定的中间件目录
   - 设置自动续期

### 命令行参数

```bash
# 指定配置文件
.\bin\LetEncryptAutoMgr.exe -config=custom-config.yaml

# 仅执行证书续期
.\bin\LetEncryptAutoMgr.exe -renew

# 显示调试日志
.\bin\LetEncryptAutoMgr.exe -debug
```

### 作为服务运行

#### Windows 服务配置

使用 NSSM (Non-Sucking Service Manager) 注册为 Windows 服务：

1. 下载并安装 [NSSM](https://nssm.cc/download)

2. 注册服务：
```bash
nssm install LetEncryptAutoMgr "C:\path\to\LetEncryptAutoMgr.exe"
nssm set LetEncryptAutoMgr AppDirectory "C:\path\to\LetEncryptAutoMgr"
nssm set LetEncryptAutoMgr Description "LetEncryptAutoMgr SSL Certificate Manager"
nssm set LetEncryptAutoMgr Start SERVICE_AUTO_START
nssm start LetEncryptAutoMgr
```

#### Linux Systemd 服务配置

1. 创建 systemd 服务文件：
```bash
sudo nano /etc/systemd/system/LetEncryptAutoMgr.service
```

2. 添加以下内容：
```ini
[Unit]
Description=LetEncryptAutoMgr SSL Certificate Manager
After=network.target

[Service]
Type=simple
User=root
ExecStart=/path/to/LetEncryptAutoMgr
WorkingDirectory=/path/to/LetEncryptAutoMgr
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

3. 启用并启动服务：
```bash
sudo systemctl daemon-reload
sudo systemctl enable LetEncryptAutoMgr
sudo systemctl start LetEncryptAutoMgr
```

4. 查看服务状态：
```bash
sudo systemctl status LetEncryptAutoMgr
```

## 证书更新和维护

### 手动续期证书

如果需要手动续期证书，可以运行：

```bash
.\bin\LetEncryptAutoMgr.exe -renew
```

### 查看证书信息

使用 OpenSSL 查看证书信息：

```bash
# 查看证书有效期
openssl x509 -in SSLCertificate/example.com/pem/cert.pem -noout -dates

# 查看证书详细信息
openssl x509 -in SSLCertificate/example.com/pem/cert.pem -noout -text
```

## 安全最佳实践

1. 权限控制：
   - 限制对证书目录的访问权限
   - 确保私钥文件权限设置为 600（仅所有者可读写）

2. 定期备份：
   - 备份 `keys/` 目录（包含 ACME 账户密钥）
   - 备份 `SSLCertificate/` 目录（包含所有证书）

3. 加固配置：
   - 使用防火墙限制对服务器的访问
   - 定期更新系统和依赖组件

4. 监控与通知：
   - 启用邮件或 Webhook 通知功能
   - 监控证书有效期和续期状态

## 注意事项

1. 端口要求：
   - 必须使用 80 端口进行域名验证
   - 如果 80 端口被占用，需要配置端口转发

2. 证书存储：
   - 证书文件存储在 `SSLCertificate` 目录下
   - 每个域名有独立的子目录
   - 私钥文件权限设置为 600

3. 自动续期：
   - 默认在证书到期前 30 天开始续期
   - 续期失败会自动重试
   - 建议启用通知功能以便及时发现问题

4. Let's Encrypt 限制：
   - 每个域名每周最多可申请 5 个证书
   - 每个账户每小时最多可创建 50 个证书
   - 详见 [Let's Encrypt 速率限制](https://letsencrypt.org/docs/rate-limits/)

## 常见问题

### 证书申请失败

1. 问题：HTTP-01 验证失败
   - 解决方案：
     - 确保 80 端口可访问
     - 检查域名是否正确解析到服务器
     - 检查防火墙设置
     - 查看日志文件获取详细错误信息

2. 问题：证书申请限制
   - 解决方案：
     - 使用 `staging` 环境进行测试
     - 避免短时间内频繁申请
     - 等待限制解除后再申请

### 格式转换失败

1. 问题：OpenSSL 错误
   - 解决方案：
     - 确认已正确安装 OpenSSL
     - 检查 OpenSSL 是否在环境变量中
     - 检查日志中的具体错误信息

2. 问题：Keytool 错误
   - 解决方案：
     - 确认已正确安装 Java JDK
     - 检查 Keytool 是否在环境变量中
     - 检查日志中的具体错误信息

### 自动续期失败

1. 问题：程序未运行
   - 解决方案：
     - 将程序配置为系统服务自动启动
     - 设置定时任务定期执行

2. 问题：网络问题
   - 解决方案：
     - 检查网络连接
     - 检查防火墙设置
     - 启用通知功能及时发现问题

## Web 服务器配置示例

### Nginx 配置

```nginx
server {
    listen 443 ssl;
    server_name example.com;

    ssl_certificate /path/to/deploy/nginx/example.com/fullchain.pem;
    ssl_certificate_key /path/to/deploy/nginx/example.com/privkey.pem;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;

    # 其他配置...
}
```

### Apache 配置

```apache
<VirtualHost *:443>
    ServerName example.com
    SSLEngine on
    SSLCertificateFile /path/to/SSLCertificate/example.com/crt/example.com.crt
    SSLCertificateKeyFile /path/to/SSLCertificate/example.com/crt/example.com.key

    # 其他配置...
</VirtualHost>
```

### Tomcat 配置

在 `server.xml` 中添加：

```xml
<Connector port="443" protocol="org.apache.coyote.http11.Http11NioProtocol"
           maxThreads="150" SSLEnabled="true">
    <SSLHostConfig>
        <Certificate certificateKeystoreFile="/path/to/deploy/tomcat/example.com/keystore.jks"
                     certificateKeystorePassword="changeit"
                     type="RSA" />
    </SSLHostConfig>
</Connector>
```

## 贡献指南

欢迎提交 Issue 和 Pull Request 来帮助改进项目。

1. Fork 项目
2. 创建功能分支 (`git checkout -b feature/amazing-feature`)
3. 提交更改 (`git commit -m 'Add some amazing feature'`)
4. 推送到分支 (`git push origin feature/amazing-feature`)
5. 创建 Pull Request

## 许可证

本项目采用 MIT 许可证。详见 [LICENSE](LICENSE) 文件。 
