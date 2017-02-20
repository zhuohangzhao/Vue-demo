#一，组件

组件（Component）是 Vue.js 最强大的功能之一。组件可以扩展 HTML 元素，封装可重用的代码。在较高层面上，组件是自定义元素， Vue.js 的编译器为它添加特殊功能。在有些情况下，组件也可以是原生 HTML 元素的形式，以 is 特性扩展。

##1.1 使用组件

### 注册

创建一个 Vue实例：

    new Vue({
        el: '#element',
        // ...
    })
    
注册一个全局组件，可以使用 `Vue.component(tagName, options)`

    Vue.component('my-component, {
        // ...
    })
注意：对于自定义标签名，尽量遵守W3C规则。   
组件在注册之后，便可以在父实例（根实例）的模块中以自定义元素 `<my-component></my-component>` 的形式使用。要确保在初始化根实例之前注册了组件：

    <div id="example">
      <my-component></my-component>
    </div>
    
    // 注册
    Vue.component('my-component', {
      template: '<div>A custom component!</div>'
    })
    
    // 创建根实例
    new Vue({
      el: '#example'
    })

渲染后，模板`template`的值替代了自定义元素：

    <div id="example">
      <div>A custom component!</div>
    </div>
    
###局部注册
    
不必在全局注册每个组件。通过使用组件实例选项注册，可以使组件仅在另一个实例/组件的作用域中可用
    
    <div id="example">
      <my-component></my-component>
    </div>    
    
    var Child = {
        template: "<div>A custom component!</div>"
    };

    new Vue({
        el:'#example',
        components: {
            //<my-component> 只能父模板可用
            "my-component": Child
        }
    })

    
### DOM 模板解析说明
    
当使用 DOM 作为模版时（例如，将 el 选项挂载到一个已存在的元素上）, 你会受到 HTML 的一些限制，因为 **Vue 只有在浏览器解析和标准化 HTML 后才能获取模版内容**。尤其像这些元素 `<ul>` ， `<ol>`， `<table>` ， `<select>` 限制了能被它包裹的元素， `<option>` 只能出现在其它元素内部。
    
    <table id="example">
      <my-component></my-component>
    </table>        
    
    
    var Child = {
        template: "<div>A custom component!</div>"
    };

    new Vue({
        el:'#example',
        components: {
            //<my-component> 只能父模板可用
            "my-component": Child
        }
    })
    
自定义组件 `<my-component>` 被认为是无效的内容，因此在渲染的时候会导致错误。变通的方案是使用特殊的 is 属性    
    
        <table id="example">
            <tr is="my-component"></tr>
        </table>
    
这样就可以正常渲染了。   
 
注意，如果您使用来自以下来源之一的字符串模板，这些限制将不适用：
   - `<script type="text/x-template">`
   - JavaScript 内联模板字符串
   - `.Vue` 组件

因此，有必要的话请使用字符串模版。
    
### `data` 必须是函数    
    
使用组件时，大多数可以传入到 Vue 构造器中的选项可以在注册组件时使用，有一个例外： data 必须是函数。 实际上，如果你这么做：

    Vue.component('my-component', {
       template: '<span>{{ message }}</span>',
       data: {
         message: 'hello'
       }
     })
     
那么 Vue 会在控制台发出警告，告诉你在组件中 `data` 必须是一个函数。
 
     <div id="example-2">
       <simple-counter></simple-counter>
       <simple-counter></simple-counter>
       <simple-counter></simple-counter>
     </div>
     
     var data = { counter: 0 }
     Vue.component('simple-counter', {
       template: '<button v-on:click="counter += 1">{{ counter }}</button>',
       // data 是一个函数，因此 Vue 不会警告，
       // 但是我们为每一个组件返回了同一个对象引用
       data: function () {
         return data
       }
     })
     new Vue({
       el: '#example-2'
     })

     
由于这三个组件共享了同一个 data ， 因此增加一个 counter 会影响所有组件！我们可以通过为每个组件返回新的 data 对象来解决这个问题：
 
 
     data: function () {
       return {
         counter: 0
       }
     }
     
     
现在每个 counter 都有它自己内部的状态了：   
  
 
### 构成组件

组件意味着协同工作，通常父子组件会是这样的关系：组件 A 在它的模版中使用了组件 B 。它们之间必然需要相互通信：父组件要给子组件传递数据，子组件需要将它内部发生的事情告知给父组件。
    
在 Vue.js 中，父子组件的关系可以总结为 props down, events up 。**父组件通过 props 向下传递数据给子组件，子组件通过 events 给父组件发送消息**

    
##1.2 Prop
    
### 使用 Prop 传递数据

组件实例的作用域是孤立的，所以不能在子组件的模板内直接引用父组件的数据。可以使用 props 把数据传给子组件。
**prop 是父组件用来传递数据的一个自定义属性**。子组件需要显式地用 `props` 选项声明 “prop”
    
    Vue.component('child', {
        // 声明 props
        props: ['message'],      
        // 就像 data 一样，prop 可以用在模板内
        // 同样也可以在 vm 实例中像 “this.message” 这样使用
        template: '<span>{{ message }}</span>'
    });

    new Vue({
        el:'#example'
    })    

然后向它(下面是message)传入一个普通字符串：
    
    <div id="example">
        <child message="hello!"></child>
    </div>    
    
### camelCase vs .kebab-case
    
HTML 特性不区分大小写。当使用非字符串模版时，prop的名字形式会从 camelCase 转为 kebab-case（短横线隔开）
       
    <!-- html中用短横线 -->
        <div id="example">
            <child my-message="hello!"></child>
        </div>
    
        Vue.component('child', {
          // 在js用驼峰表示
          props: ['myMessage'],
          template: '<span>{{ myMessage }}</span>'
        })
        
        new Vue({
             el:'#example'
        })
        
如果使用字符串模板，没有这个限制。
    
### 动态 Prop

**用 `v-bind` 动态绑定 props 的值到父组件的数据中**。每当父组件的数据变化时，该变化也会传导给子组件：

    <div id="example">
        <div>
            <input v-model="parentMsg">
            <br>
            <child v-bind:my-message="parentMsg"></child>
        </div>
    </div>

    Vue.component('child', {
        props: ['myMessage'],
        template: '<span>{{ myMessage }}</span>'
    });

    new Vue({
        el:'#example',
        data: {
            parentMsg:''
        }
    })

使用 v-bind 的缩写语法通常更简单：

    <child :my-message="parentMsg"></child>


### 字面量语法 vs 动态语法

    <!-- 传递了一个字符串"1" -->
    <comp some-prop="1"></comp>
    
因为它是一个字面 prop ，它的值以字符串 "1" 而不是以实际的数字传下去。如果想传递一个实际的 JavaScript 数字，需要使用 `v-bind` ，从而让它的值被当作 JavaScript 表达式计算

    <!-- 传递实际的数字 -->
    <comp v-bind:some-prop="1"></comp>
    
    

### 单向数据流

prop 是单向绑定的：当父组件的属性变化时，将传导给子组件，但是不会反过来。这是为了防止子组件无意修改了父组件的状态——这会让应用的数据流难以理解。每次父组件更新时，子组件的所有 prop 都会更新为最新值。这意味着你**不应该**在子组件内部改变 prop 。如果你这么做了，Vue 会在控制台给出警告。

两种改变 prop 的情况：

 - 定义一个局部 data 属性，并将 prop 的初始值作为局部数据的初始值。
 
        
        props: ['initialCounter'],
        data: function () {
            return { counter: this.initialCounter }
        }
        
 - 定义一个 computed 属性，此属性从 prop 的值计算得出。
 
        
        props: ['size'],
        computed: {
          normalizedSize: function () {
            return this.size.trim().toLowerCase()
          }
        }
        
        
### Prop 验证        
        
组件可以为 props 指定验证要求。如果未指定验证要求，Vue 会发出警告。当组件给其他人使用时这很有用。
        
prop 是一个对象而不是字符串数组时，它包含验证要求：
        
        Vue.component('example', {
            props: {
                // 基础类型检测 （`null` 意思是任何类型都可以）
                propA: Number,
                // 多种类型
                propB: [String, Number],
                // 必传且是字符串
                propC: {
                    type: String,
                    required: true
                },
                // 数字，有默认值
                propD: {
                    type: Number,
                    default: 100
                },
                // 数组／对象的默认值应当由一个工厂函数返回
                propE: {
                    type: Object,
                    default: function () {
                        return { message: 'hello' }
                    }
                },
                // 自定义验证函数
                propF: {
                    validator: function (value) {
                        return value > 10
                    }
                }
            }
        })
                
type 可以是下面原生构造器：
 - String
 - Number
 - Boolean
 - Function
 - Object
 - Array
 
type 也可以是一个自定义构造器，使用 `instanceof` 检测。当 prop 验证失败了，如果使用的是开发版本会抛出一条警告。 
        

##1.3 自定义事件

父组件是使用 props 传递数据给子组件，但如果子组件要把数据传递回去，应该怎样做？那就是自定义事件！

### 使用 `v-on` 绑定自定义事件

每个 Vue 实例都实现了事件接口（Event interface），即：

 - 使用 `$on(eventName)` 监听事件
 - 使用 `$emit(eventName)` 触发事件
 
父组件可以在使用子组件的地方直接用 v-on 来监听子组件触发的事件。
 
         <div id="counter-event-example">
             <p>{{ total }}</p>
             
             <button-counter v-on:eventa="incrementTotal"></button-counter>  
             <button-counter v-on:eventa="incrementTotal"></button-counter>
         </div>
 
         Vue.component('button-counter', {
             template: '<button v-on:click="increment">{{ counter }}</button>',
             data: function () {
                 return {
                     counter: 0
                 }
             },
             methods: {
                 increment: function () {
                     this.counter += 1;
                     this.$emit('eventa');
                 }
             }
         });
         new Vue({
             el: '#counter-event-example',
             data: {
                 total: 0
             },
             methods: {
                 incrementTotal: function () {
                     this.total += 1
                 }
             }
         })
        
        
在本例中，子组件已经和它外部完全解耦了。它所做的只是触发一个父组件关心的内部事件。        
有时候，你可能想在某个组件的根元素上监听一个原生事件。可以使用 `.native` 修饰 `v-on` 。例如：
    
    <my-component v-on:click.native="doTheThing"></my-component>        
        
        
### 使用自定义事件的表单输入组件
        
自定义事件也可以用来创建自定义的表单输入组件，使用 `v-model` 来进行数据双向绑定：

### 非父子组件通信
    
有时候非父子关系的组件也需要通信。在简单的场景下，使用一个空的 Vue 实例作为中央事件总线：

        var bus = new Vue();
    
        // 触发组件 A 中的事件
        bus.$emit('id-selected',1);
    
        // 在组件 B 创建的钩子中监听事件
        bus.$on('id-selected', function (id) {
            // ...
        })

##1.4 使用 Slot 分发内容

在使用组件时，常常要像这样组合它们：

    <app>
      <app-header></app-header>
      <app-footer></app-footer>
    </app>
    
 - `<app>` 组件不知道它的挂载点会有什么内容。挂载点的内容是由<app>的父组件决定的。
 - `<app>` 组件很可能有它自己的模版。


为了让组件可以组合，我们需要一种方式来混合父组件的内容与子组件自己的模板。这个过程被称为 **内容分发**


### 编译作用域

在深入内容分发 API 之前，我们先明确内容的编译作用域。假定模板为：
    
    <child-component>
      {{ message }}
    </child-component>

`message` 应该绑定到父组件的数据，还是绑定到子组件的数据？答案是父组件。组件作用域简单地说是：
父组件模板的内容在父组件作用域内编译；子组件模板的内容在子组件作用域内编译。
一个常见错误是试图在父组件模板内将一个指令绑定到子组件的属性/方法：

    <!-- 无效 -->
    <child-component v-show="someChildProperty"></child-component>

假定 someChildProperty 是子组件的属性，上例不会如预期那样工作。父组件模板不应该知道子组件的状态。
如果要绑定子组件内的指令到一个组件的根节点，应当在它的模板内这么做：
    
    Vue.component('child-component', {
      // 有效，因为是在正确的作用域内
      template: '<div v-show="someChildProperty">Child</div>',
      data: function () {
        return {
          someChildProperty: true
        }
      }
    })
类似地，分发内容是在父组件作用域内编译。

### 单个 Slot

除非子组件模板包含至少一个 `<slot>` 插口，否则父组件的内容将会被丢弃。当子组件模板只有一个没有属性的 `slot` 时，父组件整个内容片段将插入到 slot 所在的 DOM 位置，并替换掉 slot 标签本身。
最初在 `<slot>` 标签中的任何内容都被视为**备用内容**。备用内容在子组件的作用域内编译，并且只有在宿主元素为空，且没有要插入的内容时才显示备用内容。

假定 my-component 组件有下面模板：

    <div>
      <h2>我是子组件的标题</h2>
      <slot>
        只有在没有要分发的内容时才会显示。
      </slot>
    </div>
    
父组件模版：

    <div>
      <h1>我是父组件的标题</h1>
      <my-component>
        <p>这是一些初始内容</p>
        <p>这是更多的初始内容</p>
      </my-component>
    </div>
    
渲染结果：

    <div>
      <h1>我是父组件的标题</h1>
      <div>
        <h2>我是子组件的标题</h2>
        <p>这是一些初始内容</p>
        <p>这是更多的初始内容</p>
      </div>
    </div>

### 具名 Slot

`<slot>` 元素可以用一个特殊的属性 `name` 来配置如何分发内容。多个 `slot` 可以有不同的名字。具名 `slot` 将匹配内容片段中有对应 `slot` 特性的元素。

仍然可以有一个匿名 slot ，它是默认 slot ，作为找不到匹配的内容片段的备用插槽。如果没有默认的 slot ，这些找不到匹配的内容片段将被抛弃。

例如，假定我们有一个 `app-layout` 组件，它的模板为：

    <div class="container">
      <header>
        <slot name="header"></slot>
      </header>
      <main>
        <slot></slot>
      </main>
      <footer>
        <slot name="footer"></slot>
      </footer>
    </div>

父组件模版：

    <app-layout>
      <h1 slot="header">这里可能是一个页面标题</h1>
      <p>主要内容的一个段落。</p>
      <p>另一个主要段落。</p>
      <p slot="footer">这里有一些联系信息</p>
    </app-layout>


### 作用插槽

作用域插槽是一种特殊类型的插槽，用作使用一个（能够传递数据到）可重用模板替换已渲染元素。
在子组件中，只需将数据传递到插槽，就像你将 prop 传递给组件一样：

    <div class="child">
      <slot text="hello from child"></slot>
    </div>
    
在父级中，具有特殊属性 scope 的 <template> 元素，表示它是作用域插槽的模板。scope 的值对应一个临时变量名，此变量接收从子组件中传递的 prop 对象：
    
    <div class="parent">
      <child>
        <template scope="props">
          <span>hello from parent</span>
          <span>{{ props.text }}</span>
        </template>
      </child>
    </div>
作用域插槽更具代表性的用例是列表组件，允许组件自定义应该如何渲染列表每一项：
    
    <my-awesome-list :items="items">
      <!-- 作用域插槽也可以在这里命名 -->
      <template slot="item" scope="props">
        <li class="my-fancy-item">{{ props.text }}</li>
      </template>
    </my-awesome-list>
列表组件的模板：

    <ul>
      <slot name="item"
        v-for="item in items"
        :text="item.text">
        <!-- fallback content here -->
      </slot>
    </ul>     

##1.5 动态组件

多个组件可以使用同一个挂载点，然后动态地在它们之间切换。使用保留的 <component> 元素，动态地绑定到它的 is 特性：

    var vm = new Vue({
      el: '#example',
      data: {
        currentView: 'home'
      },
      components: {
        home: { /* ... */ },
        posts: { /* ... */ },
        archive: { /* ... */ }
      }
    })
    
    
    <component v-bind:is="currentView">
      <!-- 组件在 vm.currentview 变化时改变！ -->
    </component>
也可以直接绑定到组件对象上：


    var Home = {
      template: '<p>Welcome home!</p>'
    }
    var vm = new Vue({
      el: '#example',
      data: {
        currentView: Home
      }
    })

### keep-alive

如果把切换出去的组件保留在内存中，可以保留它的状态或避免重新渲染。为此可以添加一个 keep-alive 指令参数：
    
    <keep-alive>
      <component :is="currentView">
        <!-- 非活动组件将被缓存！ -->
      </component>
    </keep-alive>


## 1.6 杂项

### 编写可复用组件

在编写组件时，记住是否要复用组件有好处。一次性组件跟其它组件紧密耦合没关系，但是可复用组件应当定义一个清晰的公开接口。

Vue 组件的 API 来自三部分 - props, events 和 slots ：

- Props 允许外部环境传递数据给组件
- Events 允许组件触发外部环境的副作用
- Slots 允许外部环境将额外的内容组合在组件中。

使用 v-bind 和 v-on 的简写语法，模板的缩进清楚且简洁：
    
    <my-component
      :foo="baz"
      :bar="qux"
      @event-a="doThis"
      @event-b="doThat"
    >
      <img slot="icon" src="...">
      <p slot="main-text">Hello!</p>
    </my-component>

### 子组件索引

尽管有 props 和 events ，但是有时仍然需要在 JavaScript 中直接访问子组件。为此可以使用 ref 为子组件指定一个索引 ID 。例如：

    <div id="parent">
      <user-profile ref="profile"></user-profile>
    </div>
    
    var parent = new Vue({ el: '#parent' })
    // 访问子组件
    var child = parent.$refs.profile

当 ref 和 v-for 一起使用时， ref 是一个数组或对象，包含相应的子组件。

### 异步组件

在大型应用中，我们可能需要将应用拆分为多个小模块，按需从服务器下载。为了让事情更简单， Vue.js 允许将组件定义为一个工厂函数，动态地解析组件的定义。Vue.js 只在组件需要渲染时触发工厂函数，并且把结果缓存起来，用于后面的再次渲染。例如：

    Vue.component('async-example', function (resolve, reject) {
      setTimeout(function () {
        // Pass the component definition to the resolve callback
        resolve({
          template: '<div>I am async!</div>'
        })
      }, 1000)
    })
工厂函数接收一个 resolve 回调，在收到从服务器下载的组件定义时调用。也可以调用 reject(reason) 指示加载失败。这里 setTimeout 只是为了演示。怎么获取组件完全由你决定

推荐配合使用 ：Webpack 的代码分割功能：

    Vue.component('async-webpack-example', function (resolve) {
      // 这个特殊的 require 语法告诉 webpack
      // 自动将编译后的代码分割成不同的块，
      // 这些块将通过 Ajax 请求自动下载。
      require(['./my-async-component'], resolve)
    })
你可以使用 Webpack 2 + ES2015 的语法返回一个 Promise resolve 函数：
    
    Vue.component(
      'async-webpack-example',
      () => System.import('./my-async-component')
    )

### 组件命名约定

当注册组件（或者 props）时，可以使用 kebab-case ，camelCase ，或 TitleCase 。Vue 不关心这个。
    
    // 在组件定义中
    components: {
      // 使用 kebab-case 形式注册
      'kebab-cased-component': { /* ... */ },
      // register using camelCase
      'camelCasedComponent': { /* ... */ },
      // register using TitleCase
      'TitleCasedComponent': { /* ... */ }
    }
在 HTML 模版中，请使用 kebab-case 形式：
    
    <!-- 在HTML模版中始终使用 kebab-case -->
    <kebab-cased-component></kebab-cased-component>
    <camel-cased-component></camel-cased-component>
    <title-cased-component></title-cased-component>
    
当使用字符串模式时，可以不受 HTML 的 case-insensitive 限制。这意味实际上在模版中，你可以使用 camelCase 、 TitleCase 或者 kebab-case 来引用：
    
    <!-- 在字符串模版中可以用任何你喜欢的方式! -->
    <my-component></my-component>
    <myComponent></myComponent>
    <MyComponent></MyComponent>
    
如果组件未经 slot 元素传递内容，你甚至可以在组件名后使用 / 使其自闭合：

    <my-component/>
    
当然，这只在字符串模版中有效。因为自闭的自定义元素是无效的 HTML ，浏览器原生的解析器也无法识别它。


### 递归组件

组件在它的模板内可以递归地调用自己，不过，只有当它有 name 选项时才可以：
    
    name: 'unique-name-of-my-component'

当你利用Vue.component全局注册了一个组件, 全局的ID作为组件的 name 选项，被自动设置.

    Vue.component('unique-name-of-my-component', {
      // ...
    })

如果你不谨慎, 递归组件可能导致死循环:

    name: 'stack-overflow',
    template: '<div><stack-overflow></stack-overflow></div>'

上面组件会导致一个错误 “max stack size exceeded” ，所以要确保递归调用有终止条件 (比如递归调用时使用 v-if 并让他最终返回 false )。


### 内联模板

如果子组件有 inline-template 特性，组件将把它的内容当作它的模板，而不是把它当作分发内容。这让模板更灵活。

    <my-component inline-template>
      <div>
        <p>These are compiled as the component's own template.</p>
        <p>Not parent's transclusion content.</p>
      </div>
    </my-component>
    
### X-Templates
另一种定义模版的方式是在 JavaScript 标签里使用 text/x-template 类型，并且指定一个id。例如：

    <script type="text/x-template" id="hello-world-template">
      <p>Hello hello hello</p>
    </script>
    Vue.component('hello-world', {
      template: '#hello-world-template'
    })
    
### 使用v-once 的低级静态组件

尽管在 Vue 中渲染 HTML 很快，不过当组件中包含大量静态内容时，可以考虑使用 v-once 将渲染结果缓存起来，就像这样：
    
    Vue.component('terms-of-service', {
      template: '\
        <div v-once>\
          <h1>Terms of Service</h1>\
          ... a lot of static content ...\
        </div>\
      '
    })