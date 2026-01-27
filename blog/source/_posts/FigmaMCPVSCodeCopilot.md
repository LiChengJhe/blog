---
title: VSCode x Figma MCP x GitHub Copilot 教學
thumbnail: https://thumbs2.imgbox.com/36/e3/nA3548GA_t.png
categories:
  - Technical documents
  - GitHub Copilot
tags: GitHub Copilot, Figma, MCP
date: 2026/01/27
---

{% img https://thumbs2.imgbox.com/6c/fa/85AAUalh_t.png 500 %}



# 如何在 VSCode 中整合 Figma MCP 與 GitHub Copilot

本教學說明如何在 Visual Studio Code (VSCode) 中結合 Figma MCP（Model Context Protocol）與 GitHub Copilot，實現設計稿與程式碼的高效協作。

<!-- more -->

## 教學目標

本教學將帶領您了解：

- Figma MCP 的功能與應用
- 如何讓 Copilot 讀取設計稿並自動產生程式碼
- 如何設定 `.mcp` 檔案以啟動 MCP 伺服器
- 運用 Agent Mode 提升 Copilot 的 UI 生成能力
- 實用的 Copilot 提示語範例

## Figma MCP 簡介

Figma MCP（Model Context Protocol）是一項協議，能讓 GitHub Copilot 直接存取 Figma 設計資料（如元件、屬性、佈局等），協助開發者根據設計稿自動產生對應的程式碼。只需貼上 Figma 的 frame 或 layer 連結，Copilot 即可取得設計細節，提升前端開發效率。

## 前置需求

| 軟體/服務 | 說明 |
| --- | --- |
| VSCode | 請安裝最新版，並啟用 GitHub Copilot Chat 擴充功能 |
| Node.js | 用於執行 MCP server，請確認已安裝 |
| Figma Access Token | 需申請 Figma API 金鑰以存取設計資料 |
| Copilot MCP 功能 | 請於 Copilot 設定中啟用 MCP 功能 |

## 步驟 1：產生 Figma Access Token

1. 登入 Figma，進入 Settings → Account
2. 點選「Generate Personal Access Token」產生金鑰
3. 請妥善保存此金鑰，勿外洩

## 步驟 2：於 VSCode 設定 MCP Server

請於專案目錄下建立 `.vscode/mcp.json`，範例如下：

```json
{
  "inputs": [
    {
      "type": "promptString",
      "id": "figma-key",
      "description": "Figma API Key",
      "password": true
    }
  ],
  "servers": {
    "figma": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "-y",
        "figma-developer-mcp",
        "--figma-api-key",
        "${input:figma-key}",
        "--stdio"
      ]
    }
  }
}
```

若 Node.js 版本較舊，請於 `args` 增加 `--node-options=--experimental-fetch` 參數，例如：

```json
{
  "servers": {
    "Framelink Figma MCP": {
      "command": "cmd",
      "args": [
        "/c",
        "npx",
        "-y",
        "--node-options=--experimental-fetch",
        "figma-developer-mcp",
        "--figma-api-key=FIGMA_KEY",
        "--stdio"
      ]
    }
  }
}
```

## 步驟 3：驗證 Figma MCP 設定

1. 儲存 `.mcp` 設定檔
2. 重新啟動 VSCode 以載入 MCP 設定
3. 於 Copilot Chat 右上角設定中，確認 MCP Server – Figma 已啟用
4. MCP 服務可手動啟動、停止或重啟

## 步驟 4：Agent Mode 實作

1. 於 Figma 右鍵選取區塊，複製 selection 連結
2. 於 Copilot Chat 輸入提示語並貼上連結
3. Copilot 會自動解析 node-id，MCP server 取得設計資料
4. Copilot 根據設計自動產生 UI 程式碼

## Copilot 提示語範例

**簡單：產生 UI 元件**

```
這是 Figma frame: https://www.figma.com/design/xxx/xxx?node-id=xxx&p=f&t=xxx
請產生符合此畫面的 Vue 元件，包含佈局、間距、字型大小。
```

**進階：產生 UI 元件**

```
根據這個 Figma 設計連結產生 Vue 元件： https://www.figma.com/design/xxx/xxx?node-id=xxx&p=f&t=xxx

請遵循以下最佳實踐：
- 使用 TypeScript 的函式型 Vue
- 遵循設計系統（元件、間距、字體）
- 使用 Flex 或 CSS Grid 實現響應式
- 使用語意化 HTML
- 重複元素請抽成可重用元件
- 命名清晰、易維護

輸出格式：
1. 元件資料夾結構
2. Vue + TypeScript 程式碼
3. 設計決策說明
```

## 預期成效

- 可於 VSCode 直接取得 Figma 設計細節，提升協作效率
- Copilot 可自動協助設計轉換為程式碼
- UI 生成流程更為迅速與精確
- 促進工程師與設計師間的合作
