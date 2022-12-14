
<h1 align="center">ð v-model åçåæ</h1>

å¨æ¥å¸¸å¼åä¸­æä»¬ä½¿ç¨ v-model æ¥å®ç°æ°æ®çååç»å®ï¼æ¥çä¸ä¸ä¸é¢çä¾å­

```js
 new Vue({
  el: '#app',
  template: '<div>'
  + '<input v-model="message" placeholder="è¯·è¾ message">' +
  '<span> message is: {{ message }}</span>' +
  '</div>',
  data() {
    return {
      message: ''
    }
  }
})
```

ä¸é¢çä¾å­å¶å®éå¸¸ç®åï¼å½æä»¬å¨è¾å¥æ¡ä¸­è¾å¥åå®¹çæ¶åï¼span åç´ ä¸­ä¹ä¼åæ­¥ååãé£å®æ¯æä¹åå°çå¢ï¼è®©æä»¬æ¥çä¸ä¸æºç ã

é¦åæ¯ parse é¶æ®µè°ç¨ genDirectives æ¹æ³ï¼è¯¥æ¹æ³å®ä¹å¨ `src/compiler/codegen/index.js` ä¸­

```js
function genDirectives (el: ASTElement, state: CodegenState): string | void {
  const dirs = el.directives
  if (!dirs) return
  let res = 'directives:['
  let hasRuntime = false
  let i, l, dir, needRuntime
  for (i = 0, l = dirs.length; i < l; i++) {
    dir = dirs[i]
    needRuntime = true
    const gen: DirectiveFunction = state.directives[dir.name]
    if (gen) {
      // compile-time directive that manipulates AST.
      // returns true if it also needs a runtime counterpart.
      needRuntime = !!gen(el, dir, state.warn)
    }
    if (needRuntime) {
      hasRuntime = true
      res += `{name:"${dir.name}",rawName:"${dir.rawName}"${
        dir.value ? `,value:(${dir.value}),expression:${JSON.stringify(dir.value)}` : ''
      }${
        dir.arg ? `,arg:"${dir.arg}"` : ''
      }${
        dir.modifiers ? `,modifiers:${JSON.stringify(dir.modifiers)}` : ''
      }},`
    }
  }
  if (hasRuntime) {
    return res.slice(0, -1) + ']'
  }
}
```

ä»æºç ä¸­å¯ä»¥çå° genDirectives æ¹æ³å°±æ¯éå el.directivesï¼ç¶åè°ç¨ `const gen: DirectiveFunction = state.directives[dir.name]` è·åæ¯ä¸ªæä»¤å¯¹åºçæ¹æ³ãè state åæ¯å®ä¾å CodegenState çæ¶åéè¿ option ä¼ å¥çãè¿ä¸ª option å°±æ¯ç¼è¯ç¸å³çéç½®ï¼å®å¨ä¸åçå¹³å°ä¸éç½®ä¸åï¼å¨ web ç¯å¢ä¸çå®ä¹å¨ `src/platforms/web/compiler/options.js ä¸`

```js
export const baseOptions: CompilerOptions = {
  expectHTML: true,
  modules,
  directives,
  isPreTag,
  isUnaryTag,
  mustUseProp,
  canBeLeftOpenTag,
  isReservedTag,
  getTagNamespace,
  staticKeys: genStaticKeys(modules)
}
```

directives å®ä¹å¨ `src/platforms/web/compiler/directives/index.js` ä¸­

```js
export default {
  model,
  text,
  html
}
```

é£ä¹å¯¹äº v-model èè¨ï¼å¯¹åºç directive å½æ°æ¯å¨ `src/platforms/web/compiler/directives/model.js` ä¸­å®ä¹ç model å½æ°


```js
export default function model (
  el: ASTElement,
  dir: ASTDirective,
  _warn: Function
): ?boolean {
  warn = _warn
  const value = dir.value
  const modifiers = dir.modifiers
  const tag = el.tag
  const type = el.attrsMap.type
  // ...

  if (el.component) {
    genComponentModel(el, value, modifiers)
    // component v-model doesn't need extra runtime
    return false
  } else if (tag === 'select') {
    genSelect(el, value, modifiers)
  } else if (tag === 'input' && type === 'checkbox') {
    genCheckboxModel(el, value, modifiers)
  } else if (tag === 'input' && type === 'radio') {
    genRadioModel(el, value, modifiers)
  } else if (tag === 'input' || tag === 'textarea') {
    genDefaultModel(el, value, modifiers)
  } else if (!config.isReservedTag(tag)) {
    genComponentModel(el, value, modifiers)
    // component v-model doesn't need extra runtime
    return false
  } else if (process.env.NODE_ENV !== 'production') {
    // ...
  }

  // ensure runtime directive metadata
  return true
}

```

ä»ä¸é¢å¯ä»¥çå° model å½æ°ä¼æ ¹æ® AST åç´ èç¹çä¸åæåµå»æ§è¡ä¸åçé»è¾ãæä»¬æ¥çä¸ genDefaultModel å½æ°

```js
function genDefaultModel (
  el: ASTElement,
  value: string,
  modifiers: ?ASTModifiers
): ?boolean {
  const type = el.attrsMap.type

  // ....

  const { lazy, number, trim } = modifiers || {}
  const needCompositionGuard = !lazy && type !== 'range'
  const event = lazy
    ? 'change'
    : type === 'range'
      ? RANGE_TOKEN
      : 'input'

  let valueExpression = '$event.target.value'
  if (trim) {
    valueExpression = `$event.target.value.trim()`
  }
  if (number) {
    valueExpression = `_n(${valueExpression})`
  }

  let code = genAssignmentCode(value, valueExpression)
  if (needCompositionGuard) {
    code = `if($event.target.composing)return;${code}`
  }

  addProp(el, 'value', `(${value})`)
  addHandler(el, event, code, null, true)
  if (trim || number) {
    addHandler(el, 'blur', '$forceUpdate()')
  }
}

```

genDefaultModel å½æ°ä¸»è¦æ¯éè¿ä¼ å¥ç modifiers å»å¤ç event å valueExpression çå¼ï¼ ç¶åè°ç¨ genAssignmentCodeãå®çå®ä¹å¨ `src/compiler/directives/model.js` ä¸­ 

```js
export function genAssignmentCode (
  value: string,
  assignment: string
): string {
  const res = parseModel(value)
  if (res.key === null) {
    return `${value}=${assignment}`
  } else {
    return `$set(${res.exp}, ${res.key}, ${assignment})`
  }
}
```

genAssignmentCode ä¸»è¦æ¯éè¿ parseModel æ¹æ³å¯¹ä¼ å¥ç value è¿è¡è§£æçæ codeãçæ code ä¹å genDefaultModel æ¹æ³æ§è¡äºä¸¤ä¸ªéå¸¸å³é®çæ¹æ³

```js
addProp(el, 'value', `(${value})`)
addHandler(el, event, code, null, true)
```

éè¿ä¿®æ¹ AST åç´ ï¼ç» el æ·»å ä¸ä¸ª propï¼ç¸å½äºæä»¬å¨ input ä¸å¨æç»å®äº valueï¼åç» el æ·»å äºäºä»¶å¤çï¼ç¸å½äºå¨ input ä¸ç»å®äº input äºä»¶ï¼å°±ç¸å½äºå¨æçç»å®äºå¼åäºä»¶ï¼è½¬æ¢æ¨¡çå¦ä¸

```js
<input
  v-bind:value="name"
  v-on:input="name=$event.target.value">
```

å½è·åå®æä»¤çæ¹æ³åï¼æä»¬å¨åå° `genDirectives` æ¹æ³ãå¦æéè¦è¿åè¿è¡æ¶å¯¹è±¡åè¿å needRuntime ä¸º ture ï¼å¼æçææ ¹æ® dir çæ resã

```js
  if (needRuntime) {
    hasRuntime = true
    res += `{name:"${dir.name}",rawName:"${dir.rawName}"${
      dir.value ? `,value:(${dir.value}),expression:${JSON.stringify(dir.value)}` : ''
    }${
      dir.arg ? `,arg:"${dir.arg}"` : ''
    }${
      dir.modifiers ? `,modifiers:${JSON.stringify(dir.modifiers)}` : ''
    }},`
  }
```

æåçæ with å¯æ§è¡ä»£ç ï¼å¯¹äºæä»¬çä¾å­çæå¦ä¸ codeã

```js
with(this) {
  return _c('div',[_c('input',{
    directives:[{
      name:"model",
      rawName:"v-model",
      value:(message),
      expression:"message"
    }],
    attrs:{"placeholder":"è¯·è¾å¥ message "},
    domProps:{"value":(message)},
    on:{"input":function($event){
      if($event.target.composing)
        return;
      message=$event.target.value
    }}}),_c('span',[_v("message is: "+_s(message))])
    ])
}

```

çå®äº v-model çè¯­æ³ç³ï¼æä»¬åæ¥çä¸ä¸æµç¨ä¸­çå³é®å½æ°

```js
// å°ä¼åè¿åç ast æ è½¬åææ§è¡çä»£ç 
generate(
  ast: ASTElement | void,
  options: CompilerOptions
) {
  const code = ast ? genElement(ast) : '_h("div")'
  // ...
  return {
    render: `with(this){return ${code}}`,
    staticRenderFns: currentStaticRenderFns
  }
}
// çæ code
genElement(el: ASTElement) {
  if (el.component) {
    code = genComponent(el.component, el)
  } else {
    const data = el.plain ? undefined : genData(el)

    const children = el.inlineTemplate ? null : genChildren(el)
    code = `_h('${el.tag}'${
      data ? `,${data}` : '' // data
    }${
      children ? `,${children}` : '' // children
    })`
  }
  // module transforms
  for (let i = 0; i < transforms.length; i++) {
    code = transforms[i](el, code)
  }
  return code
}
// çæ data
genData(el: ASTElement) {
  const dirs = genDirectives(el)
  if (dirs) data += dirs + ','
  // ...
  return data
}
// çæ dir
genDirectives (el: ASTElement){}
```

æåç¨ with çæç code ä¼ å¥ render ä¸­æ§è¡ `new Function()`
```js
  const compiled = compile(template, options) 
  res.render = createFunction(compiled.render, fnGenErrors) 
  function createFunction (code, errors) { 
    try { return new Function(code) 
    } catch (err) { 
      errors.push({ err, code })
      return noop 
  } }
```

### æ»ç»
éè¿ä¸é¢å¯¹æºä»£ç çåæäºè§£å°å®æ¯ Vue ååç»å®ççæ­£å®ç°ï¼æ¬è´¨ä¸å°±æ¯ä¸ç§è¯­æ³ç³ï¼å®å³æ¯æåçè¡¨ååç´ ï¼ä¹æ¯æèªå®ä¹ç»ä»¶ï¼æ¬ç« ä¸­åªåæäºè¡¨ååç´ ï¼å¯¹äºèªå®ä¹ç»ä»¶åé¢ä¹å¯ä»¥æ¢æ¢äºè§£ã

<img src="/assets/vue-vmodel.png"></img>