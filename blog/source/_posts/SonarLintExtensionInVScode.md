---
title: 'Setting SonarLint In Visual Studio Code'
categories:
  - 技術文件
	- Visual Studio Code
thumbnail: https://code.visualstudio.com/assets/favicon.ico
tags: [VScode]
date: 2021/05/19
updated: 2021/05/19
---

{% img https://sonarsource.gallerycdn.vsassets.io/extensions/sonarsource/sonarlint-vscode/1.22.0/1620831688015/Microsoft.VisualStudio.Services.Icons.Default  100 %}

SonarLint能在IDE上面顯示錯誤和安全漏洞，並提供修復指南，以便您進行修復。VS Code中的SonarLint支援JavaScript，TypeScript，Python，Java，HTML和PHP等程式的分析，也可直接與SonarQube同步(需要額外設定)。
![](https://github.com/SonarSource/sonarlint-vscode/raw/HEAD/images/sonarlint-vscode.gif)
![](https://github.com/SonarSource/sonarlint-vscode/raw/HEAD/images/sonarlint-rule-description.gif)

<!-- more --> 

# 安裝SonarLint Extension
![](https://i.imgur.com/k5qQMbL.png)

# 設定 SonarQube Connection
在使用者的Settings.json 設定SonarQube，需要輸入serverUrl和token。
![](https://i.imgur.com/CcfIArM.png)

Token可以在SonarQube Web底下的Account/Security頁面產生。
![](https://i.imgur.com/EDsnG4B.png)

# 專案綁定
在工作區的Settings.json 設定SonarQube，需要輸入ProjectKey。
![](https://imgur.com/RZPyA8X.png)

ProjectKey可以在SonarQube Web底下的Project Information頁面找到它。
![](https://i.imgur.com/9Py2rWR.png)