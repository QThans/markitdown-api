# GitHub Actions 工作流说明

本项目包含两个主要的GitHub Actions工作流，用于自动化Docker镜像的构建、测试和发布。

## 工作流文件

### 1. docker-build.yml
**触发条件：**
- 推送到 `main` 或 `develop` 分支
- 创建版本标签（如 `v1.0.0`）
- 创建Pull Request到 `main` 分支

**功能：**
- 构建多架构Docker镜像（linux/amd64, linux/arm64）
- 推送到GitHub Container Registry (ghcr.io)
- 运行Trivy安全扫描
- 缓存构建层以提高效率

### 2. release.yml
**触发条件：**
- 推送版本标签（如 `v1.0.0`）

**功能：**
- 构建生产就绪的Docker镜像
- 创建多个标签（版本号、主版本、最新版本）
- 生成发布说明

## 镜像标签策略

### 开发分支
- `main` 分支推送：`latest`
- `develop` 分支推送：`develop`
- Pull Request：不推送镜像

### 版本标签
- `v1.0.0`：创建标签 `1.0.0`, `1.0`, `1`, `latest`
- `v1.2.3`：创建标签 `1.2.3`, `1.2`, `1`, `latest`

## 使用方法

### 1. 启用GitHub Container Registry
确保仓库设置中启用了GitHub Container Registry。

### 2. 设置权限
工作流使用 `GITHUB_TOKEN` 自动认证，无需额外配置。

### 3. 查看构建结果
- 在Actions标签页查看构建状态
- 在Packages标签页查看发布的镜像
- 在Security标签页查看安全扫描结果

### 4. 使用镜像
```bash
# 拉取最新镜像
docker pull ghcr.io/your-username/markitdown-api:latest

# 运行容器
docker run -p 8490:8490 ghcr.io/your-username/markitdown-api:latest

# 拉取特定版本
docker pull ghcr.io/your-username/markitdown-api:1.0.0
```

## 安全特性

- 使用非root用户运行容器
- 多阶段构建减少镜像大小
- Trivy安全扫描检测漏洞
- 自动安全报告上传到GitHub Security

## 缓存优化

- 使用GitHub Actions缓存加速构建
- 多架构构建共享缓存层
- 增量构建减少构建时间
