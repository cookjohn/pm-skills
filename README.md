# PubMed Skills for Claude Code

[English](#english) | [中文](#中文)

| WeChat Official Account (公众号) | WeChat Group (微信群) | Discord |
|:---:|:---:|:---:|
| <img src="qrcode_for_gh_a1c14419b847_258.jpg" width="200"> | <img src="0317.jpg" width="200"> | [Join Discord](https://discord.gg/tGd5vTDASg) |
| 未来论文实验室 | 扫码加入交流群 | English & Chinese |

---

<a id="english"></a>

## English

[Claude Code](https://docs.anthropic.com/en/docs/claude-code) skills that let Claude interact with [PubMed](https://pubmed.ncbi.nlm.nih.gov/) through Chrome DevTools MCP.

Search papers, view detailed metadata, access full text, and export to Zotero with PDF attachments — all from the Claude Code CLI.

### Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI installed
- Chrome browser
- [Zotero](https://www.zotero.org/) desktop app (optional, for export)
- Python 3 (optional, for Zotero push script)

### Skills

| Skill | Description | Invocation |
|-------|-------------|------------|
| `pm-search` | Keyword search via PubMed API with structured results | `/pm-search gastric cancer` |
| `pm-advanced-search` | Filtered search: author, journal, date, MeSH terms, article type | `/pm-advanced-search Nature 2020-2025 review` |
| `pm-paper-detail` | Full paper metadata (abstract, keywords, MeSH, references) | `/pm-paper-detail 38547890` |
| `pm-fulltext` | Full-text access links: PMC, DOI, Sci-Hub, publisher | `/pm-fulltext 38547890` |
| `pm-navigate-pages` | Search result pagination | `/pm-navigate-pages next` |
| `pm-export` | Export to Zotero with PDF attachment (3-step flow) | `/pm-export 38547890` |

### Agent

**`pm-researcher`** — orchestrates all 6 skills. Handles PubMed API interaction, tab management, and multi-step workflows like "search → filter → export to Zotero".

### Installation

#### 1. Install Chrome DevTools MCP server

```bash
claude mcp add chrome-devtools -- npx -y chrome-devtools-mcp@latest
```

#### 2. Install PubMed skills

```bash
git clone https://github.com/cookjohn/pm-skills.git
cd pm-skills
cp -r skills/ agents/ .claude/
```

Or add to an existing project:

```bash
git clone https://github.com/cookjohn/pm-skills.git /tmp/pm-skills
cp -r /tmp/pm-skills/skills/ your-project/.claude/skills/
cp -r /tmp/pm-skills/agents/ your-project/.claude/agents/
```

#### 3. Start Chrome with remote debugging

```bash
# Windows
"C:\Program Files\Google\Chrome\Application\chrome.exe" --remote-debugging-port=9222

# macOS
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --remote-debugging-port=9222

# Linux
google-chrome --remote-debugging-port=9222
```

#### 4. Launch Claude Code

```bash
claude
```

Skills and agent are picked up automatically. Try `/pm-search CRISPR` to verify.

### How It Works

All skills use PubMed's E-utilities API (`esearch`, `esummary`, `efetch`) via `evaluate_script` calls through Chrome DevTools MCP. The export skill uses a 3-step flow to attach PDFs to Zotero:

1. **saveItems** — push paper metadata to Zotero via Connector API
2. **download PDF** — fetch PDF from PMC/publisher via Python
3. **saveAttachment** — upload PDF binary to Zotero, linked to the parent item

Key design choices:
- **PubMed API over DOM scraping** — structured JSON responses, no fragile selectors
- **Deterministic session IDs** — content-hash based, prevents duplicate imports
- **PDF attachment via saveAttachment** — Zotero 7.x ignores the `attachments` field in saveItems, so PDFs are uploaded separately

### Project Structure

```
skills/
├── pm-search/SKILL.md              # Basic keyword search
├── pm-advanced-search/SKILL.md     # Filtered search (author, journal, date, MeSH)
├── pm-paper-detail/SKILL.md        # Paper metadata extraction
├── pm-fulltext/SKILL.md            # Full-text access links
├── pm-navigate-pages/SKILL.md      # Pagination
└── pm-export/                      # Zotero export with PDF
    ├── SKILL.md
    └── scripts/
        └── push_to_zotero.py       # Zotero Connector API client
agents/
└── pm-researcher.md                # Agent: orchestrates all skills
```

---

<a id="中文"></a>

## 中文

| 公众号 | 微信交流群 | Discord |
|:---:|:---:|:---:|
| <img src="qrcode_for_gh_a1c14419b847_258.jpg" width="200"> | <img src="0317.jpg" width="200"> | [加入 Discord](https://discord.gg/tGd5vTDASg) |
| 未来论文实验室 | 扫码加入交流群 | 中英文交流 |

让 [Claude Code](https://docs.anthropic.com/en/docs/claude-code) 通过 Chrome DevTools MCP 操作 [PubMed](https://pubmed.ncbi.nlm.nih.gov/) 的技能集。

支持论文检索、详细信息查看、全文获取、导出到 Zotero（含 PDF 附件）等功能，全部在 Claude Code 命令行中完成。

### 前置要求

- 已安装 [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- Chrome 浏览器
- [Zotero](https://www.zotero.org/) 桌面端（可选，用于导出）
- Python 3（可选，用于 Zotero 推送脚本）

### 技能列表

| 技能 | 功能 | 调用方式 |
|------|------|----------|
| `pm-search` | 通过 PubMed API 关键词检索，返回结构化结果 | `/pm-search gastric cancer` |
| `pm-advanced-search` | 高级检索：作者、期刊、时间、MeSH 词、文章类型 | `/pm-advanced-search Nature 2020-2025 review` |
| `pm-paper-detail` | 提取论文详细信息（摘要、关键词、MeSH、参考文献） | `/pm-paper-detail 38547890` |
| `pm-fulltext` | 全文获取链接：PMC、DOI、Sci-Hub、出版商 | `/pm-fulltext 38547890` |
| `pm-navigate-pages` | 搜索结果翻页 | `/pm-navigate-pages next` |
| `pm-export` | 导出到 Zotero，含 PDF 附件（三步流程） | `/pm-export 38547890` |

### 智能体

**`pm-researcher`** — 统一调度全部 6 个技能。处理 PubMed API 交互、标签页管理，支持"检索 → 筛选 → 导出到 Zotero"等复合工作流。

### 安装方法

#### 1. 安装 Chrome DevTools MCP 服务器

```bash
claude mcp add chrome-devtools -- npx -y chrome-devtools-mcp@latest
```

#### 2. 安装 PubMed 技能

```bash
git clone https://github.com/cookjohn/pm-skills.git
cd pm-skills
cp -r skills/ agents/ .claude/
```

添加到已有项目：

```bash
git clone https://github.com/cookjohn/pm-skills.git /tmp/pm-skills
cp -r /tmp/pm-skills/skills/ your-project/.claude/skills/
cp -r /tmp/pm-skills/agents/ your-project/.claude/agents/
```

#### 3. 启动 Chrome 远程调试

```bash
# Windows
"C:\Program Files\Google\Chrome\Application\chrome.exe" --remote-debugging-port=9222

# macOS
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --remote-debugging-port=9222

# Linux
google-chrome --remote-debugging-port=9222
```

#### 4. 启动 Claude Code

```bash
claude
```

技能和智能体会自动加载。输入 `/pm-search CRISPR` 验证是否正常。

### 工作原理

所有技能通过 Chrome DevTools MCP 的 `evaluate_script` 调用 PubMed E-utilities API（`esearch`、`esummary`、`efetch`），获取结构化 JSON 数据。导出技能使用三步流程将 PDF 附件添加到 Zotero：

1. **saveItems** — 通过 Connector API 推送论文元数据到 Zotero
2. **下载 PDF** — 通过 Python 从 PMC/出版商获取 PDF
3. **saveAttachment** — 上传 PDF 二进制文件到 Zotero，关联到父条目

核心设计：
- **PubMed API 优于 DOM 抓取** — 结构化 JSON 响应，无脆弱的 CSS 选择器
- **确定性会话 ID** — 基于内容哈希，防止重复导入
- **通过 saveAttachment 附加 PDF** — Zotero 7.x 忽略 saveItems 中的 `attachments` 字段，PDF 需单独上传

---

## License

MIT
