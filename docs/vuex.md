# Vuex 应用状态管理

Vue 3.0 中最新的 Vuex 方案在 [v4.0.0-rc.1](https://github.com/vuejs/vuex/releases/tag/v4.0.0-rc.1) 版本，不过[文档](https://v3.vuejs.org/api/application-api.html#component)还是保持了更新状态。
## 安装


使用 yarn 安装最新的 vuex 版本
```shell
yarn add vuex@next
```
## 使用
### createStore
Vuex 4 版本中提供 createStore 函数来创建 store。新建 `store.js` 文件
```javascript
import { createStore } from 'vuex'

const store = createStore({
  state: {
    count: 0
  },
  mutations: {
    increment(state, count){
      state.count = count
    }
  },
  actions: {
    Increment({commit, state}) {
      state.count++
      commit('increment', state.count)
    }
  }
})

export default store;
```
我们可以发现其实写法和以前没什么差别。在 `main.js` 中 `use` 状态
```javascript
import { createApp } from 'vue'
import Store from './components/Store.vue'
import store from './store.js'

createApp(Store)
  .use(store)
  .mount('#app')
```
### useStore
由于在 Vue 3.0 中没有 `this` 概念，所以 `Vuex` 提供了 `useStore` 在 `setup` 函数中访问，它的对象其实和以前的 `this.$store` 没有差别
```javascript
<template>
  <h3>Vuex 状态</h3>
  <div>{{count}}</div>
  <button @click="add">累加</button>
</template>
<script>
  import { computed, defineComponent } from 'vue'
  import { useStore } from 'vuex'
  export default defineComponent({
    setup() {
      const store = useStore()
      return {
        count: computed(() => store.state.count),
        add: () => store.dispatch('Increment')
      }
    }
  })
</script>
```
需要注意的是我们不能直接通过赋值得到一个响应式的 `count`，而需要使用 Vue 3.0 的计算属性 `computed` 代理才可以
```javascript
// bad
const count = store.state.count

// good
const count = computed(() => store.state.count)
```


