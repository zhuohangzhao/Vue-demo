#一，事件处理器

##1.1 监听事件

可以用 `v-on` 指令监听 DOM 事件来触发一些 JavaScript 代码。
    
    <ul id="example-1">
        <button v-on:click="counter += 1">增加1</button>
        <p>这个按钮被点击了 {{ counter }} 次。</p>
    </ul>
        
    var vm = new Vue({

        el: "#example-1",
        data: {
            counter: 0
        }

    })
            
点击按钮，`counter`就增加 1 。


##1.2 方法事件处理器

v-on 可以接收一个定义的方法来调用。

    <div id="example-2">
      <!-- `greet` 是在下面定义的方法名 -->
      <button v-on:click="greet">Greet</button>
    </div>    
    
    var example2 = new Vue({
        el: '#example-2',
        data: {
            name: 'Vue.js'
        },
        // 在 `methods` 对象中定义方法
        methods: {
            greet: function (event) {
                // `this` 在方法里指当前 Vue 实例
                alert('Hello ' + this.name + '!');
                // `event` 是原生 DOM 事件
                alert(event.target.tagName);
            }
        }
    });
    // 也可以用 JavaScript 直接调用方法
    example2.greet();// -> 'Hello Vue.js!'  
        
##1.3 内联处理器

以用内联 JavaScript 语句
    
    <div id="example-3">
        <button v-on:click="say('hi')">Say hi</button>
        <button v-on:click="say('what')">Say what</button>
    </div>   
    
    var example2 = new Vue({
        el: '#example-3',
        methods: {
            say: function(message) {
                alert(message)
            }
        }
    });

有时也需要在内联语句处理器中访问原生 DOM 事件。可以用特殊变量 `$event` 把它传入方法
    
    <div id="example-3">
        <button v-on:click="warn('Form cannot be submitted yet.', $event)">Submit</button>
    </div>

    var example2 = new Vue({
        el: '#example-3',
        methods: {
            warn: function (message, event) {
                if(event) event.preventDefault();
                alert(message);
            }
        }
    });


##1.4 事件修饰符

在事件处理程序中调用 `event.preventDefault()` 或 `event.stopPropagation()` 是非常常见的需求。尽管我们可以在 methods 中轻松实现这点，但更好的方式是：methods 只有纯粹的数据逻辑，而不是去处理 DOM 事件细节。
为了解决这个问题， Vue.js 为 `v-on` 提供了**事件修饰符**。通过由点(.)表示的指令后缀来调用修饰符


 - `.stop`
 - `.prevent`
 - `.capture`
 - `.self`
 - `.once`
 
        
    <!-- 阻止单击事件冒泡 -->
    <a v-on:click.stop="doThis"></a>

    <!-- 提交事件不再重载页面 -->
    <form v-on:submit.prevent="onSubmit"></form>

    <!-- 修饰符可以串联  -->
    <a v-on:click.stop.prevent="doThat"></a>
    
    <!-- 只有修饰符 -->
    <form v-on:submit.prevent></form>
    
    <!-- 添加事件侦听器时使用事件捕获模式 -->
    <div v-on:click.capture="doThis">...</div>
    
    !-- 只当事件在该元素本身（而不是子元素）触发时触发回调 -->
    <div v-on:click.self="doThat">...</div>
 
    <!-- 点击事件只会执行一次 -->    版本v2.14添加
    <a v-on:click.once="doThis"></a>


##1.5 按键修饰符

在**监听键盘事件**时，我们经常需要监测常见的键值。 Vue 允许为 `v-on` 在监听键盘事件时添加按键修饰符：

    <!-- 只有在 keyCode 是 13 时调用 vm.submit() -->
    <input v-on:keyup.13="submit">
    
    <!-- 同上 -->
    <input v-on:keyup.enter="submit">
    <!-- 缩写语法 -->
    <input @keyup.enter="submit">

全部按键别名：

 - `.enter`
 - `.tab`
 - `.delete`
 - `.esc`
 - `.space`
 - `.up`
 - `.down`
 - `.left`
 - `.right`

可以通过全局 `config.keyCodes` 对象自定义按键修饰符别名：

    // 可以使用 v-on:keyup.f1
    Vue.config.keyCodes.f1 = 112

版本v2.1.0新增 

 - .ctrl
 - .alt
 - .shift
 - .meta
 
 



