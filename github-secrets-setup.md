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
| `STORAGE_BUCKET` | 存储桶名称 | `pic-impact` | 🔄 可选，会覆盖builder.config.json中的配置 |
| `STORAGE_REGION` | 存储区域 | `us-east-1` | 🔄 可选 |
| `STORAGE_PREFIX` | 存储前缀 | `img` | 🔄 可选 |
| `STORAGE_CUSTOM_DOMAIN` | 自定义域名 | `r2-pic-impact.pigsbig.com` | 🔄 可选 |

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

## 🆘 故障排除

如果构建失败，请检查：
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

### 常见错误解决
- **403 Forbidden**: 检查API Token权限设置
- **NoSuchBucket**: 确认存储桶名称正确
- **Invalid Endpoint**: 检查Account ID和Endpoint格式

更多问题请查看GitHub Actions的构建日志。
