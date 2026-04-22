<!-- ============================================================ -->
<!--  FORK NOTICE — 中国 A 股 / 同花顺 iFinD 本地化版本             -->
<!-- ============================================================ -->

> ## 🇨🇳 This is a fork — Chinese A-share / iFinD edition
>
> **EN:** This repository is a fork of [`anthropics/financial-services-plugins`](https://github.com/anthropics/financial-services-plugins), modified to replace the original 11 foreign data MCPs (Daloopa, FactSet, Morningstar, S&P/Kensho, Moody's, MT Newswires, Aiera, LSEG, PitchBook, Chronograph, Egnyte) with **4 Hexin iFinD MCPs** (同花顺) for Chinese stock, fund, macro, and news data. **Use this fork only if you analyze Chinese markets (A-shares, HK, etc.).** For US/global markets, please use the upstream repo.
>
> **中文：** 本仓库 fork 自 [`anthropics/financial-services-plugins`](https://github.com/anthropics/financial-services-plugins)，将原版 11 个海外数据 MCP（Daloopa、FactSet、Morningstar、S&P/Kensho、Moody's、MT Newswires、Aiera、LSEG、PitchBook、Chronograph、Egnyte）替换为**同花顺 iFinD 的 4 个 MCP**（个股 / 基金 / 宏观 / 新闻公告）。**仅适用于中国市场（A 股、港股等）分析**；如需海外市场，请使用上游原版仓库。
>
> **What changed / 改动概览**
> - `financial-analysis/.mcp.json` → 11 foreign MCPs replaced with 4 `hexin-ifind-ds-*` MCPs
> - 11 SKILL.md files in `financial-analysis/` and `equity-research/` → CN data-sourcing rule injected
> - `plugin.json` versions bumped to `0.1.1-cn`
> - Setup guide: see [`docs/IFIND-SETUP.md`](./docs/IFIND-SETUP.md) (bilingual)
> - Full change log: see [`CHANGES-CN.md`](./CHANGES-CN.md) (bilingual)
>
> **Upstream sync policy / 上游同步策略**
> - This fork tracks upstream `main` and is manually rebased when notable upstream changes land. No automatic sync.
> - 本 fork 跟踪上游 `main` 分支，遇到上游重要更新时手动 rebase，不做自动同步。
>
> **License / 许可证：** Original work © Anthropic, Apache-2.0. Modifications © 2026 NBreeze-Eric (`256726122+NBreeze-Eric@users.noreply.github.com`), released under the same Apache-2.0 license. See [`LICENSE`](./LICENSE) and [`NOTICE`](./NOTICE).

<!-- ============================================================ -->
<!--  🚀 5-MINUTE QUICKSTART (for non-developers)                 -->
<!--  🚀 5 分钟上手（给小白）                                      -->
<!-- ============================================================ -->

# 🚀 5 分钟上手 / 5-Minute Quickstart

> 没用过 GitHub？没写过代码？完全 OK，跟着下面 4 步走就能用。
> Never used GitHub? Never written code? It's fine — just follow the 4 steps below.

## 这是什么 / What is this

**中文：** 一套给中国 A 股 / 港股分析师用的 AI 助手"插件"。装好后，你只用一句中文（"帮我估一下贵州茅台"），AI 就会自动从同花顺 iFinD 拉数据、建 Excel 模型、写研报。

**EN:** An AI-assistant "plugin" pack for Chinese A-share / HK equity analysts. Once installed, you can ask in plain Chinese ("estimate Kweichow Moutai for me") and the AI auto-pulls data from Hexin iFinD, builds Excel models, and drafts research reports.

---

## 你能用它做什么 / What you can do

下面是真实跑出来的输出文件（点击下载查看效果），你最终生成的文件应当是类似的水平：

These are real outputs (click to download and inspect), your generated files should be at a similar level:

| 想做什么 / Use case | 一句话指令 / One-line command | 输出 / Output |
|---|---|---|
| **可比公司分析** / Comps analysis | `/financial-analysis:comps 600519`<br>("帮我做贵州茅台的可比公司分析") | [`examples/600519_Comps_Analysis.xlsx`](./examples/600519_Comps_Analysis.xlsx) — 自动建好的 Excel，含倍数、统计、benchmarks |
| **DCF 估值模型** / DCF valuation | `/financial-analysis:dcf 600519`<br>("帮我给贵州茅台做 DCF 估值") | [`examples/600519_DCF_Model.xlsx`](./examples/600519_DCF_Model.xlsx) — 现金流预测 + WACC + 敏感性分析 |
| **业绩点评研报** / Earnings update | `/equity-research:earnings 000001`<br>("分析平安银行最新财报") | [`examples/PingAn_Bank_FY2025_Earnings_Update.docx`](./examples/PingAn_Bank_FY2025_Earnings_Update.docx) — 8-12 页的 Word 研报 |

> 还可以做：行业研报、初次覆盖报告、催化剂日历、晨会纪要、PPT 改稿、Excel 数据清洗、3 张报表建模、LBO 模型……
> Also: sector reports, initiating coverage, catalyst calendar, morning notes, PPT refresh, data cleaning, 3-statement modeling, LBO models, ...

---

## 安装 4 步 / Install in 4 steps

### Step 1 — 装一个 AI Agent 终端 / Install an AI agent terminal

任选其一 / Pick one:
- **Claude Code**（Anthropic 官方）
- **OpenClaw**（开源替代品）

> 自行搜索安装方式即可；本仓库的所有命令在两个终端里都能跑。
> Look up the install method on your own; all commands in this repo work in both terminals.

### Step 2 — 申请同花顺 iFinD 账号并拿 token / Get an iFinD account & token

1. 去官网申请账号 / Sign up at: **https://www.51ifind.com**
2. 登录后 → 终端 → **设置 → WCP 启用** → 复制 bearer token
3. After login → terminal → **Settings → Enable WCP** → copy the bearer token

> ⚠️ Token 长得像一长串乱码 (eyJhbGc...)。**像密码一样保存**，绝不能发给别人或上传到 GitHub。
> ⚠️ The token looks like a long random string. **Treat it as a password** — never share or upload it.

### Step 3 — 配置项目设置 / Configure project settings

在你的项目目录里新建文件 `.claude/settings.json`，粘贴：
Create `.claude/settings.json` in your project directory and paste:

```json
{
  "enabledPlugins": {
    "financial-analysis@financial-services-plugins": true,
    "equity-research@financial-services-plugins": true
  },
  "extraKnownMarketplaces": {
    "financial-services-plugins": {
      "source": {
        "source": "github",
        "repo": "NBreeze-Eric/financial-services-plugins-cn"
      }
    }
  }
}
```

### Step 4 — 配置 token / Drop the token

在**同一个项目目录**里新建 `.claude/settings.local.json`：
In the **same project directory** create `.claude/settings.local.json`:

```json
{
  "env": {
    "IFIND_AUTH": "把第二步复制的 token 粘到这里 / paste your token here"
  }
}
```

> ✅ `.claude/settings.local.json` 已经在 `.gitignore` 里，永远不会被提交到 GitHub。
> ✅ This file is already in `.gitignore`, so it will never be committed.

---

## 第一次跑 / First run

打开 Claude Code / OpenClaw，依次输入：
Open Claude Code / OpenClaw and type:

```
/plugin marketplace update financial-services-plugins
/reload-plugins
```

期望看到 / Expected output:
```
Reloaded: 2 plugins · 17 skills · 5 agents · 4 plugin MCP servers
```

然后试一条 / Then try:
```
/financial-analysis:dcf 600519
```

如果 5 分钟内输出了一份 Excel，恭喜，安装成功 🎉
If you get an Excel file in ~5 minutes, you're all set 🎉

---

## 出问题怎么办 / Troubleshooting

| 现象 / Symptom | 原因 / Cause | 修复 / Fix |
|---|---|---|
| `/reload-plugins` 显示 11 个 MCP 而不是 4 个 / Shows 11 MCP servers instead of 4 | 缓存陈旧，加载到上游版本 / Stale cache, loading upstream version | 见 [`docs/IFIND-SETUP.md`](./docs/IFIND-SETUP.md) "Troubleshooting" |
| MCP 显示 "needs authentication" 或红叉 / MCP shows "needs authentication" or red X | Token 没配或过期 / Token missing or expired | 重做 Step 2 + Step 4，重启 / Redo Steps 2 & 4, restart |
| 命令跑了但说找不到数据 / Command runs but data lookup fails | iFinD 配额用完 / iFinD quota exhausted | 登 iFinD 终端看配额 / Check quota in iFinD terminal |

---

## 让 AI 带你装 / Get AI to walk you through it

如果上面看不懂，把下面这段**整段复制**给任何 AI 助手（Claude / ChatGPT / 通义千问 …），它会一步步带你装：

If the above is unclear, copy the prompt below into any AI assistant (Claude / ChatGPT / Qwen / etc.) and it will walk you through:

```
我想在我的电脑上安装并使用 https://github.com/NBreeze-Eric/financial-services-plugins-cn
这个仓库。我已经申请了同花顺 iFinD 账号、拿到 bearer token。
我没用过 Git/GitHub/Claude Code，请一步步带我装：每一步告诉我在哪里输入命令、
正确的输出应该是什么样的、出错了怎么办。我的操作系统是 Windows / macOS / Linux（请改成你自己的）。
```

---

<!-- ============================================================ -->
<!--  📚 For developers / 给开发者：原 README 文档在下方           -->
<!--  Original upstream README follows ↓↓↓                         -->
<!-- ============================================================ -->

# Claude for Financial Services Plugins

Plugins that turn Claude into a specialist for financial services — investment banking, equity research, private equity, and wealth management. Built for [Claude Cowork](https://claude.com/product/cowork), also compatible with [Claude Code](https://claude.com/product/claude-code).

## Why Plugins

Cowork lets you set the goal and Claude delivers finished, professional work. Plugins let you go further: tell Claude how your firm does analysis, which data sources to pull from, how to handle critical workflows, and what slash commands to expose — so your team gets better and more consistent outcomes.

Each plugin bundles the skills, connectors, slash commands, and sub-agents for a specific financial services workflow. Out of the box, they give Claude a strong starting point for helping anyone in that role. The real power comes when you customize them for your firm — your models, your templates, your processes — so Claude works like it was built for your team.

## What is Claude for Financial Services?

Claude for Financial Services is a comprehensive solution built on Claude for Enterprise with specialized capabilities for financial analysis. It connects Claude to the data sources and tools financial professionals use daily — eliminating the need to juggle multiple browser tabs and improving source verification to reduce the risk of errors from manual data gathering.

## End-to-End Workflows

These plugins aren't just a collection of point tools — they enable complete workflows that span research, analysis, modeling, and output creation:

- **Research to Report**: Pull real-time data from MCP providers, analyze earnings results, and generate publication-ready equity research reports — all in a single session
- **Spreadsheet Analysis**: Build comparable company analyses, DCF models, and LBO models as fully functional Excel workbooks with live formulas, sensitivity tables, and industry-standard formatting
- **Financial Modeling**: Populate 3-statement models from SEC filings, cross-check assumptions against peer data, and stress-test scenarios — with blue/black/green color coding conventions built in
- **Deal Materials**: Draft CIMs, teasers, and process letters, then generate pitch deck slides and strip profiles using your firm's branded PowerPoint templates
- **Portfolio to Presentation**: Screen opportunities, run diligence checklists, build IC memos, and track portfolio KPIs — moving seamlessly from data to deliverable

Each workflow connects upstream data sources (via MCP) to downstream outputs (Excel, PowerPoint, Word), so you move from question to finished work product without context-switching.

## Plugin Marketplace

Start with **financial analysis** — the core plugin that provides shared modeling tools and all MCP data connectors. Then add any function-specific plugins to enhance Claude's capabilities for your workflow.

| Plugin | Type | How it helps | Connectors |
|--------|------|-------------|------------|
| **[financial analysis](./financial-analysis)** | Core (install first) | Build comps, DCF models, LBO models, and 3-statement financials. QC presentations and create reusable PPT templates. Provides the shared foundation and all data connectors. | Daloopa, Morningstar, S&P Global, FactSet, Moody's, MT Newswires, Aiera, LSEG, PitchBook, Chronograph, Egnyte |
| **[investment banking](./investment-banking)** | Add-on | Draft CIMs, teasers, and process letters. Build buyer lists, run merger models, create strip profiles, and track live deals through milestones. | — |
| **[equity research](./equity-research)** | Add-on | Write earnings updates and initiating coverage reports. Maintain investment theses, track catalysts, draft morning notes, and screen for new ideas. | — |
| **[private equity](./private-equity)** | Add-on | Source and screen deals, run due diligence checklists, analyze unit economics and returns, draft IC memos, and monitor portfolio company KPIs. | — |
| **[wealth management](./wealth-management)** | Add-on | Prep for client meetings, build financial plans, rebalance portfolios, generate client reports, and identify tax-loss harvesting opportunities. | — |

**41 skills, 38 commands, 11 MCP integrations**

Install these directly from Cowork, browse the full collection here on GitHub, or build your own.

### Partner-Built Plugins

These plugins are built and maintained by our data partners, bringing their financial data and analytics directly into Claude workflows.

| Plugin | Partner | How it helps |
|--------|---------|-------------|
| **[LSEG](./partner-built/lseg)** | [LSEG](https://www.lseg.com/) | Price bonds, analyze yield curves, evaluate FX carry trades, value options, and build macro dashboards using LSEG financial data and analytics. 8 commands covering fixed income, FX, equities, and macro. |
| **[S&P Global](./partner-built/spglobal)** | [S&P Global](https://www.spglobal.com/) | Generate company tearsheets, earnings previews, and funding digests powered by S&P Capital IQ data. Supports multiple audience types (equity research, IB/M&A, corp dev, sales). |

### Office Add-in Deployment

For Microsoft 365 admins deploying the [Claude Office add-in](https://pivot.claude.ai) with direct cloud access to your own Vertex AI, Bedrock, or LLM gateway.

| Plugin | How it helps |
|--------|-------------|
| **[claude-in-office](./claude-in-office)** | Interactive setup wizard that provisions cloud resources, generates the customized add-in manifest, walks through Azure admin consent, and writes per-user config via Microsoft Graph extension attributes. |

```bash
claude plugin install claude-in-office@financial-services-plugins
/claude-in-office:setup
```

## Getting Started

### Cowork

Install plugins from [claude.com/plugins](https://claude.com/plugins/).

### Claude Code

```bash
# Add the marketplace
claude plugin marketplace add anthropics/financial-services-plugins

# Install the core plugin first (required)
claude plugin install financial-analysis@financial-services-plugins

# Then add function-specific plugins as needed
claude plugin install investment-banking@financial-services-plugins
claude plugin install equity-research@financial-services-plugins
claude plugin install private-equity@financial-services-plugins
claude plugin install wealth-management@financial-services-plugins
```

Once installed, plugins activate automatically. Skills fire when relevant, and slash commands are available in your session:

```bash
/comps [company]                # Comparable company analysis
/dcf [company]                  # DCF valuation model
/earnings [company] [quarter]   # Post-earnings update report
/one-pager [company]            # One-page company profile
/ic-memo [project name]         # Investment committee memo
/source [criteria]              # Deal sourcing
/client-review [client]         # Client meeting prep
```

## How Plugins Work

Every plugin follows the same structure:

```
plugin-name/
├── .claude-plugin/plugin.json   # Manifest
├── .mcp.json                    # Tool connections
├── commands/                    # Slash commands you invoke explicitly
└── skills/                      # Domain knowledge Claude draws on automatically
```

- **Skills** encode the domain expertise, best practices, and step-by-step workflows Claude needs to deliver professional-quality financial work. Claude draws on them automatically when relevant.
- **Commands** are explicit actions you trigger (e.g., `/comps`, `/earnings`, `/ic-memo`).
- **Connectors** wire Claude to the external data sources your workflow depends on — financial data terminals, research platforms, document management, and more — via [MCP servers](https://modelcontextprotocol.io/).

Every component is file-based — markdown and JSON, no code, no infrastructure, no build steps.

## MCP Integrations

All connectors are centralized in the **financial analysis** core plugin and shared across all add-on plugins.

| Provider | URL |
|----------|-----|
| [Daloopa](https://www.daloopa.com/) | `https://mcp.daloopa.com/server/mcp` |
| [Morningstar](https://www.morningstar.com/) | `https://mcp.morningstar.com/mcp` |
| [S&P Global](https://www.spglobal.com/) | `https://kfinance.kensho.com/integrations/mcp` |
| [FactSet](https://www.factset.com/) | `https://mcp.factset.com/mcp` |
| [Moody's](https://www.moodys.com/) | `https://api.moodys.com/genai-ready-data/m1/mcp` |
| [MT Newswires](https://www.mtnewswires.com/) | `https://vast-mcp.blueskyapi.com/mtnewswires` |
| [Aiera](https://www.aiera.com/) | `https://mcp-pub.aiera.com` |
| [LSEG](https://www.lseg.com/) | `https://api.analytics.lseg.com/lfa/mcp` |
| [PitchBook](https://pitchbook.com/) | `https://premium.mcp.pitchbook.com/mcp` |
| [Chronograph](https://www.chronograph.pe/) | `https://ai.chronograph.pe/mcp` |
| [Egnyte](https://www.egnyte.com/) | `https://mcp-server.egnyte.com/mcp` |

> MCP access may require a subscription or API key from the respective provider.

## Making Them Yours

These plugins are starting points. They become much more useful when you customize them for how your firm actually works:

- **Swap connectors** — Edit `.mcp.json` to point at your specific data providers and internal tools.
- **Add firm context** — Drop your terminology, deal processes, and formatting standards into skill files so Claude understands your world.
- **Bring your templates** — Use `/ppt-template` to teach Claude your firm's branded PowerPoint layouts, so every deck matches your style guide.
- **Adjust workflows** — Modify skill instructions to match how your team actually does analysis, not how a textbook says to.
- **Build new plugins** — Follow the structure above to create plugins for workflows we haven't covered yet.

As your team builds and shares plugins, Claude becomes a cross-functional expert. The context you define gets baked into every relevant interaction, so leaders can spend less time enforcing processes and more time improving them.

## Contributing

Plugins are just markdown files. Fork the repo, make your changes, and submit a PR. For new skills or plugins, include:

- A `SKILL.md` with clear trigger conditions and workflow steps
- A corresponding command in `commands/` if user-invocable
- Updated plugin manifest if adding new capabilities

## License

[Apache License 2.0](./LICENSE)

## Disclaimer

These plugins assist with financial workflows but do not provide financial or investing advice. Always verify conclusions with qualified financial professionals. AI-generated analysis should be reviewed by financial professionals before being relied upon for financial or investment decisions.
