---
title: Vuex 通俗版教程
date: 2017-03-16 13:56:48
tags: Vue Vuex
---

本文基本上是[官方教程](http://vuex.vuejs.org/zh-cn/intro.html)的盗版，用通俗易懂的文字讲解**Vuex**，也对原文内容有删减。

如果你对以上声明不介意，那么就可以继续看本文，希望对你有所帮助。

<!-- more -->

学习一个新技术，必须要清楚两个**W**，"What && Why"。

"XX 是什么？"，"为什么要使用 XX ，或者说 XX 有什么好处"，最后才是"XX 怎么使用"。

## Vuex是什么？

Vuex 类似 Redux 的**状态管理器**，用来管理**Vue**的所有组件状态。

## 为什么使用Vuex？

当你打算开发大型单页应用（SPA），会出现多个视图组件依赖同一个状态，来自不同视图的行为需要变更同一个状态。

遇到以上情况时候，你就应该考虑使用Vuex了，它能把组件的共享状态抽取出来，当做一个全局单例模式进行管理。这样不管你在何处改变状态，都会通知使用该状态的组件做出相应修改。

下面讲解如何使用Vuex。

## 最简单的Vuex示例

本文就不涉及如何安装Vuex，直接通过代码讲解。

```
import Vue from 'vue';
import Vuex form 'vuex';

Vue.use(Vuex);

const store = new Vuex.Store({
    state: {
        count: 0
    },
    mutations: {
        increment (state) {
            state.count++
        }
    }
})
```

以上就是一个最简单的Vuex，每一个Vuex应用就是一个store，在store中包含组件中的共享**状态**`state`和改变状态的**方法**（暂且称作方法）`mutations`。

需要注意的是只能通过`mutations`改变store的`state`的状态，不能通过`store.state.count = 5;`直接更改，`state`相当于对外的只读属性。

使用`store.commit`方法触发`mutations`改变`state`:
```
store.commit('increment');

console.log(store.state.count)  // 1
```

一个简简单单的Vuex应用就实现了。

## 在Vue组件使用Vuex

如果希望Vuex状态更新，相应的Vue组件也得到更新，最简单的方法就是在Vue的`computed`（计算属性）获取`state`

```
// Counter 组件
const Counter = {
    template: `<div>{{ count }}</div>`,
    computed: {
        count () {
            return store.state.count;
        }
    }
}
```

上面的例子是直接操作全局状态`store.state.count`，那么每个使用该Vuex的组件都要引入。为了解决这个，Vuex通过`store`选项，提供了一种机制将状态从根组件**注入**到每一个子组件中。

```
// 根组件
import Vue from 'vue';
import Vuex form 'vuex';

Vue.use(Vuex);
const app = new Vue({
    el: '#app',
    store,
    components: {
        Counter
    },
    template: `
        <div class="app">
            <counter></counter>
        </div>
    `
})
```

通过这种**注入**机制，就能在子组件`Counter`通过`this.$store`访问：
```
// Counter 组件
const Counter = {
    template: `<div>{{ count }}</div>`,
    computed: {
        count () {
            return this.$store.state.count
        }
    }
}
```

### mapState函数

```
computed: {
    count () {
        return this.$store.state.count
    }
}
```
这样通过`count`计算属性获取同名`state.count`属性，是不是显得太重复了，我们可以使用`mapState`函数简化这个过程。
```
import { mapState } from 'vuex';

export default {
    computed: mapState ({
        count: state => state.count,
        countAlias: 'count',    // 别名 `count` 等价于 state => state.count
    })
}
```
还有更简单的使用方法： 
```
computed: mapState([
  // 映射 this.count 为 store.state.count
  'count'
])
```

### Getters对象

如果我们需要对`state`对象进行做处理计算，如下：
```
computed: {
    doneTodosCount () {
        return this.$store.state.todos.filter(todo => todo.done).length
    }
}
```
如果多个组件都要进行这样的处理，那么就要在多个组件中复制该函数。这样是很没有效率的事情，当这个处理过程更改了，还有在多个组件中进行同样的更改，这就更加不易于维护。

Vuex中`getters`对象，可以方便我们在`store`中做集中的处理。Getters接受`state`作为第一个参数：

```
const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
})
```
**在Vue中通过`store.getters`对象调用。**
```
computed: {
  doneTodos () {
    return this.$store.getters.doneTodos
  }
}
```

Getter也可以接受其他**getters**作为第二个参数：
```
getters: {
  doneTodos: state => {
      return state.todos.filter(todo => todo.done)
  },
  doneTodosCount: (state, getters) => {
    return getters.doneTodos.length
  }
}
```

### mapGetters辅助函数

与`mapState`类似，都能达到简化代码的效果。`mapGetters`辅助函数仅仅是将store中的getters映射到局部计算属性：
```
import { mapGetters } from 'vuex'

export default {
  // ...
  computed: {
    // 使用对象展开运算符将 getters 混入 computed 对象中
    ...mapGetters([
      'doneTodosCount',
      'anotherGetter',
      // ...
    ])
  }
}
```
上面也可以写作：

```
computed: mapGetters([
      'doneTodosCount',
      'anotherGetter',
      // ...
    ])
```

所以在Vue的`computed`计算属性中会存在两种辅助函数：
```
import { mapState, mapGetters } form 'vuex';

export default {
    // ...
    computed: {
        mapState({ ... }),
        mapGetter({ ... })
    }
}
```

### Mutations

之前也说过了，更改Vuex的store中的状态的唯一方法就是`mutations`。

每一个`mutation`都有一个**事件类型**`type`和一个**回调函数**`handler`。

```
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // 变更状态
      state.count++
    }
  }
})
```
调用`mutation`，需要通过`store.commit`方法调用`mutation type`：
```
store.commit('increment')
```

#### Payload 提交载荷

也可以向`store.commit`传入第二参数，也就是mutation的`payload`:

```
mutaion: {
    increment (state, n) {
        state.count += n;
    }
}

store.commit('increment', 10);
```
单单传入一个`n`，可能并不能满足我们的业务需要，这时候我们可以选择传入一个`payload`对象：
```
mutation: {
    increment (state, payload) {
        state.totalPrice += payload.price + payload.count;
    }
}

store.commit({
    type: 'increment',
    price: 10,
    count: 8
})
```

#### mapMutations函数

不例外，mutations也有映射函数`mapMutations`，帮助我们简化代码，使用`mapMutations`辅助函数将组件中的`methods`映射为`store.commit`调用。

```
import { mapMutations } from 'vuex'

export default {
  // ...
  methods: {
    ...mapMutations([
      'increment' // 映射 this.increment() 为 this.$store.commit('increment')
    ]),
    ...mapMutations({
      add: 'increment' // 映射 this.add() 为 this.$store.commit('increment')
    })
  }
}
```

**注** Mutations必须是同步函数。

如果我们需要异步操作，Mutations就不能满足我们需求了，这时候我们就需要`Actions`了。

### Aciton

相信看完之前的Vuex的内容，你就已经入门了。那么**Action**就自己进行学习吧（Action有点复杂，我还需要时间消化）。

## 结语

上个月看Vuex还是一头雾水，现在看来Vuex也是很容易理解的。

学习一门新技术最重要的就是实践，单单看教程和demo是远远不够的。

前端路途漫漫，共勉。