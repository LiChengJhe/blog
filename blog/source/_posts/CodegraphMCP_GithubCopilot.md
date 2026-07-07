---
title: VSCode x CodeGraph MCP x Github Copilot 教學
thumbnail: https://thumbs2.imgbox.com/96/42/U315ZFDg_t.png
categories:
  - Technical documents
  - Github Copilot
tags: Github Copilot, CodeGraph, MCP
date: 2026/07/03
---

{% img https://thumbs2.imgbox.com/9b/c1/3FTaX71t_t.png 500 %}

# CodeGraph MCP + GitHub Copilot 完整教學文件

> 目標：讓 GitHub Copilot Agent 在大型 Repo 中優先使用 CodeGraph MCP 做 codebase understanding，避免一開始就用 `read_file / grep / glob` 大量掃描檔案。

***

<!-- more --> 

## 1. 適用情境

適合以下工作流：

* C# / Vue / VSTO / TypeScript 大型專案
* 需要找實作位置
* 需要查 caller / callee
* 需要分析影響範圍
* 需要做 minimal change
* 希望減少 Copilot Agent 無意義讀檔
* 希望避免 AI 透過 raw file scanning 猜測架構

CodeGraph 官方安裝流程包含：安裝 CLI、設定 Agent、重新啟動 Agent、在每個專案執行 `codegraph init` 建立 `.codegraph/` 索引。 [\[colbymchen....github.io\]](https://colbymchenry.github.io/codegraph/getting-started/installation/)

***

## 2. 核心概念

### 2.1 傳統 Copilot Agent 探索模式

```text
User Prompt
  ↓
glob / grep / search
  ↓
read_file
  ↓
read_file
  ↓
read_file
  ↓
推測架構與關聯
  ↓
修改程式
```

問題：

* 容易讀太多檔案
* Token 消耗高
* 關聯判斷可能不完整
* 容易探索超出需求範圍

***

### 2.2 CodeGraph First 模式

```text
User Prompt
  ↓
CodeGraph MCP
  ↓
Symbol Search
  ↓
Callers / Callees
  ↓
Impact Analysis
  ↓
Read affected files only
  ↓
Minimal change
```

CodeGraph 官方文件說明，安裝後仍需要在每個專案執行 `codegraph init`，此命令會建立本地 `.codegraph/` 目錄並建置 graph。 [\[colbymchen....github.io\]](https://colbymchenry.github.io/codegraph/getting-started/installation/)

***

## 3. 安裝方式

### 3.1 前置需求

先確認 Node.js / npm：

```powershell
node -v
npm -v
```

如果尚未安裝 Node.js，建議安裝 LTS 版。

***

### 3.2 使用 npm 安裝 CodeGraph

```powershell
npm install -g @colbymchenry/codegraph
```

安裝完成後確認版本：

```powershell
codegraph --version
```

npm 全域安裝方式與 `codegraph --version` 驗證方式可見公開安裝教學。 [\[blog.csdn.net\]](https://blog.csdn.net/hai4321/article/details/161821396), [\[blog.csdn.net\]](https://blog.csdn.net/G_whang/article/details/161948958)

***

### 3.3 另一種官方快速安裝方式

官方文件也提供直接執行：

```powershell
npx @colbymchenry/codegraph
```

此 installer 會詢問要設定哪些 Agent、是否加入 PATH、以及設定套用到全域或本地專案。 [\[colbymchen....github.io\]](https://colbymchenry.github.io/codegraph/getting-started/installation/)

***

## 4. 初始化專案索引

進入你的專案根目錄：

```powershell
cd C:\Source\YourProject
```

建立 CodeGraph 索引：

```powershell
codegraph init
```

完成後專案會出現：

```text
.codegraph/
```

官方文件說明，一次 global install 可涵蓋所有專案，但每個專案仍需各自執行一次 `codegraph init`。 [\[colbymchen....github.io\]](https://colbymchenry.github.io/codegraph/getting-started/installation/)

***

## 5. GitHub Copilot MCP 設定

### 5.1 為什麼 GitHub Copilot 可能不會出現在 `codegraph install`

目前 CodeGraph installer 會自動偵測的 Agent 包含 Claude Code、Cursor、Codex CLI、opencode、Hermes Agent、Gemini CLI、Antigravity IDE、Kiro。 [\[colbymchen....github.io\]](https://colbymchenry.github.io/codegraph/getting-started/installation/)

如果畫面沒有 GitHub Copilot 選項，通常可以改成手動建立 VS Code MCP 設定。

***

### 5.2 建立 VS Code MCP 設定

在專案建立：

```text
.vscode/mcp.json
```

內容：

```json
{
  "servers": {
    "codegraph": {
      "type": "stdio",
      "command": "codegraph",
      "args": [
        "serve",
        "--mcp"
      ]
    }
  }
}
```

這個設定是讓 MCP client 透過 stdio 啟動 `codegraph serve --mcp`。公開教學中也列出相同方向的 MCP server 設定格式。 [\[blog.csdn.net\]](https://blog.csdn.net/hai4321/article/details/161821396)

***

### 5.3 重新啟動 VS Code

設定完成後：

1. 關閉 VS Code
2. 重新開啟 VS Code
3. 開啟該 Workspace
4. 確認 MCP server 是否被載入

官方安裝文件也要求在設定 Agent 後重新啟動 Agent，讓 MCP server 載入。 [\[colbymchen....github.io\]](https://colbymchenry.github.io/codegraph/getting-started/installation/)

***

## 6. 常用指令

### 6.1 安裝 / 更新 / 移除

```powershell
# 安裝
npm install -g @colbymchenry/codegraph

# 查看版本
codegraph --version

# 更新
codegraph upgrade

# 解除 Agent 設定
codegraph uninstall
```

官方文件說明可用 `codegraph uninstall` 移除 installer 寫入的 Agent 設定，但專案索引 `.codegraph/` 不會被刪除；要移除專案索引用 `codegraph uninit`。 [\[colbymchen....github.io\]](https://colbymchenry.github.io/codegraph/getting-started/installation/)

***

### 6.2 專案索引

```powershell
# 建立索引
codegraph init

# 查看狀態
codegraph status

# 移除專案索引
codegraph uninit
```

`codegraph init` 會建立本地 `.codegraph/` 並建置完整 graph。 [\[colbymchen....github.io\]](https://colbymchenry.github.io/codegraph/getting-started/installation/)

***

### 6.3 查 Symbol

```powershell
codegraph query UserService
codegraph query UpdateOrderAsync
codegraph query ShippingPlanService
```

公開教學列出 `codegraph query <symbol>` 作為搜尋 class / function / variable 的指令。 [\[blog.csdn.net\]](https://blog.csdn.net/hai4321/article/details/161821396)

***

### 6.4 查誰呼叫它：callers

```powershell
codegraph callers UpdateOrderAsync
codegraph callers SaveData
codegraph callers ReadApproval
```

公開教學列出 `codegraph callers <function>` 作為查找呼叫方的指令。 [\[blog.csdn.net\]](https://blog.csdn.net/hai4321/article/details/161821396)

***

### 6.5 查它呼叫誰：callees

```powershell
codegraph callees UpdateOrderAsync
codegraph callees SaveData
codegraph callees ReadApproval
```

公開教學列出 `codegraph callees <function>` 作為查找被呼叫方的指令。 [\[blog.csdn.net\]](https://blog.csdn.net/hai4321/article/details/161821396)

***

### 6.6 分析影響範圍

```powershell
codegraph impact UpdateOrderAsync
codegraph impact UserService
codegraph impact ReadApproval
```

公開教學列出 `codegraph impact <symbol>` 作為分析修改影響範圍的指令。 [\[blog.csdn.net\]](https://blog.csdn.net/hai4321/article/details/161821396)

***

### 6.7 索引更新

```powershell
# 全量更新
codegraph index

# 強制重建
codegraph index --force

# 增量更新
codegraph index --incremental

# 監控檔案變更
codegraph watch
```

公開教學列出 `codegraph index`、`codegraph index --force`、`codegraph index --incremental`、`codegraph watch` 作為索引更新方式。 [\[blog.csdn.net\]](https://blog.csdn.net/hai4321/article/details/161821396)

***

## 7. GitHub Copilot Instructions

建立：

```text
.github/copilot-instructions.md
```

放入以下完整內容。

```yaml
core:
- always use codegraph mcp before any codebase exploration or file reading
- no assumptions
- only requested tasks
- minimal change
- no execution/debug/log inspection


focus:
  - identify main problem first
  - understand dependencies before modification
  - ignore non-critical
  - prioritize blocking issue

suggestion:
  - separate section only
  - must not implement

rules:
  - unclear/conflict → ask
  - assumptions → list + wait
  - large task → split
  - failures → report
  - no exploration beyond scope
  - no modification before impact analysis

scope:
  - minimal required change

no:
  - feature addition
  - implicit optimization
  - unrelated changes
  - new package
  - architecture changes
  - design pattern changes
  - large refactor
  - style mixing
  - image generation
  - image reading

coding:
  - minimal
  - readable
  - consistent
  - follow existing style
  - preserve existing behavior
  - modify only proven relevant code

precondition:
  - understand context
  - preserve behavior
  - preserve design

testing:
  - none unless asked
```

***

## 8. 驗證 Checklist

### 8.1 安裝檢查

```powershell
node -v
npm -v
codegraph --version
```

***

### 8.2 專案檢查

```powershell
cd C:\Source\YourProject
codegraph status
```

***

### 8.3 MCP 設定檢查

確認存在：

```text
.vscode/mcp.json
```

內容：

```json
{
  "servers": {
    "codegraph": {
      "type": "stdio",
      "command": "codegraph",
      "args": [
        "serve",
        "--mcp"
      ]
    }
  }
}
```

***

## 9. 疑難排解

### 9.1 `codegraph` 不是內部或外部命令

確認 npm global path：

```powershell
npm config get prefix
```

重新開啟 PowerShell / VS Code。

***

### 9.2 Copilot 沒有使用 CodeGraph

檢查：

* `.vscode/mcp.json` 是否在 workspace 中
* VS Code 是否重新啟動
* MCP server 是否顯示在 Copilot MCP 工具清單
* `.github/copilot-instructions.md` 是否位於 repo 正確位置
* Prompt 是否明確寫 `Use CodeGraph MCP first`

***

### 9.3 CodeGraph 結果不完整

執行：

```powershell
codegraph status
codegraph index --force
```

必要時重新初始化：

```powershell
codegraph uninit
codegraph init
```

***

## 10. 建議 Repo 結構

```text
your-repo/
  .github/
    copilot-instructions.md
  .vscode/
    mcp.json
  .codegraph/
  src/
  tests/
```

說明：

* `.github/copilot-instructions.md`：控制 Copilot Agent 工作方式
* `.vscode/mcp.json`：讓 VS Code / Copilot 載入 CodeGraph MCP
* `.codegraph/`：CodeGraph 專案索引，不建議手動修改

***

## 11. 最終建議

你的目標不是單純「讓 Copilot 知道 CodeGraph 存在」，而是建立這種工作流：

```text
CodeGraph MCP
  ↓
Symbol / Caller / Callee
  ↓
Impact Analysis
  ↓
Read affected files only
  ↓
Minimal Change
```

避免這種工作流：

```text
Glob
  ↓
Grep
  ↓
Read many files
  ↓
Infer architecture
  ↓
Modify
```

因此最重要的是：

1. `.vscode/mcp.json` 要設定好
2. 每個 repo 都要 `codegraph init`
3. `.github/copilot-instructions.md` 要放在最上層
4. Prompt 仍可補一句 `Use CodeGraph MCP first`
5. 不期待 100% 強制，但可以大幅提高 Copilot 優先使用 CodeGraph 的機率

***

## 12. 參考來源

* Installation | codegraph：官方安裝流程、Agent 設定、`codegraph init`、重新啟動 Agent、解除安裝說明。 [\[colbymchen....github.io\]](https://colbymchenry.github.io/codegraph/getting-started/installation/)
* CodeGraph完整安装与使用指南 - CSDN博客：npm 安裝、MCP 設定、`query / callers / callees / impact`、索引更新指令。 [\[blog.csdn.net\]](https://blog.csdn.net/hai4321/article/details/161821396)
* CodeGraph — 代码知识图谱：安装、配置与深度使用指南-CSDN博客：npm / npx / install 指令與 `status` 驗證說明。 [\[blog.csdn.net\]](https://blog.csdn.net/G_whang/article/details/161948958)
