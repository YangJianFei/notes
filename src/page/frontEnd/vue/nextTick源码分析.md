## nextTick源码解析
转载：https://segmentfault.com/a/1190000020049857

## 1 nextTick的使用
vue中dom的更像并不是实时的，当数据改变后，vue会把渲染watcher添加到异步队列，异步执行，同步代码执行完成后再统一修改dom，我们看下面的代码。

```
<template>
  <div class="box">{{msg}}</div>
</template>

export default {
  name: 'index',
  data () {
    return {
      msg: 'hello'
    }
  },
  mounted () {
    this.msg = 'world'
    let box = document.getElementsByClassName('box')[0]
    console.log(box.innerHTML) // hello
  }
}
```

可以看到，修改数据后并不会立即更新dom ，dom的更新是异步的，无法通过同步代码获取，需要使用nextTick，在下一次事件循环中获取。

```
this.msg = 'world'
let box = document.getElementsByClassName('box')[0]
this.$nextTick(() => {
  console.log(box.innerHTML) // world
})
```

如果我们需要获取数据更新后的dom信息，比如动态获取宽高、位置信息等，需要使用nextTick。

## 2 数据变化dom更新与nextTick的原理分析

### 2.1 数据变化

vue双向数据绑定依赖于ES5的Object.defineProperty，在数据初始化的时候，通过Object.defineProperty为每一个属性创建getter与setter，把数据变成响应式数据。对属性值进行修改操作时，如this.msg = world，实际上会触发setter。下面看源码，为方便越读，源码有删减。
数据改变触发set函数

```
Object.defineProperty(obj, key, {
  enumerable: true,
  configurable: true,
  // 数据修改后触发set函数 经过一系列操作 完成dom更新
  set: function reactiveSetter (newVal) {
    const value = getter ? getter.call(obj) : val
    if (getter && !setter) return
    if (setter) {
      setter.call(obj, newVal)
    } else {
      val = newVal
    }
    childOb = !shallow && observe(newVal)
    dep.notify() // 执行dep notify方法
  }
})
```

执行dep.notify方法

```
export default class Dep {
  constructor () {
    this.id = uid++
    this.subs = []
  }
  notify () {
    const subs = this.subs.slice()
    for (let i = 0, l = subs.length; i < l; i++) {
      // 实际上遍历执行了subs数组中元素的update方法
      subs[i].update()
    }
  }
}
```
当数据被引用时，如<div>{{msg}}</div> ，会执行get方法，并向subs数组中添加渲染Watcher，当数据被改变时执行Watcher的update方法执行数据更新。

```
update () {
  /* istanbul ignore else */
  if (this.lazy) {
    this.dirty = true
  } else if (this.sync) {
    this.run()
  } else {
    queueWatcher(this) //执行queueWatcher
  }
}
```
update 方法最终执行queueWatcher
```
function queueWatcher (watcher: Watcher) {
  const id = watcher.id
  if (has[id] == null) {
    has[id] = true
    if (!flushing) {
      queue.push(watcher)
    } else {
      // if already flushing, splice the watcher based on its id
      // if already past its id, it will be run next immediately.
      let i = queue.length - 1
      while (i > index && queue[i].id > watcher.id) {
        i--
      }
      queue.splice(i + 1, 0, watcher)
    }
    // queue the flush
    if (!waiting) {
      // 通过waiting 保证nextTick只执行一次
      waiting = true
      // 最终queueWatcher 方法会把flushSchedulerQueue 传入到nextTick中执行
      nextTick(flushSchedulerQueue)
    }
  }
}
```
执行flushSchedulerQueue方法
```
function flushSchedulerQueue () {
  currentFlushTimestamp = getNow()
  flushing = true
  let watcher, id
  ...
  for (index = 0; index < queue.length; index++) {
    watcher = queue[index]
    if (watcher.before) {
      watcher.before()
    }
    id = watcher.id
    has[id] = null
    // 遍历执行渲染watcher的run方法 完成视图更新
    watcher.run()
  }
  // 重置waiting变量 
  resetSchedulerState()
  ...
}
```
也就是说当数据变化最终会把flushSchedulerQueue传入到nextTick中执行flushSchedulerQueue函数会遍历执行watcher.run()方法，watcher.run()方法最终会完成视图更新，接下来我们看关键的nextTick方法到底是啥

### 2.2 nextTick

nextTick方法会被传进来的回调push进callbacks数组，然后执行timerFunc方法
```
export function nextTick (cb?: Function, ctx?: Object) {
  let _resolve
  // push进callbacks数组
  callbacks.push(() => {
     cb.call(ctx)
  })
  if (!pending) {
    pending = true
    // 执行timerFunc方法
    timerFunc()
  }
}
```
timerFunc
```
let timerFunc
// 判断是否原生支持Promise
if (typeof Promise !== 'undefined' && isNative(Promise)) {
  const p = Promise.resolve()
  timerFunc = () => {
    // 如果原生支持Promise 用Promise执行flushCallbacks
    p.then(flushCallbacks)
    if (isIOS) setTimeout(noop)
  }
  isUsingMicroTask = true
// 判断是否原生支持MutationObserver
} else if (!isIE && typeof MutationObserver !== 'undefined' && (
  isNative(MutationObserver) ||
  // PhantomJS and iOS 7.x
  MutationObserver.toString() === '[object MutationObserverConstructor]'
)) {
  let counter = 1
  // 如果原生支持MutationObserver 用MutationObserver执行flushCallbacks
  const observer = new MutationObserver(flushCallbacks)
  const textNode = document.createTextNode(String(counter))
  observer.observe(textNode, {
    characterData: true
  })
  timerFunc = () => {
    counter = (counter + 1) % 2
    textNode.data = String(counter)
  }
  isUsingMicroTask = true
// 判断是否原生支持setImmediate 
} else if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
  timerFunc = () => {
  // 如果原生支持setImmediate  用setImmediate执行flushCallbacks
    setImmediate(flushCallbacks)
  }
// 都不支持的情况下使用setTimeout 0
} else {
  timerFunc = () => {
    // 使用setTimeout执行flushCallbacks
    setTimeout(flushCallbacks, 0)
  }
}

// flushCallbacks 最终执行nextTick 方法传进来的回调函数
function flushCallbacks () {
  pending = false
  const copies = callbacks.slice(0)
  callbacks.length = 0
  for (let i = 0; i < copies.length; i++) {
    copies[i]()
  }
}
```
nextTick会优先使用microTask, 其次是macroTask 。

也就是说nextTick中的任务，实际上会异步执行，nextTick(callback)类似于
Promise.resolve().then(callback)，或者setTimeout(callback, 0)。

也就是说vue的视图更新 nextTick(flushSchedulerQueue)等同于setTimeout(flushSchedulerQueue, 0)，会异步执行flushSchedulerQueue函数，所以我们在this.msg = hello 并不会立即更新dom。

要想在dom更新后读取dom信息，我们需要在本次异步任务创建之后创建一个异步任务。

异步队列

为了验证这个想法我们不用nextTick，直接用setTimeout实验一下。如下面代码，验证了我们的想法。

```
<template>
  <div class="box">{{msg}}</div>
</template>

<script>
export default {
  name: 'index',
  data () {
    return {
      msg: 'hello'
    }
  },
  mounted () {
    this.msg = 'world'
    let box = document.getElementsByClassName('box')[0]
    setTimeout(() => {
      console.log(box.innerHTML) // world
    })
  }
}
```

如果我们在数据修改前nextTick ，那么我们添加的异步任务会在渲染的异步任务之前执行，拿不到更新后的dom。

```
<template>
  <div class="box">{{msg}}</div>
</template>

<script>
export default {
  name: 'index',
  data () {
    return {
      msg: 'hello'
    }
  },
  mounted () {
    this.$nextTick(() => {
      console.log(box.innerHTML) // hello
    })
    this.msg = 'world'
    let box = document.getElementsByClassName('box')[0]
  }
}
```

## 3 总结

vue为了保证性能，会把dom修改添加到异步任务，所有同步代码执行完成后再统一修改dom，一次事件循环中的多次数据修改只会触发一次watcher.run()。也就是通过nextTick，nextTick会优先使用microTask创建异步任务。vue项目中如果需要获取修改后的dom信息，需要通过nextTick在dom更新任务之后创建一个异步任务。如官网所说，nextTick会在下次 DOM 更新循环结束之后执行延迟回调。