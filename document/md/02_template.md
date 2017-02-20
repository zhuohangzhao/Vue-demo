#一，模板语法

Vue.js 使用了基于 HTML 的模版语法，将 DOM 和 Vue的实例绑定起来。

##1.1 插值
### 文本
数据绑定常见的形式是使用 "Mustache" 语法（双大括号）的文本插值：

    <span> Message: {{ msg }} </span>
   
Mustache 标签里 msg 将会被替代为对应数据对象上 msg 属性的值。绑定的数据对象上 `msg` 属性发生了改变，插值处的内容都会更新。也可以通过 v-once 指令，只能一次执行插值，第二次时候，插值出内容不会更新。

    <span v-once>This will never change: {{ msg }}</span>


### 纯HTML

双大括号会将数据解释为纯文本，而非 HTML 。为了输出真正的 HTML ，你需要使用 v-html 指令：

    <div v-html="rawHtml"></div>
    
### 属性

绑定属性 v-bind 指令，不能使用 Mustache

    <div v-bind:id="dynamicId"></div>

### JavaScript表达式

Vue.js 都提供了完全的 JavaScript 表达式支持。

    {{ number + 1 }}
    {{ ok ? 'YES' : 'NO' }}
    {{ message.split('').reverse().join('') }}
    
    <div v-bind:id="'list-' + id"></div>
    
每个绑定都只能包含单个表达式。

    <!-- 这是语句，不是表达式 -->
    {{ var a = 1 }}
    
    <!-- 流控制也不会生效，请使用三元表达式 -->
    {{ if (ok) { return message } }}
    
##1.2 指令

指令是带有 `v-`前缀的属性。指令除了`v-for`以外都是单一表达式，作用就是当表达式的值改变时将相应的行为应用到 DOM 上。

    <p v-if="seen">Now you see me</p>
    
上面代码，通过 `v-if` 指令将根据表达式 seen 的值的真假来移除/插入 <p> 元素。

### 参数

一些指令能接受参数，在指令冒号后添加。

    <a v-bind:href="url"></a>
    
上面例子，`href` 是参数，告知 `v-bind` 指令将该元素的 `href` 属性与表达式 `url` 的值绑定。

### 修饰符

修饰符（Modifiers）是以半角句号 . 指明的特殊后缀，用于指出一个指定应该以特殊方式绑定。
    
    <form v-on:submit.prevent="onSubmit"></form>
    
上面代码，`.prevent` 修饰符告诉 v-on 指令对于触发的事件调用 `event.preventDefault()`

### 过滤器

Vue 2.x 版本中，过滤器只能在 mustache 和 v-bind 中使用。
    
    {{message | capitalize|}}
    <div v-bind:id="rawId | formatId"></div>
    
    new Vue({
      // ...
      filters: {
        capitalize: function (value) {
          if (!value) return ''
          value = value.toString()
          return value.charAt(0).toUpperCase() + value.slice(1)
        }
      }
    })
    
过滤器可以接受参数：
   
    {{ message | filterA('arg1', arg2) }}
    
### 缩写

    <a v-bind:href="url"></a>
    //等同于缩写
    <a :bind="url"></a>
    
    <a v-on:click="todo"></a>
    //等同于缩写
    <a @click="todo"></a>
    
    