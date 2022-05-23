---
title: 如何取得DataTable Row的異動狀態與資料 ?
thumbnail: https://i.imgur.com/NRXpcll.png
categories: 
	- 技術文件
    - C#
tags: [CSharp]
date: 2022/05/23
---

{% img https://i.imgur.com/NRXpcll.png 500 %}

最近遇到了一個VSTO Excel需求，需要知道Excel Sheet中每筆Row的編輯狀態以及新舊值，但是在VSTO Excel控制項物件(如ListObject、NamedRange)，
它們不支援這個功能，也就是說沒辦法細分編輯的行為(如Add,Modify,Delete,Uncahnge)以及取得舊值。

不過，幸好我們都是以[DataTable](https://docs.microsoft.com/zh-tw/dotnet/api/system.data.datatable?view=net-6.0)作為DataSource，所以我們可以使用DataTable的相關方法和屬性(如[GetChanges](https://docs.microsoft.com/zh-tw/dotnet/api/system.data.datatable.getchanges?view=net-6.0)、[AcceptChanges](https://docs.microsoft.com/zh-tw/dotnet/api/system.data.datatable.acceptchanges?view=net-6.0)、[DataRowState](https://docs.microsoft.com/zh-tw/dotnet/api/system.data.datarowstate?view=net-6.0)、[DataRowVersion](https://docs.microsoft.com/zh-tw/dotnet/api/system.data.datarowversion?view=net-6.0))就可以知道每個Row的編輯狀態以及新舊值，
而且也有相關的Events(如[ColumnChanged](https://docs.microsoft.com/zh-tw/dotnet/api/system.data.datatable.columnchanged?view=net-6.0)、 [RowChanged](https://docs.microsoft.com/zh-tw/dotnet/api/system.data.datatable.rowchanged?view=net-6.0)、 [RowDeleted](https://docs.microsoft.com/zh-tw/dotnet/api/system.data.datatable.rowdeleted?view=net-6.0)、[TableCleared](https://docs.microsoft.com/zh-tw/dotnet/api/system.data.datatable.tablecleared?view=net-6.0)、 [TableNewRow](https://docs.microsoft.com/zh-tw/dotnet/api/system.data.datatable.tablenewrow?view=net-6.0))可以當做執行方法的觸發點。

<!-- more -->

### # DataRowState

在DataTable的每一個DataRow都有一個RowState屬性。如果任意一行中某一個欄位發生改變，那麼整個DataRow的RowState也就發生了改變，以下是RowState的五種狀態：

| DataRowState | 描述 |
|--------------|------|
| Added        | 已加入到DataRowCollection中，但尚未使用AcceptChanges。|
| Deleted      | 已透過DataRow的Delete方法刪除。|
| Detached     | 已被創建，但不屬於任何DataRowCollection。DataRow 在已經建立後、加入至集合前，或如果已經從集合移除後，會立即處在這個狀態中。|
| Modified     | 已被修改，尚未使用AcceptChanges。|
| Unchanged    | 自上次使用AcceptChanges以來尚未變更。|

※如果要取消變更可以呼叫RejectChanges方法。

### # DataRowVersion

在DataTable的每一個DataColumn都有一個DataRowVersion屬性。如果任意一行中某一個欄位發生改變，那麼我們可以透過DataRowVersion取得相關數值(如新舊值)，以下是DataRowVersion的五種狀態：

| DataRowVersion | 描述 |
|----------------|-----|
| Current        | 當前數值（DataRowState=Deleted時候無效）。|
| Original       | 原來數值（DataRowState=Added、Unchanged時候無效）。|
| Proposed       | 建議數值（僅在DataRowState=Detached的時候有效）。 |
| Default        | 預設數值（DataRowState=Added、Modified或者Unchanged時，等於Current；如果DataRowState=Deleted，等於Original；如果DataRowState=Detached，那麼等於Proposed）。|
| Proposed       | 建議數值（僅在DataRowState=Detached的時候有效）。 |

### # 範例

接下來的範例，我們將使用DataTable的GetChanges方法與DataRowState、DataRowVersion屬性，來取的Row的變更狀態以及欄位新舊值。

```c
	DataTable dt = new DataTable();
	dt.Columns.Add("Name");
	dt.Columns.Add("Phone");
	dt.Rows.Add("Andy", "0919543196");
	dt.Rows.Add("Jack", "0972188166");
	dt.Rows.Add("Lily", "0977234521");
	dt.AcceptChanges(); // Commit Data

	dt.Rows.Add("Anny", "0957234521"); // 新增
	dt.Rows[0][0] = "AndyLu"; // 修改
	dt.Rows[1].Delete(); // 刪除

	DataTable cdt = dt.GetChanges();
	for (int i = 0; i < cdt.Rows.Count; i++)
	{
		if (cdt.Rows[i].RowState == DataRowState.Deleted)
			Console.WriteLine("刪除的行索引[{0}]，原來數值是{1}", i, cdt.Rows[i][0, DataRowVersion.Original]);
		else if (cdt.Rows[i].RowState == DataRowState.Modified)
			Console.WriteLine("修改的行索引[{0}]，原來數值是{1}，現在的新數值{2}", i, cdt.Rows[i][0, DataRowVersion.Original], cdt.Rows[i][0, DataRowVersion.Current]);
		else if (cdt.Rows[i].RowState == DataRowState.Added)
			Console.WriteLine("新增行索引[{0}]，數值是{1}", i, cdt.Rows[i][0, DataRowVersion.Current]);
	}
```
### # 執行結果
![](https://i.imgur.com/KKW06Nl.png)
修改的行索引[0]，原來數值是Andy，現在的新數值AndyLu  
刪除的行索引[1]，原來數值是Jack  
新增行索引[2]，數值是Anny  

# 參考資料
* [DataTable](https://docs.microsoft.com/zh-tw/dotnet/api/system.data.datatable?view=net-6.0)
