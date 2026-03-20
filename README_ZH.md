[English](README.md) | 中文

# 📄 Google Docs Operator

> **OpenClaw 专属的 Google Docs 无缝集成方案**
> 
> *通过 Maton 托管 OAuth 创建、读取、编辑和导出 Google Docs—无需本地 Chrome 或复杂配置。*

[![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)](https://github.com/tankeito/google_docs_operator/releases)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![OpenClaw](https://img.shields.io/badge/OpenClaw-Skill-green.svg)](https://openclaw.ai)

---

## 🌟 项目概述

**Google Docs Operator** 是一款生产就绪的 OpenClaw 技能，通过 **Maton API 网关** 提供与 Google Docs 的无缝集成。告别复杂的 OAuth 配置、本地 Chrome 驱动和凭证管理—只需连接一次 Google 账户，即可开始自动化处理文档。

### 功能特性

| 功能 | 说明 |
|------|------|
| 📝 **文档创建** | 以编程方式创建带有自定义标题的新 Google Doc |
| 📖 **内容读取** | 提取完整文档文本，保留段落结构 |
| ✏️ **内容编辑** | 在文档任意位置追加、覆盖或搜索替换文本 |
| 🎨 **文本格式化** | 应用粗体、斜体、标题（H1-H6）和段落样式 |
| 📤 **文档导出** | 通过 Google Drive API 导出为 PDF、DOCX（Word）或 TXT 格式 |
| 🔗 **多账户支持** | 管理多个 Google 账户连接，轻松切换 |

### 为什么选择 Google Docs Operator？

- **🔐 托管 OAuth**：无需处理 OAuth 令牌或服务账户—Maton 安全地处理认证
- **⚡ 零配置**：通过简单的授权 URL，几分钟内连接 Google 账户
- **🌐 基于 REST API**：使用官方 Google Docs API v1，功能完全对等
- **🛠️ 附带 CLI 工具**：常用任务的快速命令行操作
- **📦 OpenClaw 原生**：专为 OpenClaw AI 助手集成设计

---

## ⚠️ 前置要求

使用此技能前，请确保具备：

1. **Maton API Key** — 所有操作的必备条件
2. **Google 账户** — 必须通过 Maton 的 OAuth 流程授权
3. **网络访问** — 所有请求通过 Maton 网关
4. **Python 3.8+** — 用于 CLI 工具（OpenClaw 集成为可选）

---

## 🚀 快速开始

### 步骤 1：获取 Maton API Key

1. 在 [maton.ai](https://maton.ai) 注册或登录账户
2. 导航至 [设置页面](https://maton.ai/settings)
3. 复制您的 API Key

```bash
export MATON_API_KEY="your_api_key_here"
```

### 步骤 2：安装依赖

```bash
# 进入技能目录
cd ~/.openclaw/workspace/skills/google_docs_operator

# 安装 Python 依赖
pip install -r requirements.txt
```

### 步骤 3：创建 Google OAuth 连接

```bash
# 列出已有连接
python3 gdocs_driver.py connections list

# 创建新连接（打开授权 URL）
python3 gdocs_driver.py connections create
```

**授权流程**：
1. 运行 `connections create` 命令
2. 从输出中复制授权 URL
3. 在浏览器中打开该 URL
4. 使用 Google 账户登录并授予权限
5. 返回技能—您的连接现已激活！

### 步骤 4：测试配置

```bash
# 创建测试文档
python3 gdocs_driver.py create --title "我的第一个文档"

# 读取文档（将 DOCUMENT_ID 替换为 create 命令的输出）
python3 gdocs_driver.py get --id DOCUMENT_ID
```

---

## 📚 命令参考

### 核心命令（OpenClaw 集成）

| 命令 | 说明 | 示例 |
|------|------|------|
| `/gdocs create` | 创建新 Google Doc | `/gdocs create "会议记录"` |
| `/gdocs read` | 读取文档内容 | `/gdocs read DOCUMENT_ID` |
| `/gdocs write` | 写入或追加内容 | `/gdocs write DOCUMENT_ID "新内容"` |
| `/gdocs export` | 导出文档为 PDF/DOCX | `/gdocs export DOCUMENT_ID pdf` |

### CLI 命令（直接使用）

| 命令 | 说明 | 示例 |
|------|------|------|
| `get` | 读取文档内容 | `python3 gdocs_driver.py get --id DOC_ID` |
| `create` | 创建新文档 | `python3 gdocs_driver.py create --title "文档标题"` |
| `append` | 追加文本到末尾 | `python3 gdocs_driver.py append --id DOC_ID --content "文本"` |
| `write` | 覆盖整个文档 | `python3 gdocs_driver.py write --id DOC_ID --content "新文本"` |
| `replace` | 搜索并替换 | `python3 gdocs_driver.py replace --id DOC_ID --find "旧词" --replace "新词"` |
| `heading` | 应用标题样式 | `python3 gdocs_driver.py heading --id DOC_ID --text "标题" --level 1` |
| `bold` | 应用粗体格式 | `python3 gdocs_driver.py bold --id DOC_ID --text "重要"` |
| `export` | 导出为 PDF/DOCX/TXT | `python3 gdocs_driver.py export --id DOC_ID --format pdf --output out.pdf` |

---

## 🔧 API 参考

### 基础 URL

所有 API 请求通过 Maton 网关：

```
https://gateway.maton.ai/google-docs/v1/documents
```

网关将请求代理到 `docs.googleapis.com` 并自动注入您的 OAuth 令牌。

### 认证

所有请求需要在 Authorization 头中包含 Maton API Key：

```http
Authorization: Bearer $MATON_API_KEY
```

### 获取文档

```http
GET /google-docs/v1/documents/{documentId}
```

**Python 示例**：
```python
import os, json, urllib.request

def get_doc_text(doc_id):
    req = urllib.request.Request(
        f'https://gateway.maton.ai/google-docs/v1/documents/{doc_id}')
    req.add_header('Authorization', f'Bearer {os.environ["MATON_API_KEY"]}')
    doc = json.load(urllib.request.urlopen(req))
    
    # 从文档结构中提取纯文本
    text = ''.join(
        pe.get('textRun', {}).get('content', '')
        for elem in doc.get('body', {}).get('content', [])
        for pe in elem.get('paragraph', {}).get('elements', [])
    )
    return {'title': doc['title'], 'content': text, 'documentId': doc['documentId']}
```

### 创建文档

```http
POST /google-docs/v1/documents
Content-Type: application/json

{"title": "My New Document"}
```

**Python 示例**：
```python
import os, json, urllib.request

data = json.dumps({'title': 'My New Document'}).encode()
req = urllib.request.Request(
    'https://gateway.maton.ai/google-docs/v1/documents',
    data=data, method='POST')
req.add_header('Authorization', f'Bearer {os.environ["MATON_API_KEY"]}')
req.add_header('Content-Type', 'application/json')
doc = json.load(urllib.request.urlopen(req))
print(f"已创建：https://docs.google.com/document/d/{doc['documentId']}/edit")
```

### 批量更新（写入、格式化、替换）

所有内容修改使用 `batchUpdate`：

```http
POST /google-docs/v1/documents/{documentId}:batchUpdate
Content-Type: application/json

{"requests": [...]}
```

---

## 🎨 常用操作

### 📝 追加文本（到文档末尾）

```python
def append_text(doc_id, text):
    # 获取文档末尾索引
    req = urllib.request.Request(
        f'https://gateway.maton.ai/google-docs/v1/documents/{doc_id}')
    req.add_header('Authorization', f'Bearer {os.environ["MATON_API_KEY"]}')
    doc = json.load(urllib.request.urlopen(req))
    content = doc.get('body', {}).get('content', [])
    end_index = content[-1].get('endIndex', 1) - 1 if content else 1

    # 在末尾插入文本
    body = json.dumps({'requests': [{'insertText': {
        'location': {'index': end_index},
        'text': text
    }}]}).encode()
    req2 = urllib.request.Request(
        f'https://gateway.maton.ai/google-docs/v1/documents/{doc_id}:batchUpdate',
        data=body, method='POST')
    req2.add_header('Authorization', f'Bearer {os.environ["MATON_API_KEY"]}')
    req2.add_header('Content-Type', 'application/json')
    urllib.request.urlopen(req2)
    print(f'已追加 {len(text)} 个字符')
```

### 🔍 搜索并替换全部

```python
def replace_all(doc_id, find, replace):
    body = json.dumps({'requests': [{'replaceAllText': {
        'containsText': {'text': find, 'matchCase': True},
        'replaceText': replace
    }}]}).encode()
    req = urllib.request.Request(
        f'https://gateway.maton.ai/google-docs/v1/documents/{doc_id}:batchUpdate',
        data=body, method='POST')
    req.add_header('Authorization', f'Bearer {os.environ["MATON_API_KEY"]}')
    req.add_header('Content-Type', 'application/json')
    result = json.load(urllib.request.urlopen(req))
    n = result['replies'][0].get('replaceAllText', {}).get('occurrencesChanged', 0)
    print(f'已替换 {n} 处')
```

### 📑 应用标题样式

命名样式：`NORMAL_TEXT` | `HEADING_1` ~ `HEADING_6` | `TITLE` | `SUBTITLE`

```python
def apply_heading(doc_id, start_idx, end_idx, level):
    style_map = {1: "HEADING_1", 2: "HEADING_2", 3: "HEADING_3"}
    body = json.dumps({'requests': [{
        'updateParagraphStyle': {
            'range': {'startIndex': start_idx, 'endIndex': end_idx},
            'paragraphStyle': {'namedStyleType': style_map.get(level, 'HEADING_1')},
            'fields': 'namedStyleType'
        }
    }]}).encode()
    req = urllib.request.Request(
        f'https://gateway.maton.ai/google-docs/v1/documents/{doc_id}:batchUpdate',
        data=body, method='POST')
    req.add_header('Authorization', f'Bearer {os.environ["MATON_API_KEY"]}')
    req.add_header('Content-Type', 'application/json')
    urllib.request.urlopen(req)
    print(f'已应用标题 {level}')
```

### ✨ 应用文本格式化（粗体/斜体）

```python
def apply_bold(doc_id, start_idx, end_idx):
    body = json.dumps({'requests': [{
        'updateTextStyle': {
            'range': {'startIndex': start_idx, 'endIndex': end_idx},
            'textStyle': {'bold': True},
            'fields': 'bold'
        }
    }]}).encode()
    req = urllib.request.Request(
        f'https://gateway.maton.ai/google-docs/v1/documents/{doc_id}:batchUpdate',
        data=body, method='POST')
    req.add_header('Authorization', f'Bearer {os.environ["MATON_API_KEY"]}')
    req.add_header('Content-Type', 'application/json')
    urllib.request.urlopen(req)
    print('已应用粗体格式')
```

### 📤 导出为 PDF / DOCX

导出通过 Google Drive API（也由 Maton 代理）：

```python
def export_doc(doc_id, mime_type, output_path):
    import urllib.parse
    url = (f'https://gateway.maton.ai/google-drive/drive/v3/files/{doc_id}/export'
           f'?mimeType={urllib.parse.quote(mime_type)}')
    req = urllib.request.Request(url)
    req.add_header('Authorization', f'Bearer {os.environ["MATON_API_KEY"]}')
    with urllib.request.urlopen(req) as resp:
        with open(output_path, 'wb') as f:
            f.write(resp.read())
    print(f'已导出到 {output_path}')

# 导出为 PDF
export_doc('YOUR_DOC_ID', 'application/pdf', '/tmp/document.pdf')

# 导出为 Word
export_doc('YOUR_DOC_ID',
           'application/vnd.openxmlformats-officedocument.wordprocessingml.document',
           '/tmp/document.docx')
```

---

## 🔗 连接管理

在 `https://ctrl.maton.ai` 管理您的 Google OAuth 连接。

### 列出活动连接

```bash
python3 gdocs_driver.py connections list
```

### 创建新连接

```bash
python3 gdocs_driver.py connections create
```

输出将包含授权 URL—在浏览器中打开以完成 OAuth 流程。

### 使用多账户

如果您连接了多个 Google 账户，可指定使用哪一个：

```python
req.add_header('Maton-Connection', 'YOUR_CONNECTION_ID')
```

---

## 📋 环境变量

| 变量名 | 必填 | 说明 | 示例值 |
|--------|------|------|--------|
| `MATON_API_KEY` | ✅ 是 | 从 maton.ai/settings 获取的 Maton API Key | `your_api_key_here` |

---

## 📦 项目结构

```
google_docs_operator/
├── gdocs_driver.py           # 常用操作的 CLI 工具
├── SKILL.md                  # OpenClaw 技能定义
├── README.md                 # 英文文档
├── README_ZH.md              # 中文文档（本文件）
├── LICENSE                   # MIT 许可证
├── requirements.txt          # Python 依赖
└── .gitignore                # Git 忽略规则
```

---

## ❓ 常见问题

### Q: 如何从 URL 中提取文档 ID？

**A**: 从 Google Docs URL 如：
```
https://docs.google.com/document/d/1a2b3c4d5e6f7g8h9i0j/edit
```
文档 ID 为：`1a2b3c4d5e6f7g8h9i0j`（`/d/` 和 `/edit` 之间的字符串）

### Q: 可以与 Google Workspace 账户一起使用吗？

**A**: 可以！Maton 网关支持个人 Gmail 和 Google Workspace 账户。

### Q: 如果 OAuth 令牌过期会发生什么？

**A**: Maton 自动处理令牌刷新。如果连接失效，您将收到 401 错误—只需重新创建连接即可。

### Q: 有速率限制吗？

**A**: Google Docs API 对每个账户强制执行 10 请求/秒 的限制。Maton 也可能根据您的计划应用速率限制。

---

## ⚠️ 错误处理

| 状态码 | 含义 | 解决方案 |
|--------|------|----------|
| 400 | 缺少连接或请求格式错误 | 检查文档 ID 和请求体 |
| 401 | Maton API Key 无效或缺失 | 验证 `MATON_API_KEY` 环境变量 |
| 403 | 权限不足 | 确保 OAuth 连接有文档访问权限 |
| 429 | 速率限制 | 等待后重试（10 请求/秒限制） |
| 4xx/5xx | Google API 错误 | 检查 Google Docs API 状态 |

---

## 🔐 安全与隐私

- **OAuth 令牌由 Maton 管理**—永不本地存储
- **所有请求通过 HTTPS 加密**
- **无需本地 Chrome 或浏览器自动化**
- **`.gitignore` 保护敏感文件**—永不提交 API Key

**最佳实践**：
1. 永不将 `MATON_API_KEY` 提交到版本控制
2. 使用环境变量或安全密钥管理
3. 通过 Maton 仪表板定期轮换 API Key
4. 在 `ctrl.maton.ai` 审计连接访问

---

## 🔄 版本历史

### v1.0.0 — 2026-03-16

- 🎉 **初始发布**：通过 Maton 网关完整集成 Google Docs API
- 📝 **核心操作**：创建、读取、追加、写入、搜索替换
- 🎨 **格式化支持**：标题（H1-H6）、粗体、斜体、段落样式
- 📤 **导出功能**：通过 Google Drive API 导出 PDF、DOCX、TXT
- 🛠️ **CLI 工具**：`gdocs_driver.py` 用于快速命令行操作
- 🔗 **多账户**：支持多个 Google 账户连接
- 📚 **文档**：完整的中英文 README

---

## 📄 许可证

MIT License – 详见 [LICENSE](LICENSE) 文件。

---

## 📞 支持与资源

- **GitHub Issues**: https://github.com/tankeito/google_docs_operator/issues
- **Maton 文档**: https://maton.ai/docs
- **Google Docs API**: https://developers.google.com/workspace/docs/api
- **Maton 社区**: https://discord.com/invite/dBfFAcefs2
- **Maton 支持**: support@maton.ai

---

## 🙏 致谢

- **Maton** – 提供 OAuth 托管的 API 网关
- **Google Workspace** – 提供强大的 Docs API
- **OpenClaw** – 提供 AI 助手平台
