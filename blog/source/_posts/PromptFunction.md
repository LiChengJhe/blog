---
title: Functional Prompt
thumbnail: https://i.imgur.com/0rfm2iE.png
categories: 
	- 技術文件
	- Prompt
    - ChatGPT
tags: [Prompt, ChatGPT]
date: 2023/07/10
---

{% img https://i.imgur.com/0rfm2iE.png 500 %}

ChatGPT已經逐漸融入了我們的生活之中，對於程式開發工作也有著不少幫助，像是JSON轉換成其他語言的Class或是資料格式之間的轉換，我們都可以在第一時間透過ChatGPT來完成，
儘管ChatGPT如此強大，倘若沒有良好的Prompt，ChatGPT也無法產生出你所預想的結果，甚至很可能演變成一本正經的胡說八道情況。
因此本篇來介紹Functional Prompt，讓我們用Functional Programming的概念寫出優良又簡短Prompt讓ChatGPT為我們做事!!
<!-- more -->

### # 規則一 : 開頭使用Return
使用Return可以明白告訴ChatGPT產生結果，而非只產生程式碼。

範例Prompt:
```csharp
return uppercase("abc")
```
ChatGPT Output:
```python
To convert a string to uppercase in Python, you can use the built-in upper() function. Here’s an example:

string = "abc"
string_uppercase = string.upper()
print(string_uppercase)

This will output:
ABC
```

### # 規則二 : Function必須具有清楚明瞭的語意，並且單一職則
亦可使用多個Function來完成一連串操作，如以下範例將JSON Object轉換成C# Class，並且以Pascal作為命名規則。

範例Prompt:
```js
return json({
    "id": "001",
    "name": "andy",
    "phone": "0972188166",
    "email": "abc123@gmail.com"
}).toCSharpClass().usePascal()
```
ChatGPT Output:
```csharp
Here’s the C# class in Pascal case:

public class JsonClass
{
    public string Id { get; set; }
    public string Name { get; set; }
    public string Phone { get; set; }
    public string Email { get; set; }
}
```

### # 規則三 : 可以使用Anonymous Function，作為語意補充的方法
例如將 JSON Object的欄位映射到另一個 JSON Array。

範例Prompt:
```js
return json({
    "id": "001",
    "name": "andy",
    "phone": "0972188166",
    "email": "abc123@gmail.com"
}).keys()
  .map(o=>{
    text: o,
    align: "left",
    sortable: false,
    value: o,
  })
```
ChatGPT Output:
```js
You can use the Object.keys() method to get an array of keys from the JSON object and then use the Array.prototype.map() method to create an array of objects with the desired properties. Here’s an example:

const json = {
  "id": "001",
  "name": "andy",
  "phone": "0972188166",
  "email": "abc123@gmail.com"
};

const arr = Object.keys(json).map(key => {
  return {
    text: key,
    align: "left",
    sortable: false,
    value: key,
  };
});

console.log(arr);

This will output:
[
  { text: 'id', align: 'left', sortable: false, value: 'id' },
  { text: 'name', align: 'left', sortable: false, value: 'name' },
  { text: 'phone', align: 'left', sortable: false, value: 'phone' },
  { text: 'email', align: 'left', sortable: false, value: 'email' }
]
```

# 參考資料
* [Prompt Engineering Guide](https://www.promptingguide.ai/)
