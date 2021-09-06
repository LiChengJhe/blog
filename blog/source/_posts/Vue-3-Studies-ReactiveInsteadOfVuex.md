---
title: Vue 3 Studies - 用Reactive取代麻煩的Vuex
categories:
	- 技術文件
  - Vue
thumbnail: https://vuejs.org/images/logo.png
tags: [Vue]
date: 2021/08/09
updated: 2021/08/09
---
![](https://cythilya.github.io/assets/vue/2017-05-21-vue-logo.png)

Vue 3 的Composition API 提供兩種資料響應方式分別是 ref 和 reactive，其中ref適用於監聽簡單型別的資料、reactive適用於監聽複雜型別的資料(物件、陣列)。
過去我們使用Vuex來管理狀態來幫助我們處裡資料響應的問題，但同時我們也寫了很多很多的action、getter、mutations、state；現在不訪可試試看寫寫簡單的reactive來達成資料響應的功能。

<!-- more --> 
# 範例
這裡有一個簡單的reactive範例，在上面Input元件輸入資料，則下面的Info要顯示輸入的資料。
![](https://imgur.com/N1SW2AJ.png)

首先我們先用reactive建立一個響應物件與其set方法。
```js
import { reactive } from "vue";

export const user = reactive({
  account:null,
  name:null
});

export function setUser(val) {
  user.account = val.account;
  user.name = val.name;
}
```

接著建立UserInput並監聽account和name，只要有異動就呼叫setUser。
```html
<template>
  <div>
    Account: <input type="text" v-model="account" /> Name:
    <input type="text" v-model="name" />
  </div>
</template>

<script>
import { setUser } from "../store/user";
export default {
  name: "UserInput",
  data() {
    return { account: null, name: null };
  },
  watch: {
    account: function () {
      setUser({ account: this.account, name: this.name });
    },
    name: function () {
      setUser({ account: this.account, name: this.name });
    },
  },
};
</script>
```

再來建立UserInfo並透過先前使用reactive建立好的user取值。
```html
<template>
  <div >
    <h3>Account = {{ account }}</h3>
    <h3>Name = {{ name }}</h3>
  </div>
</template>

<script>
import { user } from "../store/user";
export default {
  name: "UserInfo",
  computed: {
    account: () => user.account,
    name: () => user.name,
  },
};
</script>
```


最後在App呼叫UserInput和UserInfo這兩個元件就完成啦~~~
這樣看起來是不是比Vuex簡單上很多呢XD
```html
<template>
  <UserInput />
  <UserInfo />
</template>

<script>
import UserInput from "./components/UserInput.vue";
import UserInfo from "./components/UserInfo.vue";

export default {
  name: "App",
  components: {
    UserInput,
    UserInfo,
  },
};
</script>
```

# 參考資料
* [Composition API RFC](https://composition-api.vuejs.org/)