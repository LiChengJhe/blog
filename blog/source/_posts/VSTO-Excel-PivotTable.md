---
title: VSTO Excel Pivot Table 介紹
thumbnail: https://i.imgur.com/6TgSUaY.png
categories: 
	- 技術文件
	- VSTO
tags: VSTO
date: 2021/08/27
---

{% img https://i.imgur.com/6TgSUaY.png 500 %}

樞紐分析表(PivotTable)是Excel強大的功能之一，它可以用來做一些簡單的資料統計與計算。本篇將介紹如何使用VSTO建立樞紐分析表(PivotTable)。

<!-- more --> 
### # 範例
這邊有個範例資料，它包含各家分店每月的商品品項和數量，接下來我們將用程式碼說明如何將右邊的資料放進樞紐紐分析表(PivotTable)進行的資料轉置和加總。
![](https://imgur.com/XGtw8Cc.png)

範例程式:
```c
            // 使用Range來建立PivotCaches
            var pc = Globals.ThisWorkbook.PivotCaches().Create(XlPivotTableSourceType.xlDatabase, ItemQtyListObjects.Range);
            // 用PivotCaches來創造PivotTable，並設定其名稱與顯示的位置
            var pvt = pc.CreatePivotTable(Range["H1"], "ItemQtyReport");

            // 接著透過Orientation將欄位按照需求歸類，如Branch和Item為列、Month為欄、Qrt為值
            PivotField branchField = pvt.PivotFields("Branch");
            branchField.Orientation = XlPivotFieldOrientation.xlRowField;

            PivotField itemField = pvt.PivotFields("Item");
            itemField.Orientation = XlPivotFieldOrientation.xlRowField;
            
            PivotField monthField = pvt.PivotFields("Month");
            monthField.Orientation = XlPivotFieldOrientation.xlColumnField;
            
            PivotField qtyField = pvt.PivotFields("Qty");
            qtyField.Orientation = XlPivotFieldOrientation.xlDataField;

```
### # 其他常用的屬性

設定是否顯示自動加總:
```c
            // 不顯示欄位自動加總
            pivotField.Subtotals[1] = false;

            // 不顯示列自動加總
            pivotTable.RowGrand = false;

            // 不顯示欄自動加總
            pivotTable.ColumnGrand = false;

```

設定客製化的樣式:
```c
            // 啟用欄的樣式
            pivotTable.ShowTableStyleColumnStripes = true;
            // 啟用列的樣式
            pivotTable.ShowTableStyleRowStripes = true;
            // 設定樣式模板
            pivotTable.TableStyle2 = "Your Custom Table Style";

```

設定是否顯示展開和摺疊按鈕:
```c
            // 不顯示展開和摺疊按鈕
            pivotTable.ShowDrillIndicators = false;

```

設定欄位模式:
```c
            // 表格模式
            pivotTable.LayoutForm = XlLayoutFormType.xlTabular;
            // 大綱模式
            pivotTable.LayoutForm = XlLayoutFormType.xlOutline;

```