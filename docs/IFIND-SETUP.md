# iFinD MCP setup guide / 同花顺 iFinD 安装指南

Bilingual setup guide for the **Chinese A-share / iFinD edition** of `financial-services-plugins`.
本仓库 fork 的同花顺 iFinD 接入指南，中英对照。

---

## 1. What you get / 你将获得什么

After setup, the `financial-analysis` plugin brings up **4 Hexin iFinD MCP servers** scoped to whatever project enables the plugin. Disable the plugin (or open another project) → the MCPs go down. No global pollution.

完成本指南后，启用 `financial-analysis` 插件就会自动加载 **4 个同花顺 iFinD MCP**，作用域限定在该项目。关闭插件或离开项目 → MCP 自动下线，不污染全局环境。

| MCP server | Domain / 数据域 |
|---|---|
| `hexin-ifind-ds-stock-mcp` | A 股 / 港股 / 美股个股 — 行情、财务、一致预期、估值 |
| `hexin-ifind-ds-fund-mcp`  | 公募 / 私募基金、ETF、QDII — 净值、持仓、业绩 |
| `hexin-ifind-ds-edb-mcp`   | 宏观经济库 — GDP、CPI、PMI、利率、行业景气 |
| `hexin-ifind-ds-news-mcp`  | 财经新闻、上市公司公告、研报、事件驱动 |

---

## 2. Prerequisites / 前置条件

**EN**
1. A subscription / API access to **Hexin iFinD MCP** (`api-mcp.51ifind.com`). Without it, the 4 MCPs will fail to authenticate.
2. Claude Code installed (any recent version with plugin marketplace support).
3. A local clone of this fork.

**中文**
1. 一个**同花顺 iFinD MCP**（`api-mcp.51ifind.com`）的订阅 / API 接入权限。否则 4 个 MCP 会因鉴权失败而无法启动。
2. 已安装 Claude Code（带 plugin marketplace 支持的较新版本）。
3. 本 fork 的本地 clone。

---

## 3. Get your iFinD bearer token / 获取 iFinD bearer token

**EN:** Hexin iFinD provides a JWE-encrypted bearer token after you authenticate via their portal. The token looks like:

**中文：** 在同花顺 iFinD 控制台完成鉴权后，会拿到一个 JWE 加密格式的 bearer token，形如：

```
abCDEFGHIJKLMNOPQRSTUVWX012yz.<...long base64...>
```

Keep it private — anyone holding the token can call your iFinD quota.
请妥善保管 — 持有 token 的人可以消耗你的 iFinD 配额。

---

## 4. Install / 安装步骤

### 4.1 Clone this fork / 克隆本 fork

```bash
git clone https://github.com/NBreeze-Eric/financial-services-plugins.git <somewhere-on-your-disk>
```

### 4.2 Register the local marketplace / 注册本地 marketplace

In your project's `.claude/settings.json`:

在项目的 `.claude/settings.json` 里：

```json
{
  "enabledPlugins": {
    "financial-analysis@financial-services-plugins": true,
    "equity-research@financial-services-plugins": true
  },
  "extraKnownMarketplaces": {
    "financial-services-plugins": {
      "source": {
        "source": "directory",
        "path": "<absolute path to your local clone>"
      }
    }
  }
}
```

### 4.3 Drop the iFinD token / 配置 iFinD token

Create `.claude/settings.local.json` in the **same project** (this file is gitignored — never push it):

在**同一个项目**下新建 `.claude/settings.local.json`（已 gitignore，不会被推送）：

```json
{
  "env": {
    "IFIND_AUTH": "<paste your iFinD bearer token here>"
  }
}
```

The plugin's `.mcp.json` references `${IFIND_AUTH}` in the Authorization header — Claude Code substitutes it at MCP launch.

插件的 `.mcp.json` 在 Authorization 头里用 `${IFIND_AUTH}` 占位符，Claude Code 在启动 MCP 时会自动替换。

### 4.4 Reload / 重新加载

In Claude Code:

在 Claude Code 中运行：

```
/plugin marketplace update financial-services-plugins
/reload-plugins
```

Expected output / 期望输出：

```
Reloaded: 2 plugins · 17 skills · 5 agents · 0 hooks · 4 plugin MCP servers · 0 plugin LSP servers
```

The key part is **`4 plugin MCP servers`**. If you see 11, the cache is stale — see Troubleshooting below.

关键看 **`4 plugin MCP servers`**。如果显示 11，说明缓存陈旧，看下面的排错章节。

### 4.5 Verify / 验证

```
/mcp
```

You should see exactly 4 entries under the plugin, all `hexin-ifind-ds-*`, all green / connected. None of `daloopa`, `factset`, `morningstar`, etc.

应当看到插件下恰好 4 个 `hexin-ifind-ds-*` 条目，全部绿灯（已连接）。**不应**出现 `daloopa`、`factset`、`morningstar` 等。

Smoke test / 烟雾测试：

```
/financial-analysis:dcf 600519
/equity-research:earnings 000001
```

Watch the tool calls — they should hit `mcp__hexin-ifind-ds-*__*`, not foreign-data MCPs.

观察工具调用 — 应当都是 `mcp__hexin-ifind-ds-*__*`，不应出现外国数据 MCP。

---

## 5. Troubleshooting / 排错

### Symptom / 现象: `/reload-plugins` shows 11 plugin MCP servers / 显示 11 个 MCP

**Cause / 原因:** Claude is loading from a cached version of the upstream `0.1.0` plugin instead of your local `0.1.1-cn`. Most often because the user-scope `~/.claude/plugins/known_marketplaces.json` still has `source: github` for this marketplace, overriding your project-scope `directory` setting.

Claude 仍在加载缓存中的上游 `0.1.0` 版本，而不是本地 `0.1.1-cn`。通常是因为用户级 `~/.claude/plugins/known_marketplaces.json` 里这个 marketplace 的 `source` 还是 `github`，盖过了项目级的 `directory` 设置。

**Fix / 修复:**

Edit `~/.claude/plugins/known_marketplaces.json` so this marketplace points to your local clone:

```json
"financial-services-plugins": {
  "source": {
    "source": "directory",
    "path": "<absolute path to your local clone>"
  },
  "installLocation": "<same path>",
  "lastUpdated": "..."
}
```

Then clear the stale cache:

然后清除陈旧缓存：

```bash
rm -rf ~/.claude/plugins/cache/financial-services-plugins
rm -rf ~/.claude/plugins/marketplaces/financial-services-plugins
```

(Windows / Git Bash: same paths under `C:/Users/<you>/.claude/...`)

Then re-run `/plugin marketplace update financial-services-plugins` and `/reload-plugins`.

### Symptom / 现象: MCPs show "needs authentication" or fail / MCP 显示需要鉴权或启动失败

**Causes / 可能原因:**
- `IFIND_AUTH` not set in `.claude/settings.local.json`. / `IFIND_AUTH` 没在 `settings.local.json` 里设置。
- Token expired — JWE tokens have an expiry. Re-authenticate at the iFinD portal. / Token 过期，JWE token 有时效，需要重新鉴权。
- Network can't reach `api-mcp.51ifind.com:8643`. Check firewall / VPN. / 网络不通，检查防火墙 / VPN。

### Symptom / 现象: Skill still mentions Daloopa / FactSet in its output / Skill 输出里还提到 Daloopa / FactSet

The header injection tells Claude to ignore those references but the body text is still upstream. If you want to wipe them entirely, edit the SKILL.md body. PRs welcome.

提示词头部已经告诉 Claude 忽略这些引用，但正文文本仍是上游版本。如果你想彻底清除，直接编辑 SKILL.md 正文。欢迎 PR。

---

## 6. Going further / 进阶

- **Fork your own**: this fork is just a starting point. Fork it again and customize for your firm's models, templates, and workflows.
  **再 fork 一次**：本 fork 只是起点，你可以再次 fork 并按你所在机构的模型、模板、流程定制。
- **Sync with this fork**: when this fork rebases on upstream, you can `git fetch upstream && git merge` to pick up the new changes.
  **跟本 fork 同步**：本 fork 与上游同步时，你可以 `git fetch upstream && git merge` 拿到新内容。
- **Add new iFinD-only skills**: drop a new directory into `<plugin>/skills/<name>/SKILL.md`. The CN data-sourcing header is the convention — copy it from any existing skill.
  **新增 iFinD 专属 skill**：在 `<plugin>/skills/<name>/SKILL.md` 新建目录即可。CN 数据源头部是约定俗成的写法，从任意现有 skill 复制即可。
