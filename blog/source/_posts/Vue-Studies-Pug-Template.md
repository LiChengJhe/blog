---
title: Vue Studies - Pug Template
categories:
	- 技術文件
    - Vue
    - Pug
thumbnail: https://vuejs.org/images/logo.png
tags: [Vue, Pug]
date: 2022/04/18
updated: 2022/04/18
---
![](https://i.imgur.com/7R7dxnu.png)

我們在開發Vue的時候，難免都會有重複的HTML，為了達到重複使用的目的，我們會將其元件化並且將其組合在一起，這樣我們就可以更加方便的維護和管理我們的網站元件。
在某些情境下，我們元件化的處裡方式可能不是最佳方案。舉裡來說，像是報表性質的頁面，每個報表頁面的HTML結構可能大致相同，但存在零星差異且Javascript邏輯是不一樣的，
這時如果我們試著將它抽出來變成一個插槽元件(Slot Component)，它會造成一個使用上問題，也就是說我們無法避免內外元件互動的問題，如果報表複雜度過高的情況下，那麼這將會是一場災難
，它會大大的增加程式的維護難度。

為了處理這個問題，我們可以使用[Pug](https://pugjs.org/)模板，它是一個HTML模板語言，可以讓我們簡化HTML標籤以及處裡HTML重複的問題。
<!-- more --> 

# How to use Pug in Vue ?

## 1. Install
```cmd
npm install vue-cli-plugin-pug --save-dev
```

## 2. Change template lang
```html
<template lang="pug">
</template>
```

# Use Pug to short HTML
這是一個簡單的報表頁面，接下來我們將用Pug來簡化HTML，以及使用[extends和block](https://pugjs.org/language/inheritance.html)來處理HTML重複的問題。
現在讓我們簡單用一個報表頁面來講解，整個簡化HTML的過程。
![](https://i.imgur.com/l2tXbT2.png)
```html
  <v-container fluid>
    <v-card>
      <v-card-title class="text-h5"> Query Conditions </v-card-title>
      <v-card-text class="d-flex">
        <div><v-text-field label="Dessert"></v-text-field></div>
        <div class="ml-auto">
          <v-btn depressed class="mx-2"> Reset </v-btn>
          <v-btn depressed color="primary"> Query </v-btn>
        </div>
      </v-card-text>
    </v-card>
    <v-data-table
      :headers="headers"
      :items="items"
      :items-per-page="5"
      class="elevation-1"
    ></v-data-table>
  </v-container>
```

## 1. Convert HTML to Pug
我們可以使用[html2pug](https://marketplace.visualstudio.com/items?itemName=dbalas.vscode-html2pug)這個VSCode Extension，它可以幫助我們很輕鬆地將現有的HTML轉換成Pug。
```js
v-container(fluid)
  v-card
    v-card-title.text-h5 Query Conditions
    v-card-text.d-flex
      div
        v-text-field(label='Dessert')
      .ml-auto
        v-btn.mx-2(depressed) Reset
        v-btn(depressed color='primary') Query
  v-data-table.elevation-1(:headers='headers' :items='items' :items-per-page='5')
```

## 2. Create Base Pug
我們可以將容易變動的部分挖空作為插槽，如下圖的條件區塊和表格區塊，我們將其挖空並用block插槽替代，之後我們就可以在不同的頁面中使用不同的條件區塊和表格區塊。
![](https://i.imgur.com/yMNK3ak.png)
```js
v-container(fluid)
  v-card
    v-card-title.text-h5 Query Conditions
    v-card-text.d-flex
      block Conditions
      .ml-auto
        v-btn.mx-2(depressed) Reset
        v-btn(depressed color='primary') Query
  block Table
```

## 3. Extend Base Pug
如果有其他的報表頁面需要開發，只需用extends來繼承這個Base Pug，並且置換block插槽內容即可。
```js
extends ./Base.pug
block Conditions
  div
    v-text-field(label='Dessert')
block Table
  v-data-table.elevation-1(:headers='headers' :items='items' :items-per-page='5')
```
# 參考資料
* [vue-cli-plugin-pug](https://www.npmjs.com/package/vue-cli-plugin-pug/)
* [Pug](https://pugjs.org/api/getting-started.html)