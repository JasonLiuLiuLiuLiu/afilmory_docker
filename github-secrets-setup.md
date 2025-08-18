# GitHub Secrets 配置说明

为了使用GitHub Actions自动构建和部署工作流，您需要在GitHub仓库中配置以下Secrets。

## 🔑 必需的Secrets

在您的GitHub仓库中，进入 `Settings` → `Secrets and variables` → `Actions` → `New repository secret`，添加以下密钥：

### 📌 Cloudflare R2 用户特别说明

如果您使用Cloudflare R2存储，以下配置为**必需**：
- `S3_ACCESS_KEY_ID` - R2 API Token的Access Key ID
- `S3_SECRET_ACCESS_KEY` - R2 API Token的Secret Access Key  
- `S3_BUCKET_NAME` - R2存储桶名称
- `S3_ENDPOINT` - R2 API端点 (格式: `https://[account-id].r2.cloudflarestorage.com`)
- `S3_CUSTOM_DOMAIN` - 您的R2自定义域名

### 📌 AWS S3 用户说明

如果使用AWS S3，`S3_ENDPOINT`可以省略，其他配置保持一致。

### S3/存储相关配置（支持AWS S3和Cloudflare R2）

| Secret名称 | 描述 | 示例值 | 是否必需 |
|-----------|------|-------|---------|
| `S3_ACCESS_KEY_ID` | S3访问密钥ID | `AKIAIOSxxxxN7EXAMPLE` | ✅ 必需 |
| `S3_SECRET_ACCESS_KEY` | S3秘密访问密钥 | `wJalrXUtnFEMI/Kxxxxx/bPxRfiCYEXAMPLEKEY` | ✅ 必需 |
| `S3_BUCKET_NAME` | 存储桶名称 | `pic-impact` | ✅ 必需 |
| `S3_ENDPOINT` | S3端点URL（R2必需） | `https://xxx.r2.cloudflarestorage.com` | ✅ 必需（Cloudflare R2） |
| `S3_CUSTOM_DOMAIN` | 自定义域名 | `r2-pic-impact.pigsbig.com` | ✅ 必需 |
| `S3_REGION` | 存储区域 | `us-east-1` | 🔄 可选，默认us-east-1 |
| `S3_PREFIX` | 存储前缀 | `img` | 🔄 可选，默认img |


### Git相关配置

| Secret名称 | 描述 | 示例值 | 是否必需 |
|-----------|------|-------|---------|
| `GIT_TOKEN` | Git访问令牌 | `ghp_xxxxxxxxxxxxxxxxxxxx` | 🔄 可选，用于访问私有仓库 |
| `REPO_URL` | 仓库URL | `https://github.com/username/gallery-public` | 🔄 可选 |
| `REPO_ENABLE` | 是否启用仓库 | `true` 或 `false` | 🔄 可选 |

### 数据库配置

| Secret名称 | 描述 | 示例值 | 是否必需 |
|-----------|------|-------|---------|
| `PG_CONNECTION_STRING` | PostgreSQL连接字符串 | `postgresql://user:pass@host:5432/db` | 🔄 可选 |

### 网站配置

| Secret名称 | 描述 | 示例值 | 是否必需 |
|-----------|------|-------|---------|
| `SITE_NAME` | 网站名称 | `我的照片画廊` | 🔄 可选，会覆盖config.json中的配置 |
| `SITE_TITLE` | 网站标题 | `我的照片画廊` | 🔄 可选 |
| `SITE_DESCRIPTION` | 网站描述 | `记录生活中的美好瞬间` | 🔄 可选 |
| `SITE_URL` | 网站URL | `https://gallery.yourdomain.com` | 🔄 可选 |
| `ACCENT_COLOR` | 主题色 | `#fb7185` | 🔄 可选 |
| `AUTHOR_NAME` | 作者姓名 | `Your Name` | 🔄 可选 |
| `AUTHOR_URL` | 作者网站 | `https://yourdomain.com` | 🔄 可选 |
| `AUTHOR_AVATAR` | 作者头像URL | `https://avatar.url/avatar.png` | 🔄 可选 |
| `TWITTER_HANDLE` | Twitter用户名 | `@yourusername` | 🔄 可选 |

### VPS部署配置

| Secret名称 | 描述 | 示例值 | 是否必需 |
|-----------|------|-------|---------|
| `VPS_HOST` | VPS服务器IP地址或域名 | `123.456.789.0` 或 `your-server.com` | ✅ 部署时必需 |
| `VPS_USERNAME` | VPS服务器用户名 | `root` 或 `ubuntu` | ✅ 部署时必需 |
| `VPS_SSH_KEY` | SSH私钥 | `-----BEGIN OPENSSH PRIVATE KEY-----...` | ✅ 部署时必需 |
| `VPS_PORT` | SSH端口 | `22` | 🔄 可选，默认22 |
| `VPS_HOST_PORT` | VPS上的服务端口 | `3000` 或 `80` | 🔄 可选，默认3000 |

> **📌 注意**: 容器内部端口固定为3000，`VPS_HOST_PORT`控制的是外部访问端口。

## 🚀 如何使用

1. **配置Secrets**: 按照上表在GitHub仓库中配置所需的Secrets
2. **手动触发构建**: 
   - 进入GitHub仓库的 `Actions` 标签页
   - 选择 `构建并推送Docker镜像` 工作流
   - 点击 `Run workflow` 按钮
   - 可选择性地输入Docker镜像标签（默认为 `latest`）
3. **等待构建完成**: 工作流会自动：
   - 生成配置文件
   - 构建Docker镜像
   - 推送到GitHub Container Registry

## 📦 构建结果

构建成功后，Docker镜像将推送到：
```
ghcr.io/[your-username]/pic_pigsbig:latest
```

## 🔧 运行容器

使用以下命令运行构建好的容器：

```bash
# 从GHCR拉取镜像
docker pull ghcr.io/[your-username]/pic_pigsbig:latest

# 运行容器
docker run -p 3000:3000 ghcr.io/[your-username]/pic_pigsbig:latest
```

## 📝 注意事项

1. **最小配置**: 只有S3相关的两个密钥是必需的，其他配置都是可选的
2. **覆盖规则**: 如果设置了对应的Secret，它会覆盖配置文件中的默认值
3. **安全性**: 所有敏感信息都通过GitHub Secrets管理，确保安全性
4. **多架构支持**: 工作流支持构建 `linux/amd64` 和 `linux/arm64` 两种架构
5. **缓存优化**: 使用GitHub Actions缓存来加速构建过程

## 🔧 Cloudflare R2 配置获取指南

### 1. 获取 R2 API Token
1. 登录 [Cloudflare Dashboard](https://dash.cloudflare.com/)
2. 进入 `R2 Object Storage` → `Manage R2 API tokens`
3. 点击 `Create API token`
4. 选择权限：`Object Read & Write`
5. 获取 `Access Key ID` 和 `Secret Access Key`

### 2. 获取 Account ID 和 Endpoint
1. 在R2页面右侧可以看到您的 `Account ID`
2. Endpoint格式为：`https://[Account ID].r2.cloudflarestorage.com`

### 3. 设置自定义域名
1. 在R2存储桶设置中配置自定义域名
2. 添加CNAME记录指向R2提供的域名

### 4. 示例配置
```
S3_ACCESS_KEY_ID=f002c1d75fd90dc0afe98171ee1b3be5
S3_SECRET_ACCESS_KEY=8bc9fb0834e7e0d2c5e1a8c4b15a6f9e7d3c2a1b
S3_BUCKET_NAME=my-photo-gallery
S3_ENDPOINT=https://abc123def456.r2.cloudflarestorage.com
S3_CUSTOM_DOMAIN=cdn.example.com
S3_REGION=us-east-1
S3_PREFIX=img
```

## 🖥️ VPS部署配置指南

### 1. 准备VPS服务器

确保您的VPS服务器已安装Docker：

```bash
# Ubuntu/Debian
sudo apt update
sudo apt install docker.io docker-compose -y
sudo systemctl start docker
sudo systemctl enable docker

# 将用户添加到docker组（可选，避免每次sudo）
sudo usermod -aG docker $USER
```

### 2. 生成SSH密钥对

在您的本地机器上生成SSH密钥：

```bash
# 生成SSH密钥对
ssh-keygen -t rsa -b 4096 -C "github-actions-deploy"

# 将公钥添加到VPS服务器
ssh-copy-id -i ~/.ssh/id_rsa.pub username@your-vps-ip
```

或者手动复制公钥到VPS：

```bash
# 在VPS上创建.ssh目录
mkdir -p ~/.ssh
chmod 700 ~/.ssh

# 将公钥内容添加到authorized_keys
echo "your-public-key-content" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

### 3. 获取SSH私钥

```bash
# 查看私钥内容（用于配置GitHub Secret）
cat ~/.ssh/id_rsa
```

复制完整的私钥内容（包括 `-----BEGIN OPENSSH PRIVATE KEY-----` 和 `-----END OPENSSH PRIVATE KEY-----`）

### 4. 配置防火墙（可选）

```bash
# Ubuntu UFW示例
sudo ufw allow 22    # SSH
sudo ufw allow 3000  # 应用端口
sudo ufw enable
```

### 5. VPS部署Secrets配置示例

```
VPS_HOST=123.456.789.0
VPS_USERNAME=ubuntu
VPS_SSH_KEY=-----BEGIN OPENSSH PRIVATE KEY-----
MIIEpAIBAAKCAQEA1234567890abcdef...
...完整的私钥内容...
-----END OPENSSH PRIVATE KEY-----
VPS_PORT=22
VPS_HOST_PORT=3000
```

## 🚀 部署使用方法

### 方式1：仅构建镜像
1. 进入Actions页面
2. 选择"构建并推送Docker镜像"
3. 点击"Run workflow"
4. 保持"是否部署到VPS服务器"为false
5. 等待构建完成

### 方式2：构建并部署
1. 确保已配置所有VPS相关的Secrets
2. 进入Actions页面
3. 选择"构建并推送Docker镜像"
4. 点击"Run workflow"
5. 选择构建类型（可选）：
   - **default**: 标准增量构建
   - **force-all**: 强制重新构建所有内容
   - **force-thumbnails**: 仅强制重新生成缩略图
   - **force-manifest**: 仅强制重新生成清单文件
6. ✅ 勾选"是否部署到VPS服务器"
7. 等待构建和部署完成

## 🆘 故障排除

如果构建或部署失败，请检查：

### 构建相关问题
1. **必需的Secrets是否正确配置**
   - Cloudflare R2用户：确保所有5个必需的S3配置都已设置
   - AWS S3用户：至少需要Access Key、Secret Key和Bucket Name
2. **存储凭据是否有效**
   - R2 API Token是否有正确的读写权限
   - 存储桶是否存在且可访问
3. **端点配置是否正确**
   - R2用户确保Endpoint URL格式正确
   - 自定义域名是否已正确配置
4. **Git令牌权限**（如果使用）
   - 确保Token有足够的仓库访问权限

### VPS部署相关问题
1. **SSH连接失败**
   - 检查VPS_HOST、VPS_USERNAME、VPS_PORT是否正确
   - 确认SSH私钥格式正确（包含完整的BEGIN和END标记）
   - 验证SSH密钥是否已添加到VPS的authorized_keys
2. **Docker相关错误**
   - 确保VPS已安装Docker并正在运行
   - 检查用户是否有Docker执行权限
   - 确认VPS有足够的磁盘空间
3. **网络连接问题**
   - 检查VPS防火墙设置
   - 确认端口没有被其他服务占用（容器固定使用3000端口）
   - 验证网络连接是否正常

### 常见错误解决

#### 构建错误
- **403 Forbidden**: 检查API Token权限设置
- **NoSuchBucket**: 确认存储桶名称正确
- **Invalid Endpoint**: 检查Account ID和Endpoint格式

#### 部署错误
- **Permission denied (publickey)**: SSH密钥配置错误
- **Connection timeout**: 检查VPS IP地址和SSH端口
- **docker: command not found**: VPS未安装Docker
- **Port already in use**: 端口被占用，停止冲突的服务
- **Container failed to start**: 检查容器日志确定具体错误

### 调试命令

在VPS上手动调试：
```bash
# 检查Docker状态
sudo systemctl status docker

# 查看容器状态
docker ps -a

# 查看容器日志
docker logs pic-gallery

# 手动测试镜像拉取
docker pull ghcr.io/[username]/pic_pigsbig:latest

# 检查端口占用
sudo netstat -tlnp | grep :3000
```

更多问题请查看GitHub Actions的构建日志。
