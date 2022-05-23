---
title: VSTO Excel Style Pattern 介紹
thumbnail: https://i.imgur.com/6TgSUaY.png
categories: 
	- 技術文件
	- VSTO
tags: [VSTO]
date: 2021/10/22
---

{% img https://i.imgur.com/6TgSUaY.png 500 %}

實務上時常會有標記顯示特定欄位的需求，所以我們需要改變某個儲存格(Cell)的背景色或樣式，最簡單的方法是透過UI介面去設定樣式(Pattern)如下圖所示，然後再將樣式直接帶入到Cell的Style屬性內就可以呈現其樣式。不幸的是這個做法缺乏彈性，且可調整的屬性比較少。

<!-- more --> 

![](https://i.imgur.com/OircRfF.png)

舉例來說如果需要多層次漸層，也就是超過兩個顏色以上的漸層設定，透過UI介面的放式設定樣式最多只能設定兩種顏色，因此只能透過程式的方式建立Style Pattern來達成需求。

接下來我們將用幾個範例來展現Excel內建的Pattern效果，其範例如下所示。


### # 範例
![](https://i.imgur.com/HK7PgtK.png)

xlPatternSolid: 將儲存格填滿。  
※ VSTO Excel的色彩格式都是使用OLE格式，它是一個十進制的整數，其計算方式為 OLE = red + (green * 256) + (blue * 256 * 256)。  
※ 如果想要OLE格式與其它色彩格式的轉換可以使用ColorTranslator這個類別。  
```c
            var style = Globals.ThisWorkbook.Styles.Add("xlPatternSolid");
            style.Interior.Pattern = XlPattern.xlPatternSolid;
            style.Interior.Color = ColorTranslator.ToOle(ColorTranslator.FromHtml("#FFFFE0")); // 黃
```

xlPatternLinearGradient: 將儲存格填滿，並以線性漸層呈現。  
※ 可在ColorStops加入多段顏色，來達成多層次漸層。  
※ ColorStops的範圍大小為0~1。
```c
            style = Globals.ThisWorkbook.Styles.Add("xlPatternLinearGradient");
            style.Interior.Pattern = XlPattern.xlPatternLinearGradient;
            style.Interior.Gradient.ColorStops.Clear();
            style.Interior.Gradient.Degree = 180;
            style.Interior.Gradient.ColorStops.Add(0).Color =
                ColorTranslator.ToOle(ColorTranslator.FromHtml("#FF1C1C")); // 紅
            style.Interior.Gradient.ColorStops.Add(0.2).Color =
                ColorTranslator.ToOle(ColorTranslator.FromHtml("#FFA600")); // 橘
            style.Interior.Gradient.ColorStops.Add(1).Color =
                ColorTranslator.ToOle(ColorTranslator.FromHtml("#FFFFE0")); // 黃
```

xlPatternRectangularGradient: 將儲存格填滿，並以矩形漸層呈現。  
※ 可用RectangleLeft、RectangleRight、RectangleTop、RectangleBottom來控制矩形形狀，其參數範圍大小為0~1。
```c
            style = Globals.ThisWorkbook.Styles.Add("xlPatternRectangularGradient");
            style.Interior.Pattern = XlPattern.xlPatternRectangularGradient;
            style.Interior.Gradient.ColorStops.Clear();
            style.Interior.Gradient.RectangleLeft = 0.5;
            style.Interior.Gradient.RectangleRight = 0.5;
            style.Interior.Gradient.RectangleTop = 0.5;
            style.Interior.Gradient.RectangleBottom = 0.5;
            style.Interior.Gradient.ColorStops.Add(0).Color =
                ColorTranslator.ToOle(ColorTranslator.FromHtml("#FF1C1C")); //紅
            style.Interior.Gradient.ColorStops.Add(1).Color =
                ColorTranslator.ToOle(ColorTranslator.FromHtml("#000000")); //黑
```

其他樣式很單純不須要額外設定，如下所示:  
![](https://i.imgur.com/4uPST1B.png)