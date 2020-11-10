# 组件的生命周期

Vue 3.0 中的生命周期和 Vue 2.x 中生命周期没有多大区别，只是可以直接导入函数来注册生命周期钩子


```javascript
import { onMounted, onUpdated, onUnmounted } from 'vue'

setup() {
  onMounted(() => {
    console.log('mounted!')
  })
  onUpdated(() => {
    console.log('updated!')
  })
  onUnmounted(() => {
    console.log('unmounted!')
  })
}
```
需要注意的是，生命周期钩子只能在 `setup` 函数中使用，因为它们**依赖于内部的全局状态**来定位当前组件实例（正在调用 `setup()` 的组件实例）, 不在当前组件下调用这些函数会抛出一个错误。

**与 2.x 版本生命周期相对应的组合式 API**
**

- ~~`beforeCreate`~~ -> 使用 `setup()`
- ~~`created`~~ -> 使用 `setup()`
- `beforeMount` -> `onBeforeMount`
- `mounted` -> `onMounted`
- `beforeUpdate` -> `onBeforeUpdate`
- `updated` -> `onUpdated`
- `beforeDestroy` -> `onBeforeUnmount`
- `destroyed` -> `onUnmounted`
- `errorCaptured` -> `onErrorCaptured`



**新增的钩子函数**
**
调试钩子函数：

- `onRenderTracked`
- `onRenderTriggered`

两个钩子函数都接收一个 `DebuggerEvent`，与 `watchEffect` 参数选项中的 `onTrack` 和 `onTrigger` 类似
```javascript
export default {
  onRenderTriggered(e) {
    debugger
    // 检查哪个依赖性导致组件重新渲染
  }
}
```
