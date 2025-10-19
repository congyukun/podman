# DNMP (Docker Nginx MySQL PHP) 迁移到 Podman

本文档提供了将DNMP项目迁移到Podman环境的指导说明。

## 准备工作

1. **安装Podman**

   在macOS上，可以使用Homebrew安装Podman：
   ```bash
   brew install podman
   ```

2. **初始化Podman虚拟机**
   ```bash
   podman machine init
   podman machine start
   ```

3. **安装podman-compose**
   ```bash
   brew install podman-compose
   ```

## 目录结构

```
./podman/
├── docker-compose.yaml    # Podman兼容的docker-compose配置
├── .env                   # 环境变量配置
├── services/              # 服务配置目录
│   ├── nginx/            # Nginx配置
│   ├── php/              # PHP配置
│   ├── mysql/            # MySQL配置
│   ├── redis/            # Redis配置
│   └── consul/           # Consul配置
├── www/                   # 网站源码目录
├── logs/                  # 日志目录
└── data/                  # 数据存储目录
```

## 启动服务

1. 进入podman目录
   ```bash
   cd podman
   ```

2. 使用podman-compose启动服务
   ```bash
   podman-compose up -d
   ```

## 重要注意事项

1. **网络配置**
   - Podman默认使用10.88.0.0/16网段
   - 在docker-compose.yaml中，我们已将extra_hosts中的IP地址从172.17.0.1改为10.88.0.1

2. **权限问题**
   - Podman默认使用rootless模式运行，可能会有权限问题
   - 如需调整权限，可以使用`podman unshare chown`命令

3. **数据持久化**
   - 所有数据都存储在podman/data目录中，确保该目录有正确的权限

4. **端口映射**
   - 端口映射与Docker相同，但在Podman中，端口是映射到Podman虚拟机的

## 常用命令

- 启动所有服务: `podman-compose up -d`
- 停止所有服务: `podman-compose down`
- 查看服务状态: `podman-compose ps`
- 查看日志: `podman-compose logs [服务名称]`
- 进入容器: `podman exec -it [容器名称] /bin/bash`

## 故障排除

1. **网络连接问题**
   - 检查Podman虚拟机是否正在运行: `podman machine status`
   - 确保防火墙未阻止端口访问

2. **权限被拒绝错误**
   - 使用`podman unshare chown -R 1000:1000 data/`调整数据目录权限

3. **容器启动失败**
   - 查看详细日志: `podman-compose logs --tail=100 [服务名称]`
   - 检查配置文件是否正确