<h1 align="center">ð keep-alive åçåæ</h1>

å¨æä»¬çå¹³æ¶å¼åå·¥ä½ä¸­ï¼æå¾å¤ç»ä»¶æ²¡æå¿è¦å¤æ¬¡åå§åï¼éè¿ç¼å­ç»ä»¶çç¶æåå°æ§è½ä¸çå¼éãè vue çç»ä»¶ç¼å­ä¼ååä½¿ç¨åç½®ç»ä»¶ <keep-alive>

### åºæ¬ç¨æ³

```js
<keep-alive>
  <component />
</keep-alive>
```
ä¸é¢ä»£ç ç¤ºä¾è¯´æè¢« keep-alive åå«çç»ä»¶å·²ç»æä¸ºè¢«ç¼å­çç»ä»¶ï¼åªæ¸²æä¸æ¬¡ï¼ä¹å°±æ¯è¯´çå½å¨æä¹ä¸èµ·ä»»ä½ä½ç¨ãä½æ¯å¦æä½ æ³è¦éæ°å¨æä¸ä¸ªæ¶æºæ³è¦éæ°æ¸²æï¼keep-alive åç½®ç»ä»¶æä¾äºä¸¤ä¸ªé©å­å½æ°

- activated å½ keepalive åå«çç»ä»¶åæ¬¡æ¸²æçæ¶åè§¦å
- deactivated å½ keepalive åå«çç»ä»¶éæ¯çæ¶åè§¦å

vue å¯¹ keep-alive åç½®ç»ä»¶è¿æä¾äº 3ä¸ªå±æ§ä½ä¸ºåæ°è¿è¡å¹éå¯¹åºçç»ä»¶è¿è¡ç¼å­

- include éè¿å­ç¬¦ä¸²ãæ°ç»ãæ­£åè¡¨è¾¾å¼è¿è¡å¹éï¼å¹éå°çç»ä»¶ä¼è¿è¡ç¼å­
- exclude éè¿å­ç¬¦ä¸²ãæ°ç»ãæ­£åè¡¨è¾¾å¼è¿è¡å¹éï¼å¹éå°çç»ä»¶ä¸ä¼è¿è¡ç¼å­
- max éè¿è®¾ç½®å­ç¬¦æèæ°å­æ§å¶ç»ä»¶ç¼å­çæå¤§ä¸ªæ°

```js
// åªç¼å­aæèbç»ä»¶
<keep-alive include="a,b"> 
  <component />
</keep-alive>

// cç»ä»¶ä¸è¢«ç¼å­
<keep-alive exclude="c"> 
  <component />
</keep-alive>

// ç¼å­çæå¤§ä¸ªæ°
<keep-alive  max="5"> 
  <component />
</keep-alive>

// å¦æåæ¶ä½¿ç¨ exclude ä¼åçº§é«äº include æä»¥åªç¼å­ a,c ç»ä»¶
<keep-alive include="a,b,c" exclude="b"> 
  <component />
</keep-alive>
```
### éå router

```js
<keep-alive :include="cacheList">
  <router-view />
</keep-alive>
```
åªæè·¯å¾å¹éå°ç name æ¯ cacheList æ¹æ³ä¸­è¿åçç»ä»¶åç§°å°±ä¼è¢«ç¼å­ï¼å½ç¶ä¹å¯ä»¥ææè·¯ç±é½ç¼å­

```js
<keep-alive>
  <router-view>
      <!-- ææè·¯å¾å¹éå°çè§å¾ç»ä»¶é½ä¼è¢«ç¼å­ï¼ -->
  </router-view>
</keep-alive>
```

ä¹å¯ä»¥ä½¿ç¨ meta å±æ§åç¬æ§å¶è·¯ç±çç¼å­

```js
// routes éç½®
export default [
  {
    path: '/',
    name: 'home',
    component: Home,
    meta: {
      keepAlive: true // éè¦è¢«ç¼å­
    }
  }, {
    path: '/user-registration',
    name: 'user-registration',
    component: UserRegistrationManager,
    meta: {
      keepAlive: false // ä¸éè¦è¢«ç¼å­
    }
  }
]
```

```js
<keep-alive>
  <router-view v-if="$route.meta.keepAlive">
    <!-- è¿éæ¯ä¼è¢«ç¼å­çè§å¾ç»ä»¶ï¼æ¯å¦ Homeï¼ -->
  </router-view>
</keep-alive>

<router-view v-if="!$route.meta.keepAlive">
  <!--   -->
</router-view>
```

### æºç çè§£

éè¿ä¸é¢çä¾å­æä»¬å·²ç»åºæ¬çäºè§£äº keep-alive çä½¿ç¨ï¼æ¥ä¸æ¥æä»¬éè¿æºç æ¥åæå®çå®ç°ï¼å®å®ä¹å¨`å®ä¹å¨ src/core/components/keep-alive.js`

```js
export default {
  name: 'keep-alive,
  abstract: true,

  props: {
    include: patternTypes,
    exclude: patternTypes,
    max: [String, Number]
  },

  created () {
    this.cache = Object.create(null)
    this.keys = []
  },

  destroyed () {
    for (const key in this.cache) {
      pruneCacheEntry(this.cache, key, this.keys)
    }
  },

  mounted () {
    this.$watch('include', val => {
      pruneCache(this, name => matches(val, name))
    })
    this.$watch('exclude', val => {
      pruneCache(this, name => !matches(val, name))
    })
  },

  render () {
    const slot = this.$slots.default
    const vnode: VNode = getFirstComponentChild(slot)
    const componentOptions: ?VNodeComponentOptions = vnode && vnode.componentOptions
    if (componentOptions) {
      // check pattern
      const name: ?string = getComponentName(componentOptions)
      const { include, exclude } = this
      if (
        // not included
        (include && (!name || !matches(include, name))) ||
        // excluded
        (exclude && name && matches(exclude, name))
      ) {
        return vnode
      }

      const { cache, keys } = this
      const key: ?string = vnode.key == null
        // same constructor may get registered as different local components
        // so cid alone is not enough (#3269)
        ? componentOptions.Ctor.cid + (componentOptions.tag ? `::${componentOptions.tag}` : '')
        : vnode.key
      if (cache[key]) {
        vnode.componentInstance = cache[key].componentInstance
        // make current key freshest
        remove(keys, key)
        keys.push(key)
      } else {
        cache[key] = vnode
        keys.push(key)
        // prune oldest entry
        if (this.max && keys.length > parseInt(this.max)) {
          pruneCacheEntry(cache, keys[0], keys, this._vnode)
        }
      }

      vnode.data.keepAlive = true
    }
    return vnode || (slot && slot[0])
  }
}
```

éè¿ä¸é¢æºç æä»¬å¯ä»¥çå°éè¿ getFirstComponentChild å½æ°è·åç¬¬ä¸ä¸ªå­èç¹ï¼<keep-alive> åªå¤çç¬¬ä¸ä¸ªå­åç´ ï¼æä»¥ä¸è¬åå®æ­éä½¿ç¨çæ component å¨æç»ä»¶æèæ¯ router-viewï¼ä¹ååæ ¹æ®ä¼ ç include å excludeï¼ ç¨ matches å»åå¹éï¼å¦æå½ä¸­ç¼å­åç´æ¥ä»ç¼å­ä¸­æ¿ vnode ç»ä»¶å®ä¾ï¼éæ°è°æ´ key çé¡ºåºï¼æ¾å¨æåä¸ä¸ªï¼å¦åéæ°æ¾è¿ç¼å­ï¼å¦æéç½®äº max å¹¶ä¸ç¼å­çé¿åº¦è¶è¿äº this.max åè¦ä»ç¼å­ä¸­å é¤ç¬¬ä¸ä¸ª

```js
function pruneCacheEntry (
  cache: VNodeCache,
  key: string,
  keys: Array<string>,
  current?: VNode
) {
  const cached = cache[key]
  if (cached && (!current || cached.tag !== current.tag)) {
    cached.componentInstance.$destroy()
  }
  cache[key] = null 
  remove(keys, key)
}
```


### é¦æ¬¡æ¸²æ
æä»¬åé¢ä»ç»è¿æ¸²ææåå¨ patch è¿ç¨ä¸­ä¼æ§è¡ createComponent æ¹æ³

```js
function createComponent (vnode, insertedVnodeQueue, parentElm, refElm) {
  let i = vnode.data
  if (isDef(i)) {
    const isReactivated = isDef(vnode.componentInstance) && i.keepAlive
    if (isDef(i = i.hook) && isDef(i = i.init)) {
      i(vnode, false /* hydrating */)
    }
    // after calling the init hook, if the vnode is a child component
    // it should've created a child instance and mounted it. the child
    // component also has set the placeholder vnode's elm.
    // in that case we can just return the element and be done.
    if (isDef(vnode.componentInstance)) {
      initComponent(vnode, insertedVnodeQueue)
      insert(parentElm, vnode.elm, refElm)
      if (isTrue(isReactivated)) {
        reactivateComponent(vnode, insertedVnodeQueue, parentElm, refElm)
      }
      return true
    }
  }
}
```

å½ vnode å·²ç»æ§è¡å® patch åï¼æ§è¡ initComponent å½æ°ï¼

```js
function initComponent (vnode, insertedVnodeQueue) {
  if (isDef(vnode.data.pendingInsert)) {
    insertedVnodeQueue.push.apply(insertedVnodeQueue, vnode.data.pendingInsert)
    vnode.data.pendingInsert = null
  }
  vnode.elm = vnode.componentInstance.$el
  if (isPatchable(vnode)) {
    invokeCreateHooks(vnode, insertedVnodeQueue)
    setScope(vnode)
  } else {
    // empty component root.
    // skip all element-related modules except for ref (#3455)
    registerRef(vnode)
    // make sure to invoke the insert hook
    insertedVnodeQueue.push(vnode)
  }
}
```

è¿éä¼æ vnode.elm ç¼å­äº vnode åå»ºçæç DOM èç¹ãæä»¥å¯¹äºé¦æ¬¡æ¸²æèè¨ï¼é¤äºå¨ <keep-alive> ä¸­å»ºç«ç¼å­ï¼åæ®éç»ä»¶æ¸²ææ²¡ä»ä¹åºå«ã

### ç¼å­æ¸²æ

å½æ°æ®åéååï¼å¨ patch æ§è¡ patchVnode çé»è¾,å¯¹æ¯æ°æ§èç¹ä¹å ä¼æ§è¡ prepatch å½æ°,å®ä¹å¨ `src/core/vdom/create-component` ä¸­

```js
const componentVNodeHooks = {
  prepatch (oldVnode: MountedComponentVNode, vnode: MountedComponentVNode) {
    const options = vnode.componentOptions
    const child = vnode.componentInstance = oldVnode.componentInstance
    updateChildComponent(
      child,
      options.propsData, // updated props
      options.listeners, // updated listeners
      vnode, // new parent vnode
      options.children // new children
    )
  },
  // ...
}
```

å®ä¼æ§è¡ updateChildComponent æ¹æ³ï¼å®ä¹å¨ `src/core/instance/lifecycle.js` ä¸­

```js
export function updateChildComponent (
  vm: Component,
  propsData: ?Object,
  listeners: ?Object,
  parentVnode: MountedComponentVNode,
  renderChildren: ?Array<VNode>
) {
  const hasChildren = !!(
    renderChildren ||          
    vm.$options._renderChildren ||
    parentVnode.data.scopedSlots || 
    vm.$scopedSlots !== emptyObject 
  )

  // ...
  if (hasChildren) {
    vm.$slots = resolveSlots(renderChildren, parentVnode.context)
    vm.$forceUpdate()
  }
}
```

ä¸é¢çå°å¨æ§è¡è¿ä¸­ä¼ è§¦å <keep-alive> ç»ä»¶å®ä¾ $forceUpdate é»è¾ï¼ä¹å°±æ¯éæ°æ§è¡ <keep-alive> ç render æ¹æ³ï¼å¦æè¿ä¸ªæ¶åå½ä¸­ç¼å­ï¼åç´æ¥è¿å vnode.componentInstance ï¼åæ¬¡æ§è¡ createComponent 

```js
unction createComponent (vnode, insertedVnodeQueue, parentElm, refElm) {
  let i = vnode.data
  if (isDef(i)) {
    const isReactivated = isDef(vnode.componentInstance) && i.keepAlive
    if (isDef(i = i.hook) && isDef(i = i.init)) {
      i(vnode, false /* hydrating */)
    }
    // after calling the init hook, if the vnode is a child component
    // it should've created a child instance and mounted it. the child
    // component also has set the placeholder vnode's elm.
    // in that case we can just return the element and be done.
    if (isDef(vnode.componentInstance)) {
      initComponent(vnode, insertedVnodeQueue)
      insert(parentElm, vnode.elm, refElm)
      if (isTrue(isReactivated)) {
        reactivateComponent(vnode, insertedVnodeQueue, parentElm, refElm)
      }
      return true
    }
  }
}
```

è¿ä¸ªæ¶å isReactivated ä¸º trueï¼å°±ä¼æ§è¡ reactivateComponent æ¹æ³

```js
unction reactivateComponent (vnode, insertedVnodeQueue, parentElm, refElm) {
  let i
  // hack for #4339: a reactivated component with inner transition
  // does not trigger because the inner node's created hooks are not called
  // again. It's not ideal to involve module-specific logic in here but
  // there doesn't seem to be a better way to do it.
  let innerNode = vnode
  while (innerNode.componentInstance) {
    innerNode = innerNode.componentInstance._vnode
    if (isDef(i = innerNode.data) && isDef(i = i.transition)) {
      for (i = 0; i < cbs.activate.length; ++i) {
        cbs.activate[i](emptyNode, innerNode)
      }
      insertedVnodeQueue.push(innerNode)
      break
    }
  }
  // unlike a newly created component,
  // a reactivated keep-alive component doesn't insert itself
  insert(parentElm, vnode.elm, refElm)
}
```

è¿ä¸ªæ¶åä¼æ§è¡ insert(parentElm, vnode.elm, refElm) å°±æç¼å­ç DOM å¯¹è±¡ç´æ¥æå¥å°ç®æ åç´ ä¸­ï¼æä»¥å°±ä¸ä¼å¨æ§è¡ç»ä»¶ç createdãmounted ç­é©å­å½æ°äºã

<keep-alive> ç»ä»¶çé©å­å½æ° activated å deactivated,åæ¯å®ä¹å¨ insert æ¹æ³ä¸­,å®ä¹å¨ `src/core/vdom/create-component.js` 

```js
const componentVNodeHooks = {
  insert (vnode: MountedComponentVNode) {
    const { context, componentInstance } = vnode
    if (!componentInstance._isMounted) {
      componentInstance._isMounted = true
      callHook(componentInstance, 'mounted')
    }
    if (vnode.data.keepAlive) {
      if (context._isMounted) {
        // vue-router#1212
        // During updates, a kept-alive component's child components may
        // change, so directly walking the tree here may call activated hooks
        // on incorrect children. Instead we push them into a queue which will
        // be processed after the whole patch process ended.
        queueActivatedComponent(componentInstance)
      } else {
        activateChildComponent(componentInstance, true /* direct */)
      }
    }
  },
  // ...
}
```

éè¿å¤æ­åè£¹ç»ä»¶æ¯å¦è¢« mounted åå«æ§è¡ queueActivatedComponent(componentInstance) å activateChildComponent(componentInstance, true)

å¨ä¸è¿°ä¸¤ä¸ªæ¹æ³ä¸­ä¼æ§è¡ callHook(vm, 'activated') é©å­å½æ°ï¼å¯ä¸ä¸åç¹æ¯ queueActivatedComponent æ¯ç­ææçæ¸²æå®æ¯ï¼å¨ nextTickåä¼æ§è¡ flushSchedulerQueueï¼éè¿éåçæ¹å¼å°±æ¯ææ´ä¸ª activated æ¶æºå»¶åçï¼èç»ä»¶ç deactivated é©å­ï¼ä¹æ¯åçè¿è¡éå½éæ¯ã













