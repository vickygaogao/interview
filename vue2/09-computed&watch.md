<h1 align="center">ð computed å watch åçåæ</h1>

vue ç computed å watch æ¯æä»¬å¨å¼åä¸­ç»å¸¸ç¨çæ¹æ³ï¼ä»èå®ç°æ°æ®çååºä¸çå¬ï¼ä¸é¢æä»¬ä»ä½¿ç¨åæºç æ¥åæä¸ä¸è¿ä¸¤ä¸ªå±æ§çå®ç°åçæ¯ä»ä¹ã

### computed å watch ä½¿ç¨<hr>

> computed è®¡ç®å±æ§ï¼æ ¹æ®ä¾èµçæ°æ®å¨ææ¾ç¤ºæ°çè®¡ç®ç»æãå½ä¾èµçå±æ§å¼æ¹åä¹åï¼éè¿è°ç¨å¯¹åºç getter æ¥è®¡ç®å¹¶è¿è¡ç¼å­ï¼è®¡ç®çå±æ§åç§°ä¸è½å¨ç»ä»¶ç props å data ä¸­å®ä¹ï¼å¦åä¼æ¥éã

```js
<template>
  <div class="hello">
    {{fullName}}
  </div>
</template>

<script>
export default {
    data() {
      return {
        firstName: 'ren',
        lastName: "bo"
      }
    },
    computed: {
      fullName() {
        return this.firstName + ' ' + this.lastName
      }
    }
}
</script>
```

> watcher ä¾¦å¬å±æ§ï¼å½ä¾èµç data çæ°æ®ååï¼æ§è¡åè°æ¹æ³ï¼å¨æ¹æ³ä¸­ä¼ å¥ newVal å oldValãVue å®ä¾å°ä¼å¨å®ä¾åæ¶è°ç¨ $watch()ï¼éå watch å¯¹è±¡çæ¯ä¸ä¸ªå±æ§ãæ¥å®ç°æ°æ®ççå¬åè§å¯ï¼å¹¶å¨ä½¿ç¨watch çæ¶åå¯ä»¥æ§è¡å¼æ­¥æä½ï¼è®¾ç½®ä¸­é´ç¶æç­ã

```js
<template>
  <div class="hello">
    {{fullName}}
    <button @click="handleChangName">æ¹åå§å</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      firstName: 'ren',
      lastName: "bo",
      fullName: ''
    }
  },
  methods: {
    handleChangName() {
      this.firstName = "zhang";
      this.lastName = "san"
    }
  },
  watch: {
    firstName: {
      handler(newval, oldval) {
        console.log(newval)
        console.log(oldval)
        this.fullName = newval + ' ' + this.lastName
      },
      immediate: true
    }
  }
}
</script>
```

watch åªæå¨æ°æ®åçååçæ¶åï¼æ§è¡åè°æ¹æ³ï¼ä½æ¯åå§åçæ¶åä¹å¯ä»¥æ§è¡ï¼å¦ä¸é¢ç firstName æ¹æ³ä¸­å¢å  handler å immediate ç«å³æ§è¡ï¼å·ä½ä¸ºä»ä¹ä¼ç«å³æ§è¡ï¼ç¨åå¨ååæï¼å¦ææä»¬è¦çå¬å¯¹è±¡ä¸­å±æ§çæ·»å åå é¤é£ä¹æä»¬å¿é¡»ä¸º watch ä¸­æ·»å  deep æ¥è¿è¡å¯¹è±¡æ·±åº¦éåï¼è¿è¡çå¬ï¼å¦ä¸æç¤º

```js
<template>
  <div class="hello">
    {{fullName}}
    <button @click="handleChangName">æ¹åå§å</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      person: {
        name: 'renbo',
        age: 26
      },
      fullName: '',
    }
  },
  methods: {
    handleChangName() {
      this.person.name = "zhangsan";
      this.person.age = 27
    }
  },
  watch: {
    person: {
      handler(newVal, oldVal) {
        this.fullName = newVal.name + newVal.age
      },
      immediate: true,
      deep: true
    }
  }
}
</script>
```

### computed å watch çå¼å<hr>

éè¿ä¸é¢çä½¿ç¨å¯¹æ¯

ç¸åç¹
- computed åwatch é½è½èµ·å°çå¬åä¾èµå¤çæ°æ®
- é½æ¯éè¿ vue ççå¬å¨å®ç°ç

ä¸åç¹
- computed ä¸»è¦ç¨äºå¯¹åæ­¥æ°æ®çå¤ç
- watch åä¸»è¦ç¨äºçå¬æä¸ªå¼çååå»å®æå¼éè¾å¤§çå¤æä¸å¡é»è¾

ä¸»è¦çåºç¨åºæ¯å°±æ¯å¦æä¸ä¸ªæ°æ®ä¾èµå¦ä¸ä¸ªæ°æ®é£ä¹å°±ç¨ computedï¼å¦æå¨çå¬æ°æ®åçååå»å®ç°æä¸äºåè½ï¼ç¶æï¼å¼æ­¥ç­é£å°±å°±ç¨ watchï¼ä½æ¯ç±äº watch ä½¿ç¨deepè¿è¡æ·±åº¦éåçå¬ä¼æ¶èæ§è½ï¼æä»¥è¿æ¯å°½éå°ç¨ 

### computed æ¬è´¨ -- computed watch<hr>

åé¢åæè¿ new Vue() çæ¶åä¼è°ç¨ _init æ¹æ³ï¼è¯¥æ¹æ³ä¼åå§åçå½å¨æï¼åå§åäºä»¶ï¼åå§årenderï¼åå§ådataï¼computedï¼methodsï¼wactherç­ç­ï¼è®¡ç®å±æ§çåå§åæ¯åçå¨ vue å®ä¾åå§åé¶æ®µç initState å½æ°ä¸­

```js
 export function initState (vm: Component) {
  vm._watchers = []
  const opts = vm.$options
  if (opts.props) initProps(vm, opts.props)
  if (opts.methods) initMethods(vm, opts.methods)
  if (opts.data) {
    initData(vm)
  } else {
    observe(vm._data = {}, true /* asRootData */)
  }
  if (opts.computed) initComputed(vm, opts.computed)
  if (opts.watch && opts.watch !== nativeWatch) {
    initWatch(vm, opts.watch)
  }
}
```
ä¸é¢è¿è¡äºå¤æ­ï¼æä»¬åæ¥çç opts.computed å­å¨çæåµæ§è¡ initComputed æ¹æ³ï¼è¯¥æ¹æ³å®ä¹å¨`src/core/instance/state.js` ä¸­

```js
// ç¨äºä¼ å¥ Watcher å®ä¾çä¸ä¸ªå¯¹è±¡ï¼å³computed watcher
const computedWatcherOptions = { lazy: true }
function initComputed (vm: Component, computed: Object) {
  // å£°æ watchersï¼åæ¶å¨å®ä¾ä¸æè½½ _computedWatchers
  const watchers = vm._computedWatchers = Object.create(null)
  // è®¡ç®å±æ§å¨ ssr æ¨¡å¼å¨åªè½ä½¿ç¨ getter æ¹æ³
  const isSSR = isServerRendering()

  for (const key in computed) {
    // éåååº computed å¯¹è±¡ä¸­çæ¯ä¸ªæ¹æ³å¹¶èµå¼ç» userDef
    const userDef = computed[key]
    const getter = typeof userDef === 'function' ? userDef : userDef.get
    if (process.env.NODE_ENV !== 'production' && getter == null) {
      warn(
        `Getter is missing for computed property "${key}".`,
        vm
      )
    }

    if (!isSSR) {
      // å¦æä¸æ¯SSRæå¡ç«¯æ¸²æï¼ååå»ºä¸ä¸ªwatcherå®ä¾
      watchers[key] = new Watcher(
        vm,
        getter || noop,
        noop,
        computedWatcherOptions
      )
    }

    // ç»ä»¶å®ä¹çè®¡ç®å±æ§å·²ç»å¨ç»ä»¶ååä¸å®ä¹ãæä»¬åªéè¦å®ä¹å¨å®ä¾åæ¶å®ä¹çè®¡ç®å±æ§ã
    if (!(key in vm)) {
      // å¦æcomputed ä¸­ç key æ²¡æè®¾ç½®å° vm ä¸­ï¼éè¿defineComputedå½æ°æè½½ä¸å»
      defineComputed(vm, key, userDef)
    } else if (process.env.NODE_ENV !== 'production') {
      // props å data ä¸­ä¸è½å computed ç key åå
      if (key in vm.$data) {
        warn(`The computed property "${key}" is already defined in data.`, vm)
      } else if (vm.$options.props && key in vm.$options.props) {
        warn(`The computed property "${key}" is already defined as a prop.`, vm)
      }
    }
  }
}
```

éè¿ä¸é¢æºç æä»¬åç°å®åå£°æäºä¸ä¸ªåä¸º watchers çç©ºå¯¹è±¡ï¼åæ¶å¨ vm ä¸ä¹æè½½äºè¿ä¸ªç©ºå¯¹è±¡ãæ¥çå¯¹ computed å¯¹è±¡åéåï¼æ¿å°æ¯ä¸ª userDefï¼å¦æ userDef æ¯ function çè¯å°±èµç» getterï¼å¦æä¸æ¯æå¡ç«¯æ¸²æï¼å°±ä¼ä¸ºè¿ä¸ª getter åå»ºä¸ä¸ª watcherï¼è¿ä¸ª watcher åæ¸²æ watcher æä¸ç¹å¾å¤§çä¸åï¼æä»¬å¨æ°å»ºå®ä¾çæ¶åä¼ å¥äºç¬¬åä¸ªåæ° computedWatcherOptionsï¼å¶å®ä¹å°±æ¯å¨ä»£ç çç¬¬ä¸è¡ï¼`const computedWatcherOptions = { lazy: true }`ï¼è¿ä¸ªå¯¹è±¡æ¯å®ç°computed watcherçå³é®ï¼è¿æ ·watcher ä¸­å°±åºç°äºååï¼watcher å®ä¹å¨ `src/core/observer/watcher.js`

```js
// ...

// options
if (options) {
  this.deep = !!options.deep
  this.user = !!options.user
  this.lazy = !!options.lazy
  this.sync = !!options.sync
  this.before = options.before
} else {
  this.deep = this.user = this.lazy = this.sync = false
}
this.cb = cb
this.id = ++uid // uid for batching
this.active = true
this.dirty = this.lazy // for lazy watchers

// ...
```

ä¸é¢ç this.lazy å®éä¸å°±æ¯ computedWatcherOptions ä¼ éè¿æ¥çï¼å° dirty ç¶ææ¹ä¸º true ä¹åå¨ createComputedGetter æ¹æ³ä¸­
```js
function createComputedGetter (key) {
  return function computedGetter () {
    const watcher = this._computedWatchers && this._computedWatchers[key]
    if (watcher) {
      if (watcher.dirty) {
        watcher.evaluate()
      }
      if (Dep.target) {
        watcher.depend()
      }
      return watcher.value
    }
  }
}

```

å¯ä»¥çå°ä¹åæ§è¡äº watcher.evaluate()ï¼éè¿è°ç¨ this.get() æ¹æ³å®éä¸å°±æ¯å¨æ§è¡ `value = this.getter.call(vm, vm)`ï¼è¿æ ·å°±è½æ§è¡è®¡ç®å±æ§å®ä¹ç getter å½æ°

```js
  evaluate () {
    this.value = this.get()
    this.dirty = false
  }
```
ä¹åå¨æ§è¡ watcher.depend() æèªèº«ææç dep æ·»å å°å½åæ­£å¨è®¡ç®ç watcher ä¸­ï¼è¿ä¸ªæ¶å Dep.target å°±æ¯è¿ä¸ª computed watcherï¼æåæ¿å°å½å watcher ç valueï¼å½è®¡ç®å±æ§ä¾èµçæ°æ®åçæ¹åçæ¶åï¼ä¼è§¦å setter è¿ç¨ï¼éç¥ææè®¢éå®ååç watcher è¿è¡æ´æ°ï¼æ§è¡ watcher.update() æ¹æ³

```js
update () {
    /* istanbul ignore else */
    if (this.lazy) {
      this.dirty = true
    } else if (this.sync) {
      this.run()
    } else {
      queueWatcher(this)
    }
  }
```

### watch çå·¥ä½åç<hr>
å computed ä¸æ ·ä¹æ¯å¨æ§è¡ initState çæ¶åæ§è¡äº `initWatch(vm, opts.watch)`
```js
 export function initState (vm: Component) {
   // ...
  if (opts.watch && opts.watch !== nativeWatch) {
    initWatch(vm, opts.watch)
  }
}
```
initWatch æ¹æ³å®ä¹å¨ `src/core/instance/state.js` ä¸­ 

```js
function initWatch (vm: Component, watch: Object) {
  for (const key in watch) {
    // éå watch åå° key 
    const handler = watch[key]
    if (Array.isArray(handler)) {
      // å ä¸º Vue æ¯æ¯æ watch çåä¸ä¸ª key å¯¹åºå¤ä¸ª handlerï¼æä»¥å¦æ handler æ¯ä¸ä¸ªæ°ç»ï¼åéåè¿ä¸ªæ°ç»
      for (let i = 0; i < handler.length; i++) {
        createWatcher(vm, key, handler[i])
      }
    } else {
      createWatcher(vm, key, handler)
    }
  }
}
```
```js
function createWatcher (
  vm: Component,
  expOrFn: string | Function,
  handler: any,
  options?: Object
) {
  if (isPlainObject(handler)) {
    options = handler
    handler = handler.handler
  }
  if (typeof handler === 'string') {
    handler = vm[handler]
  }
  return vm.$watch(expOrFn, handler, options)
}
```
éè¿å¯¹ hanlder çç±»ååå¤æ­ï¼æ¿å°å®æç»çåè°å½æ°ï¼æåè°ç¨ `vm.$watch(keyOrFn, handler, options)` å½æ°ï¼vm.$watch å®éä¸æ¯æ§è¡äº Vue.prototype.$watch

```js
Vue.prototype.$watch = function (
    expOrFn: string | Function,
    cb: any,
    options?: Object
  ): Function {
    const vm: Component = this
    if (isPlainObject(cb)) {
      return createWatcher(vm, expOrFn, cb, options)
    }
    options = options || {}
    options.user = true
    const watcher = new Watcher(vm, expOrFn, cb, options)
    if (options.immediate) {
      try {
        cb.call(vm, watcher.value)
      } catch (error) {
        handleError(error, vm, `callback for immediate watcher "${watcher.expression}"`)
      }
    }
    return function unwatchFn () {
      watcher.teardown()
    }
  }
```
éè¿ä¸é¢ä»£ç å¯ä»¥çå°ï¼ä¾¦å¬å±æ§ watch æç»ä¼è°ç¨ $watch æ¹æ³ï¼è¿ä¸ªæ¹æ³é¦åå¤æ­ cb å¦ææ¯ä¸ä¸ªå¯¹è±¡ï¼åè°ç¨ createWatcher æ¹æ³ï¼è¿æ¯å ä¸º $watch æ¹æ³æ¯ç¨æ·å¯ä»¥ç´æ¥è°ç¨çï¼å®å¯ä»¥ä¼ éä¸ä¸ªå¯¹è±¡ï¼ä¹å¯ä»¥ä¼ éå½æ°ãæ¥çæ§è¡ const watcher = new Watcher(vm, expOrFn, cb, options) å®ä¾åäºä¸ä¸ª watcherï¼è¿ééè¦æ³¨æä¸ç¹è¿æ¯ä¸ä¸ª user watcherï¼å ä¸º options.user = trueãéè¿å®ä¾å watcher çæ¹å¼ï¼ä¸æ¦æä»¬ watch çæ°æ®åéååï¼å®æç»ä¼æ§è¡ watcher ç run æ¹æ³ï¼æ§è¡åè°å½æ° cbï¼ä¸é¢è¿æä¸¤ç¹éè¦æ³¨æ å¦æåæ°ä¼ éç deep ï¼è¿æ ·å°±åå»ºäºä¸ä¸ª deep watcher åä¼æ§è¡

```js
get() {
  let value = this.getter.call(vm, vm)
  // ...
  if (this.deep) {
    traverse(value)
  }
}
```

æ§è¡ traverse å½æ°è¿è¡æ·±å±éå½éåï¼é£ä¹è¿æ ·å°±è§£å³äºæä»¬å¼ç¯æ¶ä½¿ç¨ watch è§å¯æ¹åä¸ä¸ªå¤æå¯¹è±¡ä¸èµ·ä½ç¨çé®é¢ï¼é£ä¹æ¯ watch ç«å³æ§è¡æ¯æä¹åå°çå¢

```js
// ...
if (options.immediate) {
  try {
    cb.call(vm, watcher.value)
  } catch (error) {
    handleError(error, vm, `callback for immediate watcher "${watcher.expression}"`)
  }
}
//...
```
éè¿è®¾ç½® immediate ä¸º trueï¼åç´æ¥ä¼æ§è¡åè°å½æ° cbãè¿æ ·å°±ä¼ç«å³æ§è¡ watch å±æ§



### æ»ç»<hr>

éè¿ä»¥ä¸çåæï¼æä»¬å¤§æ¦äºè§£äºè®¡ç®å±æ§æ¬è´¨ä¸æ¯ä¸ä¸ªcomputed watchï¼ä¾¦å¬å±æ§æ¬è´¨ä¸æ¯ä¸ä¸ªuser watchãè®¡ç®å±æ§éåå¨æ¨¡çæ¸²æä¸­æä¸ä¸ªå¼ä¾èµäºå¶ä»ååºå¯¹è±¡çè³æ¯è®¡ç®å±æ§è®¡ç®èæ¥ï¼èä¾¦å¬å±æ§éç¨äºè§æµæä¸ªå¼çååå»å®æä¸æ®µå¤æçä¸å¡é»è¾ã