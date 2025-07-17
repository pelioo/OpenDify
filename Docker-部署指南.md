# OpenDify Docker 部署指南

OpenDify 作为一款能将 Dify API 转换为 OpenAI API 格式的代理服务器，通过 Docker 部署可大幅提升环境一致性与部署效率。本文汇总了完整部署流程、关键注意事项及常见问题解决方案。

## 一、Docker 部署 OpenDify 

### 1.克隆源代码
   克隆项目源代码，进入项目目录：
   ```bash
   git clone https://github.com/pelioo/OpenDify.git
   cd OpenDify
   ```

### 2.环境变量配置
   ```bash
   复制 .env.example 为 .env 
   编辑 .env 文件中的相关配置 
   ```
#### 变量配置 注意事项

**- Dify API 地址配置**：
在 Docker 容器中、Dify API 的 IP 地址不能是`localhost`和`127.0.0.1`，否则无法访问、这是指向容器内部的。

建议配置为宿主机或其它 IP 地址，把宿主机 IP 固定。

**- OpenDify 服务地址**：
必须配置为`0.0.0.0`，否则容器外部无法访问。
SERVER_HOST="127.0.0.1" ，需改为：0.0.0.0   # 否则宿主机无法访问容器内的服务

### 3.构建 Docker 镜像 
   在项目根目录执行：
   ```bash
   docker build -t open-dify:latest .

   # 此步可跳过...!
   ```

### 4.启动 Docker 容器 
   ```bash
   # 启动 
   docker-compose up -d

   # 后续修改 .env 文件后重启
   docker compose down
   docker compose up -d
   ```


## 二、注意事项


1. **网络地址与端口**  
  
   - **服务监听地址**：`.env` 中 `SERVER_HOST` 必须设为 `0.0.0.0`，否则容器外无法访问（默认 `127.0.0.1` 仅允许容器内访问）。
   - **端口映射一致性**：`Dockerfile` 中 `EXPOSE 5000`、`docker-compose.yml` 端口映射（`5000:5000`）与 `.env` 中 `SERVER_PORT=5000` 需保持一致。

2. **API 密钥有效性**  
   - 确保 `.env` 中 `DIFY_API_KEYS` 包含有效的 Dify 应用密钥（可在 Dify 平台「访问API」页面获取），无效密钥会导致关联失败（日志显示 `无法获取API密钥信息`），但不影响其他有效密钥使用。



## 三、常见问题与解决方案

### 1.容器启动后无法访问服务（`http://localhost:5000` 无响应）
- **可能原因**：
  - `.env`文件中`SERVER_HOST` 配置为 `127.0.0.1`（仅容器内可见）。
  - 端口映射错误或未暴露端口。

- **解决方案**：
  1. 检查 `.env` 中是否为 `SERVER_HOST=0.0.0.0`。
  2. 确认 `docker run` 或 `docker-compose` 中端口映射为 `5000:5000`。

---
END
