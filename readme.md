# Iris Photo Gallery Docker 部署

一个基于 Docker 的 [Iris Photo Gallery](https://github.com/Iris-Photo-Gallery/iris) 部署方案，让您能够快速部署现代化的照片画廊网站。

## 🚀 快速开始

### 环境要求

- Docker
- Docker Compose (可选)

### 1. 修改配置文件

**`config.json`**

```json
{
  "name": "Your Photo Gallery", // 网站名称
  "title": "Your Photo Gallery", // 页面标题
  "description": "Capturing beautiful moments in life", // 网站描述
  "url": "https://", // 个人 URL
  "accentColor": "#fb7185", // 主题色
  "author": {
    "name": "Your Name",
    "url": "https://your-website.com",
    "avatar": "https://your-avatar-url.com/avatar.png"
  },
  "social": {
    "twitter": "@yourusername"
  },
  "extra": {
    "accessRepo": true
  }
}
```

**`builder.config.json`**

```json
{
  "repo": { // 使用远端仓库作为 manifest 和 thumbnail 缓存
    "enable": false,
    "url": "https://github.com/username/gallery-public"
  },
  "storage": { // 存储配置
    "provider": "s3",
    "bucket": "your-photos-bucket",
    "region": "us-east-1",
    "prefix": "photos/",
    "customDomain": "cdn.yourdomain.com"
  }
}
```

**`.env`**

- S3 存储配置

```
S3_ACCESS_KEY_ID=your_access_key_id
S3_SECRET_ACCESS_KEY=your_secret_access_key
```

- PG (可选)

```
PG_CONNECTION_STRING=
```

- GIT （可选）

```
GIT_TOKEN=
```

### 2. 构建 Docker 镜像

```bash
docker build -t iris-gallery .
```

### 3. 运行容器

```bash
docker run -p 3000:3000 iris-gallery
```

或者使用 Docker Compose:

```yaml
version: '3.8'
services:
  iris-gallery:
    build: .
    ports:
      - '3000:3000'
    environment:
      - NODE_ENV=production
    volumes:
      - ./config.json:/app/config.json
      - ./builder.config.json:/app/builder.config.json
      - ./.env:/app/.env
```

### 4. 使用 GitHub Actions 自动构建（推荐）

我们提供了GitHub Actions工作流，可以自动构建Docker镜像并推送到GitHub Container Registry (GHCR)。

#### 配置步骤：

1. **Fork 此仓库**到您的GitHub账户

2. **配置 GitHub Secrets**
   进入您的仓库 `Settings` → `Secrets and variables` → `Actions`，添加以下必需的Secrets：
   
   ```
   # 基础认证信息
   S3_ACCESS_KEY_ID=your_access_key_id
   S3_SECRET_ACCESS_KEY=your_secret_access_key
   
   # 存储配置（Cloudflare R2或AWS S3）
   S3_BUCKET_NAME=your_bucket_name
   S3_ENDPOINT=https://your-account-id.r2.cloudflarestorage.com
   S3_CUSTOM_DOMAIN=your-custom-domain.com
   ```
   
   其他可选配置请参考 [GitHub Secrets配置说明](./github-secrets-setup.md)

3. **手动触发构建**
   - 进入仓库的 `Actions` 标签页
   - 选择 `构建并推送Docker镜像` 工作流
   - 点击 `Run workflow` 按钮
   - 可选择：
     - 🏷️ Docker镜像标签（默认：latest）
     - 🔧 构建类型：
       - `default`: 标准增量构建
       - `force-all`: 强制重新构建所有内容  
       - `force-thumbnails`: 仅强制重新生成缩略图
       - `force-manifest`: 仅强制重新生成清单文件
     - 🚀 是否部署到VPS服务器
   - 等待构建（和部署）完成

4. **部署选项**

   **方式A：仅构建镜像**
   - 保持"是否部署到VPS服务器"为关闭状态
   - 手动使用构建的镜像：
   ```bash
   docker pull ghcr.io/[your-username]/pic_pigsbig:latest
   docker run -p 3000:3000 ghcr.io/[your-username]/pic_pigsbig:latest
   ```

   **方式B：自动部署到VPS**
   - 配置VPS相关的GitHub Secrets（详见配置指南）
   - 勾选"是否部署到VPS服务器"
   - 系统自动完成：构建 → 推送 → 部署

#### 优势：
- ✅ 全自动化构建流程
- ✅ 敏感信息安全管理
- ✅ 支持多架构（amd64/arm64）
- ✅ 构建缓存优化
- ✅ 版本管理和标签
- 🚀 **新增**：一键部署到VPS服务器
- 🔄 **新增**：自动容器更新和重启（always策略）
- 🛡️ **新增**：零停机时间部署

详细配置说明请查看：[GitHub Secrets配置指南](./github-secrets-setup.md)

## 📄 许可证

MIT License © 2025
