---
title: 常用的DataTable搜尋方式與計算方法
thumbnail: https://i.imgur.com/NRXpcll.png
categories: 
	- 技術文件
    - .NET
	- C#
tags: [CSharp, .NET]
date: 2021/11/15
---

{% img https://i.imgur.com/NRXpcll.png 500 %}

平常在寫VSTO最常碰到的資料物件就是DataTable，今天來整理一下它的幾種搜尋方式與計算方法。

<!-- more --> 

### 搜尋

※ [] 包裹起來的代表DataTable內的欄位  
※ '' 包裹起來的代表字串  
※ ## 包裹起來的代表時間  
※ *和%代表like運算的萬用字元  

#### 1. DataView.RowFilter 
```c
            DataView dv = new DataView(dt);
            dv.RowFilter = "[first_name] like '%oo%'";
```
#### 2. DataTable.Select
```c
            dt.Select("[first_name] like '%oo%'")
```
#### 3. DataTable.Rows.Find
```c
            dt.PrimaryKey = new DataColumn[] { dt.Columns["id"]};
            object[] targets = new object[1];
            targets[0] = 100;
            dt.Rows.Find(targets);
```
#### 4. LINQ
```c
            dt.AsEnumerable().Where(o => o.Field<string>("first_name").Contains("oo"));
```
#### 效能表現 DataTable.Rows.Find > DataView.RowFilter > DataTable.Select > LINQ
雖然DataTable.Rows.Find的效能表現是最好，但是它只能夠透過PrimaryKey作查詢，實在缺乏彈性;  
相反的LINQ雖是裡面效能最差的一個方法，但是如果需要一些複雜的集合操作，比如Group、Merge等操作的話，使用LINQ會更有彈性。


### 計算
#### DataTable.Compute(string expression, string filter)支援聚合函數如下:
#### 1. Sum
```c
            dt.Compute("Sum([score])", string.Empty);
```
#### 2. Average
```c
            dt.Compute("Avg([score])", string.Empty);
```
#### 3. Minimum 
```c
            dt.Compute("Min([score])", string.Empty);
```
#### 4. Maximum
```c
            dt.Compute("Max([score])", string.Empty);
```
#### 5. Count
```c
            dt.Compute("Count([score])", string.Empty);
```
#### 6. Statistical Standard Deviation
```c
            dt.Compute("Stdev([score])", string.Empty);
```
#### 7. Statistical Variance
```c
            dt.Compute("Var([score])", string.Empty);
```