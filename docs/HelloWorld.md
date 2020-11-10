# Hello World

## 项目创建
Vue 3.0 中尤大使用的了新的编译工具 [vite](https://github.com/vitejs/vite)，革了 `webpack` 的命😂。我们尝试使用 `vite` 来快速创建项目。
```shell
yarn create vite-app vue3-learn
cd vue3-learn
yarn
yarn dev
```
打开 [http://localhost:3000/](http://localhost:3000/) 可以看到 Hello World
![image.png](/images/docs/cli.png)
可以看到最新的目录结构其实和以前没有差别


![image.png](/images/docs/menu.png)


不过我们可以看看入口文件 `main.js`


```javascript
import { createApp } from 'vue'
import App from './App.vue'
import './index.css'

createApp(App).mount('#app')
```
和 Vue 2.x 对比
```javascript
import Vue from 'vue'
import App from './App.vue'
new Vue({
  render: h => h(App)
}).$mount('#app');
```
其实就是使用导出函数 `createApp` 来创建，而不是以前的构造函数了。这也是 Vue 3.0 的一大改变，利用组合式 API 来构建我们的程序。
