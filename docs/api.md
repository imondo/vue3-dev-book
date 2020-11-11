# 组合式 API

组合式 API 就是利用函数式 API 灵活的组合我们的组件


## setup

`setup` 函数是组件的入口，每个 `vue` 文件必须传入的一个函数，它初始时间在 `beforeCreate` 之前。


**模板中使用：** 它返回一个对象

```javascript
import { ref } from 'vue'
export default {
  name: 'App',
  setup() {
    const count = ref(0)
    return {
      count
    }
  }
}
```

**渲染函数 / JSX 中使用：** 返回一个函数

```javascript
import { ref, h } from 'vue'
export default {
  name: 'App',
  setup() {
    const count = ref(0)
    return () => h('div', [count.value])
  }
}
```

## reactive 和 ref

这两个函数构成 vue 3.0 最重要的响应式代理。

### reactive

接受一个对象返回一个响应式代理，等同于 vue 2.x 中 `Vue.observable()`。

```javascript
<template>
  <div>
    <div>{{state.count}}</div>
  </div>
</template>

<script>
import { reactive } from 'vue'
export default {
  setup() {
    const state = reactive({
      count: 0
    })
    setTimeout(() => {
      state.count++
    }, 2000)
    return {
      state
    }
  }
}
```

注意：state 不能解构返回，不然会使其失去响应式

### ref

接受一个参数值返回一个可改变的响应式对象，且对象拥有唯一的属性 `value` 可改变。

```javascript
<template>
  <div>
    <div>{{ count }}</div>
  </div>
</template>

<script>
import { ref } from 'vue'
export default {
  setup() {
    const count = ref(0)
    setTimeout(() => {
      count.value++
    }, 2000)
    return {
      count
    }
  }
}
</script>
```


## toRefs 和 toRef

**toRefs**把一个响应对象转变为普通对象，该普通对象的每个 property 都是一个 ref ，和响应式对象 property 一一对应

```javascript
const state = reactive({
  count: 1
})
const { count } = toRefs(state)
console.log(count.value) // 1
```
**toRef** 用来为一个 reactive 对象的属性创建一个 ref。这个 ref 可以被传递并且能够保持响应性

```javascript
const state = reactive({
  count: 1
})
const countRef = toRef(state, 'count')
console.log(countRef.value) // 1
```

## computed

计算属性基本和 vue 2.x 中计算属性相同，传入一个 `getter` 函数，返回一个不可手动修改的 `ref` 对象

```javascript
<script>
import { ref, computed } from 'vue'
export default {
  setup() {
    const state = reactive({
      count: 1
    })
    const double = computed(() => state.count * 4);
    console.log(double.value) // 4
    setTimeout(() => {
      state.count++
    }, 2000)
    return {
      state,
      double
    }
  }
}
</script>

```

如果需要手动修改可以传入一个 `get` 和 `set` 函数的对象

```javascript
setup() {
    const count = ref(1);
    const double = computed({
      get: () => count.value + 1,
      set: (val) => {
        count.value = val - 1
      }
    });
    console.log(double.value) // 1
    setTimeout(() => {
      count.value++
    }, 2000)
    double.value = 1
		console.log(count.value) // 0
  }
```

## watchEffect

立即执行传入的函数，并响应式追踪依赖，并在依赖变化时，再次执行该函数

```javascript
const count = ref(0)

watchEffect(() => console.log(count.value)) // 0

setTimeout(() => {
  count.value++
  // 1
}, 1000)
```

也可以调用返回值，来停止追踪

```javascript
const count = ref(0)

const stop = watchEffect(() => console.log(count.value)) // 0

stop()

```
有一些特殊逻辑需要在停止追踪前改变状态，可以使用异步函数来执行
```javascript
    const stop = watchEffect(async (cancel) => {
      await new Promise(resolve => {
        console.log(count.value, 'watchEffect')
        resolve();
      })
      cancel(() => {
        console.log('cancel');
      })
    })
```
## watch
完全等效于 `vue 2.x` 中 `vm.$watch`
```javascript
watch(count, (val, oldVal) => {
	console.log(val, oldVal)
})
```
也可以监听多个
```javascript
watch([count, double], ([countVal, doubleVal], [countOldVal, doubleOldVal]) => {
	console.log(countVal, doubleVal)
})
```
