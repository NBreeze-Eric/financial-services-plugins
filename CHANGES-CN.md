# Changes in this fork / 本 fork 改动清单

> Bilingual change log for the **Chinese A-share / iFinD edition** of `financial-services-plugins`.
> 中英对照，记录本 fork 相对上游 [`anthropics/financial-services-plugins`](https://github.com/anthropics/financial-services-plugins) 的全部改动。

---

## v0.1.1-cn — 2026-04-22

### EN — Summary

Localize `financial-analysis` and `equity-research` plugins for the Chinese market by replacing all foreign data MCPs with Hexin iFinD (同花顺) MCPs.

### 中文 — 摘要

将 `financial-analysis` 和 `equity-research` 两个插件本地化到中国市场：用同花顺 iFinD 的 4 个 MCP 替换全部海外数据源 MCP。

---

### 1. MCP server replacement / MCP 服务器替换

**File / 文件:** `financial-analysis/.mcp.json`

| Before (upstream) | After (this fork) | Domain / 数据域 |
|---|---|---|
| `daloopa` | — removed — | financial statements |
| `morningstar` | — removed — | equity / fund data |
| `sp-global` | — removed — | market data, multiples |
| `factset` | — removed — | consensus, estimates |
| `moodys` | — removed — | credit data |
| `mtnewswire` | — removed — | financial news |
| `aiera` | — removed — | transcripts |
| `lseg` | — removed — | FX, fixed income |
| `pitchbook` | — removed — | private markets |
| `chronograph` | — removed — | PE portfolio data |
| `egnyte` | — removed — | document storage |
| — | `hexin-ifind-ds-stock-mcp` | A 股 / 港股 / 美股 个股行情、财务、一致预期、估值 |
| — | `hexin-ifind-ds-fund-mcp` | 公募 / 私募基金、ETF、QDII 净值与持仓 |
| — | `hexin-ifind-ds-edb-mcp` | 宏观经济库（GDP、CPI、PMI、利率、行业景气） |
| — | `hexin-ifind-ds-news-mcp` | 财经新闻、上市公司公告、研报、事件驱动 |

Bearer token is supplied via the `${IFIND_AUTH}` placeholder, resolved from `.claude/settings.local.json` (gitignored). See [`docs/IFIND-SETUP.md`](./docs/IFIND-SETUP.md).

通过 `${IFIND_AUTH}` 占位符注入 Bearer token，真实值放在 `.claude/settings.local.json`（已 gitignore）。详见 [`docs/IFIND-SETUP.md`](./docs/IFIND-SETUP.md)。

---

### 2. Skill prompt rewrites / Skill 提示词改写

**Files modified / 修改的文件 (11 total):**

`financial-analysis/skills/`:
- `dcf-model/SKILL.md`
- `comps-analysis/SKILL.md`
- `3-statement-model/SKILL.md`
- `lbo-model/SKILL.md`
- `competitive-analysis/SKILL.md`
- `deck-refresh/SKILL.md`

`equity-research/skills/`:
- `earnings-analysis/SKILL.md`
- `initiating-coverage/SKILL.md`
- `catalyst-calendar/SKILL.md`
- `earnings-preview/SKILL.md`
- `sector-overview/SKILL.md`

**Change / 改动:** A bilingual data-sourcing header was injected at the top of each file (after YAML frontmatter where present). The header instructs the model to prefer the 4 hexin iFinD MCPs and to **ignore** any in-body references to Daloopa / FactSet / Morningstar / S&P / Kensho / Bloomberg / Moody's / LSEG / PitchBook / Chronograph / Aiera / MT Newswires / Egnyte.

每个文件顶部（有 YAML frontmatter 的在 frontmatter 之后）注入了一段双语的数据源规则提示，要求模型优先使用 4 个同花顺 iFinD MCP，并**忽略**正文里出现的所有海外数据源引用。

---

### 3. Plugin manifest version / 插件清单版本

| File | Before | After |
|---|---|---|
| `financial-analysis/.claude-plugin/plugin.json` | `0.1.0` | `0.1.1-cn` |
| `equity-research/.claude-plugin/plugin.json` | `0.1.0` | `0.1.1-cn` |

Descriptions updated to mention "Chinese A-share / iFinD edition".

`description` 字段也更新为标注 "Chinese A-share / iFinD edition"。

---

### 4. New documentation / 新增文档

- `README.md` — top banner added (this fork is not the upstream / 顶部加 fork 声明)
- `NOTICE` — Apache-2.0 modification attribution / Apache-2.0 修改归属
- `CHANGES-CN.md` — this file / 本文件
- `docs/IFIND-SETUP.md` — bilingual setup guide / 双语安装指南
- `.github/ISSUE_TEMPLATE/config.yml` — redirect non-CN issues to upstream / 海外市场问题引导回上游

---

### 5. NOT modified / 未改动的部分

The following plugins and skills are unchanged from upstream — they have no foreign-data dependencies and remain useful as-is:

下列插件 / skill 与上游保持一致，因为它们不依赖海外数据源，可以直接使用：

- `investment-banking/` (entire plugin / 整个插件)
- `private-equity/` (entire plugin / 整个插件)
- `wealth-management/` (entire plugin / 整个插件)
- `claude-in-office/` (entire plugin / 整个插件)
- `financial-analysis/skills/{audit-xls, clean-data-xls, ib-check-deck, skill-creator, ppt-template-creator}/`
- `equity-research/skills/{model-update, morning-note, thesis-tracker, idea-generation}/`
- `partner-built/` (LSEG and S&P Global partner plugins — left in repo for upstream parity but not enabled by default in this fork's typical setup / 上游伙伴插件，保留以保持目录一致，本 fork 默认不启用)

---

## Upstream sync history / 上游同步记录

| Date | Upstream commit base | Notes |
|---|---|---|
| 2026-04-22 | (initial fork) | Forked from `anthropics/financial-services-plugins` `main` |
