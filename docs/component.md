# 组件 Component

Vue 3.0 的组件还是和 Vue 2.x 没有区别，需要注意的是 `props` 访问是在 `setup` 函数中
```javascript
// HelloWorld.vue
<template>
  <div>
    {{msg}}
  </div>
</template>

<script>
  export default {
    name: 'HelloWorld',
    props: {
      msg: {
        type: String,
        default: 'HelloWorld'
      }
    },
    setup(props, context) {
      console.log(props, context)
    }
  }
</script>

```
`setup` 函数提供两个参数：`props` 和 `context`
## props


props 是一个响应式的对象，它不能被解构，不然会失去其响应性
```javascript
// bad
setup({msg}) {
	console.log(msg) // 失去了响应性
}
// good
setup(props) {
	console.log(props.msg) // 具有响应性
}

```
它还是和 Vue 2.x 一样是不能直接被手动修改，不然会抛出异常警告


## context


`setup` 函数第二个参数是上下文对象，它只暴露了三个属性 `attrs` 、`emit` 、`slots`
```javascript
setup(props, context) {
	context.attrs
  context.emit
  context.slots
}
```

- attrs 可以访问组件上的属性
- emit 和 Vue 2.x this.$emit 效果一样
- slots 在 jsx 中使用



## JSX
由于 vite  是可以直接支持 jsx 语法，所以我们也可以在项目中直接使用 jsx。需要使用 `defineComponent` 包裹来使其变成一个双向绑定数据的组件
```jsx
import { defineComponent } from 'vue';

const Button = defineComponent({
  setup(props, { slots }) {
    return () => (
      <button class={'btn', `btn-${props.type}`}>
        {slots.default()}
      </button>
    )
  }
})

export default Button;
```


