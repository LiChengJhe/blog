---
title: 使用Chrome內建的Recorder錄製網頁操作行為
categories:
    - 技術文件
    - Chrome
tags: [Chrome]
date: 2022/02/09
updated: 2022/02/09
thumbnail: https://www.kocpc.com.tw/wp-content/uploads/2019/08/1565382278-af5729af76e9880fd1125df44a67e591.jpg
---

{% img https://www.kocpc.com.tw/wp-content/uploads/2019/08/1565382278-af5729af76e9880fd1125df44a67e591.jpg 500 %}

在開發網頁的過程中，避免不了不斷的測試功能，有時候專案需求很緊急，可能沒有時間撰寫像Cypress這類的E2E測試，在不得己的情況下只能手動測試功能。
幸運的是最近Chrome更新內建了一個實驗性工具Recorder，它可以透過錄製的方式記錄網頁的操作行為，並將其轉換成自動化腳本，能讓我們省下不少測試時的時間成本。
舉例來說，上網訂外送，我們只要到外送平台官網，打開Recorder並點擊"Record"後開始訂餐操作，包括輸入帳號密碼、選擇餐點、付款等，之後便可以將這一連串動作儲存為一個腳本，如果要重現這個流程，我們只要執行該腳本即可。

<!--more-->
## 範例

我們以外送平台網站為例，錄製的操作流程如下：

1.進入外送平台網站後，打開Recorder輸入腳本名稱並按下Start a new recording。
![Start Recorder](https://imgur.com/zqbKZe7.png)

2.接著開始訂餐操作，包括輸入帳號密碼、選擇餐點、送出、登出，此時Recorder Panel會同步記錄下操作。
![Record Step](https://i.imgur.com/VR8LrBX.png)

3.在操作的過程中，還能編輯操作步驟，例如更改Selector或是加入Asserted Events等參數。
![Change Step](https://i.imgur.com/X9MbEMp.png)

4.在按下結束錄製後，即可點選Replay，來重現錄製的流程。
※若是想要把腳本分享給其他人，點選上方的匯出按鈕即可得到該腳本的JS檔
![Replay](https://i.imgur.com/XY40Y0H.png)

5.若點選Measure Performance，除了重現錄製的流程，還會產生一份效能報告。
![Performance Report](https://i.imgur.com/FfLDDS0.png)

----------

## 參考資料：
* [Record, replay and measure user flows](https://developer.chrome.com/docs/devtools/recorder/)
