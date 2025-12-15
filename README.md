# Postman to k6 Load Testing

> 🚀 使用 Postman Collection 自动生成 k6 负载测试脚本，并在 GitHub Actions 中运行

## 📖 项目简介

本项目演示了如何将现有的 **Postman 测试集合**转换为 **k6 负载测试脚本**，并通过 GitHub Actions 实现自动化性能测试。

### 工作流程

```
Postman Collection → postman-to-k6 → k6 Script → 负载测试 → 测试报告
```

## 📁 项目结构

```
postman-to-k6-test/
├── .github/
│   └── workflows/
│       └── test.yml          # GitHub Actions 工作流配置
├── postman/
│   └── collection.json       # Postman 测试集合
└── README.md
```

## 🎯 测试的 API

本项目使用 [JSONPlaceholder](https://jsonplaceholder.typicode.com/) 作为测试目标 API：

| 接口 | 方法 | 描述 | 断言 |
|------|------|------|------|
| `/posts` | GET | 获取所有文章 | 状态码 200，返回数组 |
| `/posts` | POST | 创建新文章 | 状态码 201，返回包含 id |

## 🚀 快速开始

### 本地运行

1. **安装依赖**

```bash
# 安装 postman-to-k6 转换工具
npm install -g @apideck/postman-to-k6

# 安装 k6（macOS）
brew install k6

# 或使用 Docker
docker pull grafana/k6
```

2. **转换 Postman 集合为 k6 脚本**

```bash
postman-to-k6 postman/collection.json -o k6-script.js
```

3. **运行负载测试**

```bash
# 冒烟测试（1 用户，10 秒）
k6 run k6-script.js --vus 1 --duration 10s

# 负载测试（10 用户，1 分钟）
k6 run k6-script.js --vus 10 --duration 1m

# 压力测试（50 用户，5 分钟）
k6 run k6-script.js --vus 50 --duration 5m
```

### GitHub Actions 自动运行

工作流会在以下情况自动触发：
- ✅ 推送到 `main` 分支
- ✅ 手动触发（workflow_dispatch）

## 📊 测试报告

每次运行后，GitHub Actions 会生成以下报告文件：

| 文件 | 说明 |
|------|------|
| `results.json` | 详细的测试数据点（每个请求的响应时间、状态等） |
| `summary.json` | 汇总统计（平均响应时间、P90/P95、成功率等） |
| `k6-script.js` | 转换后的 k6 脚本（便于调试） |

### 下载报告

1. 进入 GitHub 仓库 → **Actions** 标签页
2. 点击最近的 workflow 运行记录
3. 在页面底部 **Artifacts** 区域下载 `k6-test-results`

## 📈 测试配置说明

### 当前配置（冒烟测试）

```yaml
flags: --vus 1 --duration 10s
```

- **VUs (Virtual Users)**: 1 个虚拟用户
- **Duration**: 持续 10 秒

### 其他常用配置

```bash
# 负载测试
--vus 10 --duration 1m

# 峰值测试
--vus 100 --duration 30s

# 阶梯式压力测试（需要修改脚本）
# stages: [
#   { duration: '1m', target: 10 },
#   { duration: '2m', target: 50 },
#   { duration: '1m', target: 0 },
# ]
```

## 🔧 自定义配置

### 修改 Postman 集合

编辑 `postman/collection.json` 文件，或：

1. 在 Postman 中修改集合
2. 导出为 Collection v2.1 格式
3. 替换 `postman/collection.json`

### 修改测试参数

编辑 `.github/workflows/test.yml` 中的 `flags` 参数：

```yaml
flags: --vus 10 --duration 1m --out json=results.json --summary-export=summary.json
```

### 添加环境变量

如果 Postman 集合使用了环境变量，可以这样转换：

```bash
postman-to-k6 postman/collection.json \
  -e postman/environment.json \
  -o k6-script.js
```

## 📚 相关资源

- [k6 官方文档](https://k6.io/docs/)
- [postman-to-k6 工具](https://github.com/apideck-libraries/postman-to-k6)
- [JSONPlaceholder API](https://jsonplaceholder.typicode.com/)
- [Grafana k6 GitHub Actions](https://github.com/grafana/k6-action)

## 📝 License

MIT License
