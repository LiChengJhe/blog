---
title: Vue 3 Studies - Vue-template-babel-compiler
categories:
  - 技術文件
  - Vue
thumbnail: https://vuejs.org/images/logo.png
tags: [Vue]
date: 2022/01/01
updated: 2022/01/01
---
![](https://cythilya.github.io/assets/vue/2017-05-21-vue-logo.png)

有時候難免會想在Vue的HTML Template 上面使用如下圖的 Optional Chaining(?.), Nullish Coalescing(??)的語法或是其他最新的ES語法，
這個時候我們就可以安裝 vue-template-babel-compiler 來幫我們達成這項工作。
<!-- more --> 
![](https://user-images.githubusercontent.com/14243906/127761300-076db45a-cdce-4fda-bd02-1f4fa96de6d8.png)

# How to use vue-template-babel-compiler ?

## 1. Install

```cmd
npm install vue-template-babel-compiler --save-dev
```

## 2. Setting Config

### Vue-CLI
```html
module.exports = {
    chainWebpack: config => {
        config.module
            .rule('vue')
            .use('vue-loader')
            .tap(options => {
                options.compiler = require('vue-template-babel-compiler')
                return options
            })
    }
}
```

### Webpack
```html
module.exports = {
    chainWebpack: config => {
        config.module
            .rule('vue')
            .use('vue-loader')
            .tap(options => {
                options.compiler = require('vue-template-babel-compiler')
                return options
            })
    }
}
```

# 參考資料
* [vue-template-babel-compiler](https://www.npmjs.com/package/vue-template-babel-compiler/)