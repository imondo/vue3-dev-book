# 组件间的通信

Vue 3.0 组件间的通信其实还是和 Vue 2.x 一样，只是 `emit` 发射事件不是通过` this.$emit` 来实现，而是 `emit` 函数。
## 父子组件
```javascript
// Child.vue
<template>
  <button @click="click">{{text}}</button>
</template>
<script>
  export default {
    props: {
      text: String
    },
    setup(props, { emit }) {
      return {
        click() {
          emit('get', '来自 Child 问候');
        }
      }
    }
  }
</script>

// Parent.vue
<template>
  <Child text="组件通信" @get="onGet"/>
</template>
<script>
  import Child from './Child.vue';
  export default {
    components: {
      Child
    },
    props: {
      text: String
    },
    setup(props, { emit }) {
      return {
        onGet(msg) {
          console.log(msg); // 来自 Child 问候
        }
      }
    }
  }
</script>
```
## 模板 Refs
Vue 3.0 中由于 _reactive refs_ 和 _template refs_ 的概念已经是统一。组件中没有 `this.$refs` 来访问，不过可以通过声明 ref 来获取组件实例信息来实现通信
```javascript
<template>
  <Child ref="child" text="组件通信" @get="onGet"/>
</template>
<script>
  import { ref } from 'vue'
  export default {
		...
    setup(props) {
      const child = ref(null)
      return {
        child,
        onGet(msg) {
          // 通过 child 来访问子组件实例上的信息
          console.log(msg, child.value.msg); // 来自 Child 问候 1
        }
      }
    }
  }
</script>
```
我们也可以通过子组件上的 ref 信息来访问到父组件上的实例信息；**_当然我们也推荐这样直接访问_**
```javascript
<script>
  import { ref } from 'vue'
  export default {
		...
    setup(props, { emit }) {
      const child = ref(null)
			...
      return {
        ...
        sumbit() {
          // 父组件上的属性 __vueParentComponent.parent.ctx 上下文
          console.log('父组件上的 count: ', child.value.__vueParentComponent.parent.ctx.count)
        }
      }
    }
  }
</script>
```
## 依赖注入
`provide` 和 `inject` 提供我们依赖注入，它和 Vue 2.x 的 `provide/inject` 效果类似，它只能在 `setup` 函数中使用。利用它实现大部分的逻辑复用
```javascript
// provide.js
import { inject, provide, ref } from 'vue'
const listSy = Symbol();
export const useListProvide = () => {
  const list = ref([
    {
      name: 'Mondo'
    },
    {
      name: 'imondo.cn'
    }
  ])
  const getList = () => {
    console.log('getList');
  }
  const add = () => {
    console.log('add')
    list.value.push({
      name: '微木'
    })
  }
  provide(listSy, {
    getList,
    list,
    add
  })
};

export const useListInject = () => {
  return inject(listSy)
}
```
在 App.vue 中注入全局状态
```javascript
import { useListProvide } from './components/provide.js'

setup() {
	useListProvide();
}
```
在组件中使用数据
```javascript
setup(props) {
  const { list, add } = useListInject();
  console.log('list：', list.value)
  return {
    list,
    add
  }
}
```
不管是兄弟组件或者是嵌套多级的组件，这样其实是可以获取到一个全局的响应式的状态。
