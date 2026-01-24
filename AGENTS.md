# mcpcat-market 数据规范

本文档描述 `mcp_market.json` 的 JSON 格式规范，用于定义 MCP 服务器市场的服务列表。

## 目录

- [顶层结构](#顶层结构)
- [服务对象字段说明](#服务对象字段说明)
- [install 对象](#install-对象)
- [defaults 对象](#defaults-对象)
- [fields 对象](#fields-对象)
- [字段项结构](#字段项结构)
- [添加新服务指南](#添加新服务指南)
- [完整示例](#完整示例)

---

## 顶层结构

```json
{
  "servers": [
    {
      // 服务对象...
    }
  ]
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `servers` | Array | 是 | MCP 服务列表数组 |

---

## 服务对象字段说明

每个服务对象代表一个可安装的 MCP 服务器：

```json
{
  "id": "unique-id",
  "name": "显示名称",
  "description": "功能描述",
  "type": "stdio",
  "install": { ... },
  "defaults": { ... },
  "fields": { ... },
  "tags": ["tag1", "tag2"],
  "homepage": "https://github.com/..."
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `id` | String | 是 | 唯一标识符，建议使用 kebab-case（小写连字符） |
| `name` | String | 是 | 在界面显示的服务名称 |
| `description` | String | 是 | 服务功能描述，支持英文或中文 |
| `type` | String | 是 | 传输类型，当前支持 `stdio` |
| `install` | Object | 是 | 安装命令配置 |
| `defaults` | Object | 是 | 默认配置参数 |
| `fields` | Object | 是 | 用户可配置的字段定义 |
| `tags` | Array | 否 | 标签列表，用于分类和搜索 |
| `homepage` | String | 否 | 项目主页 URL |

---

## install 对象

定义服务的安装命令。

```json
"install": {
  "command": "npx",
  "args": ["-y", "@modelcontextprotocol/server-sequential-thinking"]
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `command` | String | 是 | 执行命令（如 `npx`、`uvx`、`python`） |
| `args` | Array | 是 | 命令参数列表，每个参数为独立字符串 |

---

## defaults 对象

定义服务的默认配置参数。

```json
"defaults": {
  "enabled": true,
  "require_auth": false,
  "timeout": 30,
  "env": {},
  "headers": {}
}
```

| 字段 | 类型 | 必填 | 默认值 | 说明 |
|------|------|------|--------|------|
| `enabled` | Boolean | 否 | `true` | 是否默认启用 |
| `require_auth` | Boolean | 否 | `false` | 是否需要认证 |
| `timeout` | Number | 否 | `30` | 超时时间（秒） |
| `env` | Object | 否 | `{}` | 默认环境变量 |
| `headers` | Object | 否 | `{}` | 默认 HTTP 请求头 |

---

## fields 对象

定义用户在界面中需要填写的配置字段。

```json
"fields": {
  "env": [
    {
      "key": "API_KEY",
      "label": "API 密钥",
      "placeholder": "your_api_key_here"
    }
  ],
  "headers": []
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `env` | Array | 否 | 环境变量配置字段列表 |
| `headers` | Array | 否 | HTTP 请求头配置字段列表 |

如果服务不需要用户输入任何配置，设为空数组 `[]`。

---

## 字段项结构

`env` 和 `headers` 数组中的每个元素：

```json
{
  "key": "DB_HOST",
  "label": "数据库主机",
  "placeholder": "localhost"
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `key` | String | 是 | 配置项的键名，对应环境变量或 HTTP 头名称 |
| `label` | String | 是 | 界面显示的标签名称 |
| `placeholder` | String | 否 | 输入框占位提示文字 |

---

## 添加新服务指南

### 步骤 1：复制模板

在 `servers` 数组末尾添加新服务对象：

```json
{
  "id": "your-server-id",
  "name": "Your Server Name",
  "description": "Description of your MCP server.",
  "type": "stdio",
  "install": {
    "command": "npx",
    "args": ["-y", "your-package-name"]
  },
  "defaults": {
    "enabled": true,
    "require_auth": false,
    "timeout": 30,
    "env": {},
    "headers": {}
  },
  "fields": {
    "env": [],
    "headers": []
  },
  "tags": ["tag1", "tag2"],
  "homepage": "https://github.com/yourname/your-repo"
}
```

### 步骤 2：填写字段

| 字段 | 填写建议 |
|------|----------|
| `id` | 使用 kebab-case，确保唯一性。建议使用包名或仓库名作为基础 |
| `name` | 简洁明了的显示名称，首字母大写 |
| `description` | 一句话说明功能，句末不加句号 |
| `install.command` | 通常为 `npx`、`uvx`、`pip`、`python` 等 |
| `install.args` | 包名和安装参数。如果包支持直接运行，可省略额外参数 |
| `fields.env` | 如果需要 API Key 或连接信息，在此定义 |
| `tags` | 添加 2-4 个相关标签，便于搜索和分类 |
| `homepage` | 项目 GitHub/GitLab 仓库地址 |

### 步骤 3：注意事项

1. **ID 唯一性**：确保 `id` 不与现有服务重复
2. **JSON 格式**：使用标准 JSON 格式，字符串必须用双引号
3. **数组末尾**：新对象添加到 `servers` 数组末尾，注意前一行末尾有逗号
4. **验证 JSON**：提交前使用 `python3 -m json.tool mcp_market.json > /dev/null` 验证格式

### 步骤 4：提交更改

```bash
git add mcp_market.json
git commit -m "Add: new MCP server - your-server-id"
git push
```

---

## 完整示例

### 示例 1：简单服务（无需用户配置）

```json
{
  "id": "time",
  "name": "Time",
  "description": "MCP server providing time-related utilities",
  "type": "stdio",
  "install": {
    "command": "uvx",
    "args": [
      "mcp-server-time",
      "--local-timezone=Asia/Shanghai"
    ]
  },
  "defaults": {
    "enabled": true,
    "require_auth": false,
    "timeout": 30,
    "env": {},
    "headers": {}
  },
  "fields": {
    "env": [],
    "headers": []
  },
  "tags": [
    "time",
    "utilities"
  ],
  "homepage": "https://github.com/modelcontextprotocol/server-time"
}
```

### 示例 2：需要用户配置的服务

```json
{
  "id": "gitlab",
  "name": "GitLab",
  "description": "GitLab MCP server for repository access",
  "type": "stdio",
  "install": {
    "command": "npx",
    "args": [
      "-y",
      "@modelcontextprotocol/server-gitlab"
    ]
  },
  "defaults": {
    "enabled": true,
    "require_auth": false,
    "timeout": 30,
    "env": {},
    "headers": {}
  },
  "fields": {
    "env": [
      {
        "key": "GITLAB_PERSONAL_ACCESS_TOKEN",
        "label": "GitLab Token",
        "placeholder": "your_gitlab_token"
      },
      {
        "key": "GITLAB_API_URL",
        "label": "GitLab API URL",
        "placeholder": "https://gitlab.example.com/api/v4"
      }
    ],
    "headers": []
  },
  "tags": [
    "gitlab",
    "devops"
  ],
  "homepage": "https://github.com/modelcontextprotocol/server-gitlab"
}
```
